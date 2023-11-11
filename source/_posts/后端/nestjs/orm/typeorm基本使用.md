---
title: nestjs typeorm基本使用
date: 2023-11-11 14:24:10
tags: typeorm
categories: 后端
---
<meta name="referrer" content="no-referrer"/>

## 数据库配置

```ts
// database.ts
import { join } from "path";
export default {
    type: "mysql",
    host: "localhost",
    port: 3306,
    username: "root",
    password: "youwei",
    database: "nest",
    // 实体，nestjs对象和数据库的映射关系
    entities: [join(__dirname, "../", "**/**.entity{.ts,.js}")],
    // synchronize可以让数据库根据实体类自动生成表及元数据（严禁在生产环境中使用,会破坏表结构）
    synchronize: true,
};
```

## 实体类

```ts
import { Column, Entity, PrimaryGeneratedColumn } from "typeorm";

// 默认表名为class名称的小写，也可以通过设置@Entity(‘coffee’)来定义表名
@Entity({ name: "user-by-mysql" })
export class UserByMysql {
    @PrimaryGeneratedColumn()
    id: number;

    @Column({ length: 20 })
    name: string;

    @Column("varchar")
    password: string;

    @Column()
    status: boolean;
}
```

## 错误

### 错误 1

如果有报下面的错误。导致这个错误的原因是，目前，最新的 mysql 模块并未完全支持 MySQL 8 的“caching_sha2_password”加密方式，而“caching_sha2_password”在 MySQL 8 中是默认的加密方式。因此，下面的方式命令是默认已经使用了“caching_sha2_password”加密方式，该账号、密码无法在 mysql 模块中使用。

```
ER_NOT_SUPPORTED_AUTH_MODE: Client does not support authentication protocol requested by server; consider upgrading MySQL client
```

解决方法是从新修改用户 root 的密码，并指定 mysql 模块能够支持的加密方式：

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
```

### 错误 2

```
Nest can't resolve dependencies of the TypeOrmCoreModule (TypeOrmModuleOptions, ?). Please make sure that the argument ModuleRef at index [1] is available in the TypeOrmCoreModule context.
```

-   把`@nestjs/typeorm`和`typeorm`，降级

```
"@nestjs/typeorm": "^7.0.0",
"typeorm": "^0.2.24"
```

[看文档](https://docs.nestjs.com/techniques/database#database)
[看视频](https://www.bilibili.com/video/BV1bQ4y1A77L?p=13&vd_source=1849c61e1da64155e8d0f1ff99761382)