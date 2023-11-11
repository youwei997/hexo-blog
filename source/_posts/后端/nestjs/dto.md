---
title: nestjs dto (Data Transfer Object) 数据传输对象
tags: nestjs
categories: 后端
date: 2023-11-11 14:37:38
---
<meta name="referrer" content="no-referrer"/>

-   对接口的参数进行约束
-   dto 是做表示层（展示给用户）的，而实体是数据对象（表）
-   dto 是面向对象的，实体是面向数据库的。
-   数据传输对象，这个对象封装你需要传输的数据 在 M，V，C 这三个层传递

```ts
// /src/user/dto/create-user.dto.ts

import { ApiProperty } from "@nestjs/swagger";

// 使用swagger和ts配合使用，对参数进行约束及说明
export class CreateUserDto {
    // 对属性在swagger里进行说明
    @ApiProperty({ description: "邮箱" })
    // 配置只读属性，在接口使用时，不允许修改
    readonly email: string; // 只读
    // 配置默认值
    @ApiProperty({ description: "密码", default: "123456" })
    password: string;
    @ApiProperty({ description: "用户名", default: "用户名" })
    username: string;
}
```

```ts
// /src/user/user.controller.ts

import { CreateUserDto } from "./dto/create-user.dto";
@Controller("user")
export class UserController {
    constructor(private readonly userService: UserService) {}

    @Post()
    // @Body()装饰器 将从客户端获取到的参数赋给 createUserDto
    // CreateUserDto 用于约束 createUserDto里的属性值
    create(@Body() createUserDto: CreateUserDto) {
        return this.userService.create(createUserDto);
    }
}
```

## 可选更新

-   在构建输入验证类型（也称为 DTO）时，在同一类型上构建创建和更新变体通常很有用。例如，创建变体可能需要所有字段，而更新变体可能使所有字段都是可选的。

-   Nest 提供了 PartialType()实用功能来简化此任务并最大限度地减少样板文件。
-   该 PartialType()函数返回一个类型（类），其中输入类型的所有属性都设置为可选

-   `CreateUserDto` 里属性都是必选的，使用 `PartialType()`函数设置为可选

```ts
// update-user.dto.ts

import { PartialType } from "@nestjs/mapped-types";
import { CreateUserDto } from "./create-user.dto";

export class UpdateUserDto extends PartialType(CreateUserDto) {}
```