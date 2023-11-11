---
title: nestjs guards守卫
date: 2023-11-11 14:49:53
tags: nestjs
categories: 后端
---

说明（废话）：警卫有一个单一的责任。它们根据运行时存在的某些条件（如权限、角色、ACL 等）确定给定请求是否将由路由处理程序处理。这通常称为授权。授权（及其通常与之协作的表亲身份验证）通常由传统 Express 应用程序中的中间件处理。中间件是身份验证的最佳选择，因为诸如令牌验证和将属性附加到 request 对象之类的事情与特定的路由上下文（及其元数据）没有紧密的联系。

## 身份验证

### guard 用于检验

创建 `roles.guard.ts`

```ts
import { Injectable, CanActivate, ExecutionContext } from "@nestjs/common";
import { Reflector } from "@nestjs/core";

@Injectable()
export class RolesGuard implements CanActivate {
    constructor(private reflector: Reflector) {}

    canActivate(context: ExecutionContext): boolean {
        // 从元数据获取身份数组
        const roles = this.reflector.get<string[]>(
            "roles",
            context.getHandler()
        );

        // 如果没有设置访问权限，直接允许下一步
        if (!roles) {
            return true;
        }
        const request = context.switchToHttp().getRequest();

        // 从query取出前端传输的身份
        const { user } = request.query;
        // 判断前端传输的身份，在允许访问的数组中是否有保护传入的身份
        // console.log(!!roles.find((role) => role === user));
        // console.log(roles.includes(user));
        return roles.includes(user);
    }
}
```

### 创建 decorator，用于设置元数据

创建 `roles.decorator.ts`

```ts
import { SetMetadata } from "@nestjs/common";

// 使用装饰器设置哪些身份允许使用接口
// 把允许的身份数组设置到元数据中
export const Roles = (...args: string[]) => {
    return SetMetadata("roles", args);
};
```

### 在 controller 使用

创建 `role-guard.controller.ts`

```ts
import { Controller, Query, Get, UseGuards } from "@nestjs/common";
import { RoleGuardService } from "./role-guard.service";
import { RolesGuard } from "../../common/guards/roles.guard";
import { ApiTags } from "@nestjs/swagger";
import { Roles } from "../../common/decorators/roles.decorator";

@ApiTags("role-guard")
// 守卫：检验身份的装饰器
@UseGuards(RolesGuard)
@Controller("role-guard")
export class RoleGuardController {
    constructor(private readonly roleGuardService: RoleGuardService) {}

    @Get()
    // 设置权限，哪些身份允许访问接口
    @Roles("admin")
    get(@Query() { id }) {
        return this.roleGuardService.findOne(+id);
    }
}
```