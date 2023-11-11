---
title: nestjs Exception异常和Filters过滤器
tags: nestjs
categories: 后端
date: 2023-11-11 14:47:28
---
<meta name="referrer" content="no-referrer"/>

-   异常：抛出简单的内容
-   过滤器：抛出详细内容
-   优先级：`controller` 的`@UseFilters()` > 方法的`@UseFilters()` > 方法内部的 `throw`

## 异常

### 默认全局异常

开箱即用，此操作由内置的全局异常过滤器执行，该过滤器处理类型 HttpException（及其子类）的异常。当异常无法识别（既不是 HttpException 也不是继承自的类 HttpException）时，内置异常过滤器会生成以下默认 JSON 响应：

```json
{
    "statusCode": 500,
    "message": "Internal server error"
}
```

### 手动抛出异常

#### 标准(默认)

-   标准异常包含 `statusCode`和`message`

```ts
// cats.controller.ts

@Get()
async findAll() {
  throw new HttpException('Forbidden', HttpStatus.FORBIDDEN);
}
```

```json
{
    "statusCode": 403,
    "message": "Forbidden"
}
```

#### 自定义参数

```ts
@Get()
async findAll() {
  throw new HttpException({
    status: HttpStatus.FORBIDDEN,
    error: 'This is a custom message',
  }, HttpStatus.FORBIDDEN);
}
```

```json
{
    "status": 403,
    "error": "This is a custom message"
}
```

### 自定义异常(文件)

```ts
// forbidden.exception.ts

export class ForbiddenException extends HttpException {
    constructor() {
        super("Forbidden", HttpStatus.FORBIDDEN);
    }
}
```

```ts
// cats.controller.ts

@Get()
async findAll() {
  throw new ForbiddenException();
}

```

## 过滤器

## 普通过滤器

```ts
// http-exception.filter.ts

import {
    ExceptionFilter,
    Catch,
    ArgumentsHost,
    HttpException,
} from "@nestjs/common";
import { Request, Response } from "express";

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
    catch(exception: HttpException, host: ArgumentsHost) {
        const ctx = host.switchToHttp();
        const response = ctx.getResponse<Response>();
        const request = ctx.getRequest<Request>();
        const status = exception.getStatus();

        response.status(status).json({
            statusCode: status,
            timestamp: new Date().toISOString(),
            path: request.url,
        });
    }
}
```

```ts
// cats.controller.ts

@Post()
// @UseFilters(new HttpExceptionFilter())
@UseFilters(HttpExceptionFilter)
async create(@Body() createCatDto: CreateCatDto) {
  throw new ForbiddenException();
}
```

### 自定义全局过滤器

```ts
// all-exceptions.filter.ts

import { Catch, ArgumentsHost } from "@nestjs/common";
import { BaseExceptionFilter } from "@nestjs/core";

@Catch()
export class AllExceptionsFilter extends BaseExceptionFilter {
    catch(exception: unknown, host: ArgumentsHost) {
        super.catch(exception, host);
    }
}
```

```ts
// main.ts
async function bootstrap() {
    const app = await NestFactory.create(AppModule);

    const { httpAdapter } = app.get(HttpAdapterHost);
    app.useGlobalFilters(new AllExceptionsFilter(httpAdapter));

    await app.listen(3000);
}
bootstrap();
```