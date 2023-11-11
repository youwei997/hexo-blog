---
title: Providers依赖注入
date: 2023-11-11 14:26:27
tags: nestjs
categories: 后端
---
<meta name="referrer" content="no-referrer"/>

-   所有使用 `@Injectable()` ，装饰的类，想使用都必须在`module.ts`，里注册。
    -   `@Module({providers: [AuthService, JwtStrategy, LocalStrategy],})`
-   废话：提供者是 Nest 的一个基本概念。许多基本的 Nest 类可以被视为提供者——服务、存储库、工厂、助手等等。提供者的主要思想是它可以作为依赖注入；这意味着对象之间可以创建各种关系，并且“连接”对象实例的功能在很大程度上可以委托给 Nest 运行时系统。

## 创建一个提供者(provider) 一般是 Services 文件

```ts
// cats.service.ts
import { Injectable } from "@nestjs/common";
import { Cat } from "./interfaces/cat.interface";

@Injectable()
export class CatsService {
    private readonly cats: Cat[] = [];

    create(cat: Cat) {
        this.cats.push(cat);
    }

    findAll(): Cat[] {
        return this.cats;
    }
}
```

## 在模块中注册

```ts
// app.module.ts
import { Module } from "@nestjs/common";
import { CatsController } from "./cats/cats.controller";
import { CatsService } from "./cats/cats.service";

@Module({
    controllers: [CatsController],
    providers: [CatsService],
})
export class AppModule {}
```

## 在 controller 里使用

-   创建一个 interface(非必须)

```ts
// interfaces / cat.interface.ts;
export interface Cat {
    name: string;
    age: number;
    breed: string;
}
```

-   使用

```ts
import { Controller, Get, Post, Body } from "@nestjs/common";
import { CreateCatDto } from "./dto/create-cat.dto";
import { CatsService } from "./cats.service";
import { Cat } from "./interfaces/cat.interface";

@Controller("cats")
export class CatsController {
    constructor(private catsService: CatsService) {}

    @Post()
    async create(@Body() createCatDto: CreateCatDto) {
        this.catsService.create(createCatDto);
    }

    @Get()
    async findAll(): Promise<Cat[]> {
        return this.catsService.findAll();
    }
}
```

## 注

```ts
constructor(private catsService: CatsService) {}
```

`CatsService` 是通过类构造函数注入的。注意 `private` 语法的使用。这种简写方式允许我们 `catsService` 在同一位置立即声明和初始化成员。