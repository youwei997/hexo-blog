---
title: nestjs使用mongoose
tags: nestjs
categories: 后端
date: 2023-11-11 14:53:22
---
<meta name="referrer" content="no-referrer"/>

## 安装 mongoose

`npm i @nestjs/mongoose mongoose`

## nestjs 使用 mongoose 模块

```ts
// app.module.ts

import { Module } from "@nestjs/common";
import { MongooseModule } from "@nestjs/mongoose";
import { AppController } from "./app.controller";
import { AppService } from "./app.service";
import { VideoModule } from "./video/video.module";
import { UserModule } from "./user/user.module";

// nest 中的模块装饰器
@Module({
    // 引入子模块
    imports: [
        VideoModule,
        UserModule,
        MongooseModule.forRoot("mongodb://localhost:27017/nestApp"),
    ],
    controllers: [AppController],
    providers: [AppService],
})
export class AppModule {}
```

## 创建 schema

```ts
// /src/user/schemas/user.schemas.ts

import { Prop, Schema, SchemaFactory } from "@nestjs/mongoose";
import { Document } from "mongoose";

// 将User 和 Document类型合并
export type UserDocument = User & Document;

// Schema 装饰器
// 装饰器将@Schema()一个类标记为模式定义。它将我们的类映射Cat到同名的 MongoDB 集合
@Schema()
export class User extends Document {
    // 配置schema属性
    @Prop({ required: true, type: String })
    // 这里的类型是ts的类型
    username: string;

    @Prop({ required: true })
    email: string;

    // select 是否允许查询是查到的数据
    @Prop({ required: true, select: false })
    password: string;
}

// 创建schema
export const UserSchema = SchemaFactory.createForClass(User);
```

## 引入到对应的功能的 module

```ts
// user.module.ts

import { Module } from "@nestjs/common";
import { UserService } from "./user.service";
import { UserController } from "./user.controller";
import { MongooseModule } from "@nestjs/mongoose";
import { User, UserSchema } from "./schemas/user.schemas";

@Module({
    imports: [
        MongooseModule.forFeature([{ name: User.name, schema: UserSchema }]),
    ],
    controllers: [UserController],
    providers: [UserService],
})
export class UserModule {}
```

## 实现增删改查功能

```ts
// user.controller.ts

import {
    Controller,
    Get,
    Post,
    Body,
    Patch,
    Param,
    Delete,
} from "@nestjs/common";
import { UserService } from "./user.service";
import { CreateUserDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";
import { ApiTags, ApiOperation } from "@nestjs/swagger";

// swagger 标签分类
@ApiTags("user")
// 将装饰的class 装饰成controller
// 路径 http://localhost3000/user
// 括号里是路由
@Controller("user")
export class UserController {
    // 使用依赖注入的方式引入 service
    constructor(private readonly userService: UserService) {}

    @ApiOperation({
        summary: "添加用户",
    })

    // 方法装饰器，将controller里的方法装饰成路由，并且以请求方法调用
    // @Post post调用 http://localhost3000/user
    // 以下方法装饰器同理
    @Post()
    // @Body() 使用装饰器装饰 http 请求体参数
    // CreateUserDto ts 参数类型约束
    create(@Body() createUserDto: CreateUserDto) {
        return this.userService.create(createUserDto);
    }

    // @Get get调用 http://localhost3000/user
    @Get()
    findAll() {
        return this.userService.findAll();
    }

    // @Get get调用 http://localhost3000/user/123
    @Get(":id")
    findOne(@Param("id") id: string) {
        return this.userService.findOne(id);
    }

    @Patch(":id")
    update(@Param("id") id: string, @Body() updateUserDto: UpdateUserDto) {
        return this.userService.update(id, updateUserDto);
    }

    @Delete(":id")
    remove(@Param("id") id: string) {
        return this.userService.remove(id);
    }

    // 自定义路径
    // 和上面 @Get(':id') 装饰的方法会冲突
    // @Get('/customPath')
    // customPath() {
    //   return this.userService.customPath();
    // }
}
```

```ts
// user.service.ts

import { Injectable } from "@nestjs/common";
import { InjectModel } from "@nestjs/mongoose";
import { User, UserDocument } from "./schemas/user.schemas";
import { CreateUserDto } from "./dto/create-user.dto";
import { UpdateUserDto } from "./dto/update-user.dto";
import { Model } from "mongoose";

@Injectable()
export class UserService {
    constructor(
        @InjectModel(User.name) private userModel: Model<UserDocument>
    ) {}
    async create(createUserDto: CreateUserDto) {
        const createdUser = await this.userModel.create(createUserDto);
        return createdUser;
    }

    async findAll() {
        const result = await this.userModel.find().exec();
        return result;
    }

    async findOne(id: string) {
        return await this.userModel.findById(id);
    }

    async update(id: string, updateUserDto: UpdateUserDto) {
        return await this.userModel.findByIdAndUpdate(id, updateUserDto, {
            new: true,
        });
    }

    async remove(id: string) {
        return await this.userModel.findByIdAndRemove(id);
    }
}
```