---
title: nestjs swagger
date: 2023-11-11 14:25:37
tags: nestjs
categories: 后端
---
<meta name="referrer" content="no-referrer"/>

## main.ts 配置 swagger

```ts
// main.ts
import { NestFactory } from "@nestjs/core";
import { SwaggerModule, DocumentBuilder } from "@nestjs/swagger";
import { AppModule } from "./app.module";

async function bootstrap() {
    // 创建nest应用 （引入根模块）
    const app = await NestFactory.create(AppModule);
    // 创建swagger接口文档及接口测试
    const options = new DocumentBuilder()
        .setTitle("接口文档标题")
        .setDescription("描述")
        .setVersion("1.0")
        .build();
    const document = SwaggerModule.createDocument(app, options);
    SwaggerModule.setup("docs", app, document);

    await app.listen(3000).then(() => {
        console.log("http://localhost:3000");
    });
}
bootstrap();
```

## 设置 swagger 元素

```ts
import { ApiTags, ApiOperation } from "@nestjs/swagger";
// 设置 controller 标签 ApiTags
@ApiTags("user")
@Controller("user")
export class UserController {
    // 设置接口属性 ApiOperation
    // ApiOperation 传入一个对象
    @ApiOperation({
        summary: "添加用户", // 描述
    })
    @Post()
    create(@Body() createUserDto: CreateUserDto) {
        return this.userService.create(createUserDto);
    }
}
```

## 具体使用

-   controller

```ts
import {
    Controller,
    Get,
    Post,
    Body,
    Patch,
    Param,
    Delete,
    Query,
    Headers,
} from "@nestjs/common";
import {
    ApiQuery,
    ApiTags,
    ApiOperation,
    ApiResponse,
    ApiBody,
    ApiParam,
} from "@nestjs/swagger";
import { UserService } from "./user.service";
import { CreateUserDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";
import { User } from "./entities/user.entity";

@ApiTags("User")
@Controller("user")
export class UserController {
    constructor(private readonly userService: UserService) {}

    @Get()
    @ApiQuery({ name: "id", required: false, description: "用户id" })
    @ApiOperation({ summary: "根据Query查询" })
    @ApiResponse({
        status: 200,
        description: "返回用户信息",
        type: User,
    })
    findByQuery(@Query("id") id: string, @Headers() { authorization }) {
        return this.userService.findByQuery(+id);
    }

    @ApiBody({ description: "填写创建内容" })
    @Post()
    create(@Body() createUserDto: CreateUserDto) {
        return this.userService.create(createUserDto);
    }

    @ApiOperation({ summary: "查询所有用户" })
    @Get("all")
    findAll() {
        return this.userService.findAll();
    }

    // 下面几个必须包含id的方法，可以不加 @ApiParam，因为swagger默认加上了

    // @ApiParam({ name: 'id' })
    @Get(":id")
    findOne(@Param("id") id: string) {
        return this.userService.findOne(+id);
    }

    // @ApiParam({ name: 'id' })
    @ApiBody({ description: "填写更新内容" })
    @Patch(":id")
    update(@Param("id") id: string, @Body() updateUserDto: UpdateUserDto) {
        return this.userService.update(+id, updateUserDto);
    }

    // @ApiParam({ name: 'id' })
    @Delete(":id")
    remove(@Param("id") id: string) {
        return this.userService.remove(+id);
    }
}
```

-   entity

```ts
import { ApiProperty } from "@nestjs/swagger";

export class User {
    @ApiProperty({ example: "张三", description: "名字" })
    name: string;

    @ApiProperty({ example: 1, description: "年龄" })
    age: number;

    @ApiProperty({
        example: "品种",
        description: "白人",
    })
    breed: string;
}
```