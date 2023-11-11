---
title: nestjs dto和entities
tags: 
  - nestjs
categories: 后端
date: 2023-11-11 14:34:27
---
<meta name="referrer" content="no-referrer"/>

-   dto 是面对前端的，和前端打交道，检验前端传递的数据
-   entities 是面对数据库的，和数据库打交道，检验从数据库返回的数据
-   实体，nestjs 对象和数据库的映射关系

## dto 文件

```ts
import { IsInt, IsString } from "class-validator";

export class CreateCatDto {
    @IsString()
    readonly name: string;

    @IsInt()
    readonly age: number;

    @IsString()
    readonly breed: string;
}
```

## entity 文件

```ts
import { ApiProperty } from "@nestjs/swagger";

export class Cat {
    /**
     * The name of the Cat
     * @example Kitty
     */
    name: string;

    @ApiProperty({ example: 1, description: "The age of the Cat" })
    age: number;

    @ApiProperty({
        example: "Maine Coon",
        description: "The breed of the Cat",
    })
    breed: string;
}
```

## 在 controller 中使用

```ts
import { Body, Controller, Get, Param, Post } from "@nestjs/common";
import {
    ApiBearerAuth,
    ApiOperation,
    ApiResponse,
    ApiTags,
} from "@nestjs/swagger";
import { CatsService } from "./cats.service";
import { CreateCatDto } from "./dto/create-cat.dto";
import { Cat } from "./entities/cat.entity";

@ApiBearerAuth()
@ApiTags("cats")
@Controller("cats")
export class CatsController {
    constructor(private readonly catsService: CatsService) {}

    @Post()
    @ApiOperation({ summary: "Create cat" })
    @ApiResponse({ status: 403, description: "Forbidden." })
    async create(@Body() createCatDto: CreateCatDto): Promise<Cat> {
        return this.catsService.create(createCatDto);
    }

    @Get(":id")
    @ApiResponse({
        status: 200,
        description: "The found record",
        type: Cat,
    })
    findOne(@Param("id") id: string): Cat {
        return this.catsService.findOne(+id);
    }
}
```