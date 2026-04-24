# NestJS Core Concepts

A structured reference covering NestJS's core building blocks and request lifecycle.

---

## Table of Contents

1. [Controllers (@Controller)](#1-controllers-controller)
2. [Providers & Services (@Injectable)](#2-providers--services-injectable)
3. [Modules (@Module)](#3-modules-module)
4. [Middleware](#4-middleware)
5. [Exception Filters (@Catch)](#5-exception-filters-catch)
6. [Pipes (PipeTransform)](#6-pipes-pipetransform)
7. [Guards (CanActivate)](#7-guards-canactivate)
8. [Interceptors (NestInterceptor)](#8-interceptors-nestinterceptor)
9. [Custom Route Decorators](#9-custom-route-decorators)

---

## 1. Controllers (`@Controller`)

### Definition

Controllers handle incoming requests and return responses to the client. A controller is a class decorated with `@Controller()`, which defines the routing path for the application.

### Core Concepts

**Routing**
By providing a path prefix like `@Controller('stocks')`, you can group related routes and avoid repetitive code.

**Request Object Decorators**

| Decorator | Description |
|---|---|
| `@Body()` | Request payload (POST data) |
| `@Query()` | URL query parameters (`?page=1`) |
| `@Param()` | Dynamic route parameters (`:id`) |
| `@Headers()` | HTTP headers |

**Standard Response Handling**
- Objects or arrays are automatically serialized to JSON
- Primitive types are sent as-is
- Default status code is `200 OK` (except POST, which is `201 Created`)

### Code Example

```typescript
import { Controller, Get, Post, Body, Param, Query, HttpCode, HttpStatus } from '@nestjs/common';
import { CreateStockDto } from './dto/create-stock.dto';

@Controller('stocks')
export class StocksController {

  // POST /stocks
  @Post()
  @HttpCode(HttpStatus.CREATED)
  create(@Body() createStockDto: CreateStockDto) {
    return {
      message: 'Stock record created',
      data: createStockDto,
    };
  }

  // GET /stocks?market=KOSPI
  @Get()
  findAll(@Query('market') market: string) {
    return `This returns all stocks from ${market}`;
  }

  // GET /stocks/AAPL
  @Get(':symbol')
  findOne(@Param('symbol') symbol: string) {
    return `This returns details for stock: ${symbol}`;
  }
}
```

### Why Use Controllers?

- **Separation of Concerns**: Controllers only handle the HTTP layer (routing, request parsing) and delegate business logic to Services.
- **Declarative Programming**: Decorators make the API structure visible at a glance, improving maintainability.
- **Platform Agnostic**: Works on top of either Express or Fastify without changing business logic.

---

## 2. Providers & Services (`@Injectable`)

### Definition

Providers are the fundamental building blocks of NestJS that handle complex business logic. The most common type is a Service, but they can also be repositories, factories, or helpers. The defining characteristic is their ability to be **injected as a dependency** into other classes.

### Core Concepts

**Dependency Injection (DI)**
Instead of creating instances manually with `new`, you request a dependency through the class constructor. NestJS automatically provides the required instances.

**`@Injectable()` Decorator**
Tells the NestJS compiler that this class is a Provider and should be managed by the IoC container. Without it, Nest won't know how to inject the class.

**Provider Scope (Default: Singleton)**
By default, providers are singletons — only one instance is created and shared across the entire application. This is memory-efficient and ideal for shared state like in-memory caches or DB connection pools.

### Code Example

```typescript
import { Injectable } from '@nestjs/common';

export interface StockPrice {
  symbol: string;
  price: number;
  lastUpdated: Date;
}

@Injectable()
export class StocksService {
  // Singleton state - in-memory data store
  private readonly stockData: Map<string, StockPrice> = new Map();

  updatePrice(symbol: string, price: number): void {
    this.stockData.set(symbol, {
      symbol,
      price,
      lastUpdated: new Date(),
    });
  }

  getPrice(symbol: string): StockPrice | undefined {
    return this.stockData.get(symbol);
  }
}
```

### Why Use Providers?

- **Separation of Concerns**: Controllers stay thin (HTTP only), while providers stay fat (logic, DB, math).
- **Testability**: Injected dependencies can be swapped with mock services without touching the controller.
- **Modularity**: Providers can be exported from one module and reused across the entire application.

---

## 3. Modules (`@Module`)

### Definition

A Module is a class annotated with `@Module()` that provides metadata NestJS uses to organize the application structure. Every application has at least one **root module** (`AppModule`).

### Core Concepts

**The 4 Properties of `@Module()`**

| Property | Description |
|---|---|
| `providers` | Services to be instantiated and shared within this module |
| `controllers` | Controllers defined in this module |
| `imports` | External modules this module depends on |
| `exports` | Providers to expose to other modules |

**Feature Modules**
As the application grows, grouping related code into Feature Modules (e.g., `UsersModule`, `StocksModule`) is best practice.

**Shared & Global Modules**
- **Shared Module**: Modules are singletons by default. Any module that imports another shares the same provider instance.
- **Global Module**: Using `@Global()` makes a module available everywhere without repeated imports.

### Code Example

```typescript
import { Module } from '@nestjs/common';
import { StocksController } from './stocks.controller';
import { StocksService } from './stocks.service';

@Module({
  controllers: [StocksController],
  providers: [StocksService],
  exports: [StocksService], // Makes StocksService available to other modules
})
export class StocksModule {}
```

### Why Use Modules?

- **Encapsulation**: Internal logic is hidden; only explicitly exported providers are visible externally.
- **Dependency Management**: Organizes the application graph, making component interactions clear.
- **Scalability**: Modular architecture mirrors microservice structure and scales to enterprise size.

---

## 4. Middleware

### Definition

Middleware is a function called **before** the route handler. It has direct access to the Request and Response objects, and the `next()` function in the request-response cycle.

### Core Concepts

**What Middleware Can Do**
- Execute any code (logging, security checks)
- Modify the request and response objects
- End the request-response cycle (e.g., block unauthorized access)
- Call the next middleware function via `next()`

**Applying Middleware**
Middleware is not applied via decorators. Instead, it is configured in the module class using the `configure()` method of the `NestModule` interface.

### Code Example

```typescript
import { Injectable, NestMiddleware } from '@nestjs/common';
import { Request, Response, NextFunction } from 'express';

@Injectable()
export class LoggerMiddleware implements NestMiddleware {
  use(req: Request, res: Response, next: NextFunction) {
    console.log(`[Request] ${req.method} ${req.url}`);
    next();
  }
}

// app.module.ts
export class AppModule implements NestModule {
  configure(consumer: MiddlewareConsumer) {
    consumer
      .apply(LoggerMiddleware)
      .forRoutes('stocks');
  }
}
```

### Why Use Middleware?

- **Cross-Cutting Concerns**: Ideal for logic that applies to many routes, such as attaching a user to a request after authentication.
- **Loose Coupling**: Keeps controllers clean by extracting non-business logic into a separate layer.

---

## 5. Exception Filters (`@Catch`)

### Definition

Exception Filters process all unhandled exceptions across an application. Custom filters let you control the exact response content sent back to the client.

### Core Concepts

**Standard Exception Classes**
NestJS provides a built-in `HttpException` class. Best practice is to use specific subclasses: `BadRequestException`, `NotFoundException`, `ForbiddenException`, etc.

**`ArgumentsHost`**
The object received by the `catch()` method, which provides access to the underlying platform's Request and Response objects (Express or Fastify).

### Code Example

```typescript
import { ExceptionFilter, Catch, ArgumentsHost, HttpException } from '@nestjs/common';
import { Response } from 'express';

@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    const ctx = host.switchToHttp();
    const response = ctx.getResponse<Response>();
    const status = exception.getStatus();

    response.status(status).json({
      statusCode: status,
      timestamp: new Date().toISOString(),
      message: exception.message,
    });
  }
}
```

### Why Use Exception Filters?

- **Standardization**: Ensures every error response follows the same JSON structure, crucial for frontend integration.
- **Safe Messaging**: Prevents leaking sensitive server details by providing controlled error messages.

---

## 6. Pipes (`PipeTransform`)

### Definition

Pipes operate on the arguments of a route handler just before the method is invoked. They are used for **data transformation** and **validation**.

### Core Concepts

**Transformation vs Validation**

| Type | Description |
|---|---|
| Transformation | Converts input to the desired form (e.g., string → number) |
| Validation | Evaluates input; throws an exception before reaching the controller if invalid |

**Built-in Pipes**
`ParseIntPipe`, `ParseBoolPipe`, `ParseUUIDPipe`, `ValidationPipe` (used with `class-validator` for DTO validation)

### Code Example

```typescript
@Get(':id')
findOne(@Param('id', ParseIntPipe) id: number) {
  return this.stocksService.findOne(id);
}
```

### Why Use Pipes?

- **Data Integrity**: Ensures business logic only receives clean, validated data.
- **DRY Principle**: Validation logic is reusable across different controllers.

---

## 7. Guards (`CanActivate`)

### Definition

Guards determine whether a request will be handled by the route handler based on specific conditions (permissions, roles, etc.) present at runtime.

### Core Concepts

**Guards vs Middleware**
Middleware is "dumb" — it doesn't know which handler executes next. Guards have access to `ExecutionContext` and know exactly what is going to be executed.

**Execution Order**
`Middleware` → `Guards` → `Interceptors` → `Pipes` → `Route Handler`

### Code Example

```typescript
@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    return validateRequest(request);
  }
}
```

---

## 8. Interceptors (`NestInterceptor`)

### Definition

Interceptors inject extra logic before or after the execution of a route handler by intercepting the request/response stream.

### Core Concepts

**RxJS Observables**
The `intercept()` method wraps the request/response stream using RxJS, enabling powerful stream manipulation via `map`, `tap`, `catchError`, etc.

**Common Use Cases**
- Response transformation (wrapping results in a `{ data: ... }` object)
- Performance monitoring (calculating request duration)
- Caching

### Code Example

```typescript
@Injectable()
export class TransformInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(map(data => ({ data })));
  }
}
```

---

## 9. Custom Route Decorators

### Definition

NestJS allows you to create custom decorators to encapsulate repetitive logic for accessing request properties.

### Core Concepts

**Param Decorators**
Commonly used to extract user information from the request object after authentication.

**Composition**
Multiple decorators (Guards, Metadata, API docs) can be composed into a single decorator using `applyDecorators`.

### Code Example

```typescript
export const User = createParamDecorator(
  (data: string, ctx: ExecutionContext) => {
    const request = ctx.switchToHttp().getRequest();
    const user = request.user;
    return data ? user?.[data] : user;
  },
);

// Usage in controller
@Get()
async findOne(@User('firstName') firstName: string) {
  console.log(`Hello ${firstName}`);
}
```

---

## Request Lifecycle Summary

```
Client Request
     ↓
  Middleware       ← Logging, pre-auth processing
     ↓
   Guards          ← Permission / role checks
     ↓
 Interceptors      ← Pre-handler logic
     ↓
   Pipes           ← Data transformation & validation
     ↓
  Controller       ← Routing & response
     ↓
 Interceptors      ← Post-handler logic
     ↓
Exception Filters  ← Unhandled exception handling
     ↓
Client Response
```