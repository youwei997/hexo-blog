---
title: nestjs使用jwt
tags: nestjs
categories: 后端
date: 2023-11-11 14:30:03
---
<meta name="referrer" content="no-referrer"/>

## [nestjs jwt 验证文档](https://docs.nestjs.com/security/authentication#authentication)

## 安装相关依赖

```
npm install --save @nestjs/passport passport passport-local
npm install --save-dev @types/passport-local

npm install --save @nestjs/jwt passport-jwt
npm install --save-dev @types/passport-jwt
```

## 创建 auth 模块

-   /modules/auth
-   登录验证相关代码

## 创建 jwt 密钥

/modules/auth/constants.ts

```ts
export const jwtConstants = {
    secret: "secretKey",
};
```

## 创建`local.strategy.ts`，用于无需 token 验证

```ts
import { Strategy } from "passport-local";
import { PassportStrategy } from "@nestjs/passport";
import { Injectable, UnauthorizedException } from "@nestjs/common";
import { AuthService } from "./auth.service";

@Injectable()
export class LocalStrategy extends PassportStrategy(Strategy) {
    constructor(private authService: AuthService) {
        super();
    }

    async validate(username: string, password: string): Promise<any> {
        const user = await this.authService.validateUser(username, password);
        if (!user) {
            throw new UnauthorizedException();
        }
        return user;
    }
}
```

## 创建`jwt.strategy.ts`，用于需要 token 校验

-   jwtFromRequest: 提供从 Request. 我们将使用在 API 请求的 Authorization 标头中提供不记名令牌的标准方法。此处描述了其他选项。
-   ignoreExpiration：为了明确起见，我们选择默认 false 设置，它将确保 JWT 未过期的责任委托给 Passport 模块。这意味着如果我们的路由提供了过期的 JWT，请求将被拒绝并 401 Unauthorized 发送响应。Passport 可以方便地自动为我们处理这个问题。
-   secretOrKey：我们正在使用权宜之计选项，即提供对称密钥来签署令牌。其他选项，例如 PEM 编码的公钥，可能更适合生产应用程序（请参阅此处了解更多信息）。无论如何，如前所述，不要公开揭露这个秘密。

```ts
import { ExtractJwt, Strategy } from "passport-jwt";
import { PassportStrategy } from "@nestjs/passport";
import { Injectable } from "@nestjs/common";
import { jwtConstants } from "./constants";

@Injectable()
export class JwtStrategy extends PassportStrategy(Strategy) {
    constructor() {
        super({
            jwtFromRequest: ExtractJwt.fromAuthHeaderAsBearerToken(),
            ignoreExpiration: false,
            secretOrKey: jwtConstants.secret,
        });
    }

    // 解密token，获取token里的数据
    async validate(payload: any) {
        return payload;
    }
}
```

## 创建身份守卫

-   用于指定路由是 local 还是 jwt
-   jwt 需要 token 验证
-   local 无需验证

### `jwt-auth.guard.ts` ，jwt 验证守卫

```ts
import { Injectable } from "@nestjs/common";
import { AuthGuard } from "@nestjs/passport";

@Injectable()
export class JwtAuthGuard extends AuthGuard("jwt") {}
```

### `local-auth.guard.ts`，本地守卫

```ts
import { Injectable } from "@nestjs/common";
import { AuthGuard } from "@nestjs/passport";

@Injectable()
export class LocalAuthGuard extends AuthGuard("local") {}
```

### 使用,具体看`auth.controller.ts`

```ts
export class AuthController {
    constructor(private authService: AuthService) {}

    // 不使用守卫文件，使用自带的守卫
    @UseGuards(AuthGuard("local"))
    // @UseGuards(LocalAuthGuard)
    @Post("login")
    async login(@Request() req) {
        return this.authService.login(req.user);
    }

    //使用守卫文件
    @UseGuards(JwtAuthGuard)
    @Get("profile")
    getProfile(@Query() req) {
        return req;
    }
}
```

## 创建 service，`auth.service.ts`

```ts
import { Injectable } from "@nestjs/common";
import { JwtService } from "@nestjs/jwt";

@Injectable()
export class AuthService {
    constructor(private jwtService: JwtService) {}

    async validateUser(username: string, pass: string): Promise<any> {
        const user = { name: "name", password: "123" };
        if (user && user.password === pass) {
            const { password, ...result } = user;
            return result;
        }
        return null;
    }

    async login(user: any) {
        const payload = { username: user.username, sub: user.userId };
        return {
            access_token: this.jwtService.sign(payload),
        };
    }
}
```

## 创建 controller，`auth.controller.ts`

```ts
import {
    Controller,
    Get,
    Request,
    Post,
    UseGuards,
    Query,
} from "@nestjs/common";
import { AuthGuard } from "@nestjs/passport";
import { JwtAuthGuard } from "./jwt-auth.guard";
import { LocalAuthGuard } from "./local-auth.guard";
import { AuthService } from "./auth.service";

@Controller("auth")
export class AuthController {
    constructor(private authService: AuthService) {}

    // 不校验
    @UseGuards(AuthGuard("local"))
    // @UseGuards(LocalAuthGuard)
    @Post("login")
    async login(@Request() req) {
        return this.authService.login(req.user);
    }

    //需token
    @UseGuards(JwtAuthGuard)
    @Get("profile")
    getProfile(@Query() req) {
        return req;
    }
}
```

## 创建 module，`auth.module.ts`

```ts
import { Module } from "@nestjs/common";
import { PassportModule } from "@nestjs/passport";
import { JwtModule } from "@nestjs/jwt";
import { AuthService } from "./auth.service";
import { AuthController } from "./auth.controller";
import { jwtConstants } from "./constants";
import { JwtStrategy } from "./jwt.strategy";
import { LocalStrategy } from "./local.strategy";

@Module({
    imports: [
        PassportModule,
        // 注册
        JwtModule.register({
            // jwt密钥
            secret: jwtConstants.secret,
            // token过期时间
            signOptions: { expiresIn: "360s" },
        }),
    ],
    // 重要！！！
    // JwtStrategy, LocalStrategy 这两个2文件必须引入
    providers: [AuthService, JwtStrategy, LocalStrategy],
    exports: [AuthService],
    controllers: [AuthController],
})
export class AuthModule {}
```

## 在`app.module.ts`，中引入

```ts
import { Module } from "@nestjs/common";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { AuthModule } from "./modules/auth/auth.module";

@Module({
    imports: [AuthModule],
    controllers: [AppController],
    providers: [AppService],
})
export class AppModule {}
```