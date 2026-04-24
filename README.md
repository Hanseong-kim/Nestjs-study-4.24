# 1. Controllers (@Controller)
 Definition
Controllers are responsible for handling incoming requests and returning responses to the client. In NestJS, a controller is a class decorated with @Controller(), which defines the routing path for the application.

 Core Concepts
## 1. Routing
The routing mechanism determines which controller handles which request. By providing a path prefix in @Controller('stocks'), you can group related routes and avoid repetitive code.

## 2. Request Object Access
NestJS provides specific decorators to access parts of the HTTP request directly, ensuring type safety and readability.

@Body(): Access the request payload (e.g., POST data).

@Query(): Access URL query parameters (e.g., ?page=1).

@Param(): Access dynamic route parameters (e.g., :id).

@Headers(): Access HTTP headers.

## 3. Standard Response Handling
NestJS handles responses automatically:

If you return a JavaScript object or array, it is automatically serialized to JSON.

If you return a primitive type (string, number), it is sent as-is.

The default status code is 200 OK (except for POST, which is 201 Created).

 Code Implementation Example
TypeScript
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
 Why use Controllers? 
Separation of Concerns: Controllers only handle the HTTP layer (routing, request parsing). They delegate "how" things are done to Services.

Declarative Programming: Using decorators makes the API structure visible at a glance, improving maintainability for large-scale projects like those at CitrusBits.

Platform Agnostic: By using decorators like @Req() and @Res(), NestJS can work on top of either Express or Fastify without changing the business logic.
---
# 2. Providers & Services (@Injectable)
📝 Definition
Providers are the fundamental building blocks of NestJS. While Controllers handle HTTP routing, Providers handle the complex business logic. The most common type of provider is a Service, but they can also be repositories, factories, or helpers.

The defining characteristic of a provider is its ability to be injected as a dependency into other classes (like Controllers), meaning the classes don't have to manage their own instances.

## Core Concepts
1. Dependency Injection (DI)
NestJS is built around the Inversion of Control (IoC) principle. Instead of creating an instance manually using the new keyword, you "request" a dependency through the class constructor.

How it works: When NestJS creates a Controller, it looks at the constructor, identifies which Services are needed, and automatically provides (injects) those instances.

2. The @Injectable() Decorator
This decorator is essential. It tells the NestJS compiler that this class is a Provider and should be managed by the Nest IoC container. Without this, Nest won't know how to inject this class into others.

3. Provider Scopes (Singleton by Default)
By default, NestJS providers are Singletons.

Singleton: Only one instance of the service is created and shared across the entire application.

Benefit: Highly memory-efficient and ideal for sharing state (like an in-memory cache or a database connection pool).

## Code Implementation Example
Below is a service designed for a Stock Trading domain, which aligns with your interests in quant trading.

TypeScript
import { Injectable } from '@nestjs/common';

// Domain Interface
export interface StockPrice {
  symbol: string;
  price: number;
  lastUpdated: Date;
}

@Injectable()
export class StocksService {
  // In-memory data store (Singleton state)
  private readonly stockData: Map<string, StockPrice> = new Map();

  // Logic: Update stock price
  updatePrice(symbol: string, price: number): void {
    this.stockData.set(symbol, {
      symbol,
      price,
      lastUpdated: new Date(),
    });
  }

  // Logic: Fetch stock price
  getPrice(symbol: string): StockPrice | undefined {
    return this.stockData.get(symbol);
  }
}

## Why use Providers? (The CS Perspective)
Separation of Concerns (SoC): Controllers stay "thin" (handling only HTTP), while Providers stay "fat" (handling the actual math, DB, and logic).

Testability: Since dependencies are injected, you can easily swap the real StocksService with a MockStocksService during unit testing without changing a single line of code in your Controller.

Modularity: Providers can be exported from one module and imported into another, making your code highly reusable across the entire application.

이 문서는 NestJS의 핵심 빌딩 블록과 데이터 흐름을 체계적으로 정리한 학습 기록입니다.

# 3. Modules (@Module)
Definition
A Module is a class annotated with a @Module() decorator. It provides the metadata that NestJS uses to organize the application structure. Every application has at least one Root Module (usually AppModule), which serves as the starting point for the Application Graph.

## Core Concepts
1. The @Module() Decorator
The decorator takes an object with four main properties that define the module's behavior:

providers: The services that will be instantiated by the Nest injector and that may be shared at least across this module.

controllers: The set of controllers defined in this module which have to be instantiated.

imports: The list of exported modules that this module requires.

exports: The subset of providers that should be available in other modules that import this one.

2. Feature Modules
As the application grows, it is best practice to group related code into Feature Modules (e.g., UsersModule, StocksModule). This maintains clear boundaries and manages complexity according to SOLID principles.

3. Shared & Global Modules

Shared Modules: Modules are singletons by default. If you export a provider, any module that imports that module will share the same instance of that provider.

Global Modules: Using the @Global() decorator makes a module available everywhere without needing to import it repeatedly (e.g., for database connections or configuration).

Code Implementation Example
TypeScript
import { Module } from '@nestjs/common';
import { StocksController } from './stocks.controller';
import { StocksService } from './stocks.service';

@Module({
  controllers: [StocksController],
  providers: [StocksService],
  exports: [StocksService] // Allows other modules to inject StocksService
})
export class StocksModule {}
Why use Modules? (CS Perspective)
Encapsulation: Modules hide internal logic. Only explicitly exported providers are visible to other parts of the system.

Dependency Management: It organizes the "Application Graph," making it clear how different components interact.

Scalability: This modular architecture allows NestJS applications to scale to enterprise sizes, mirroring the structure of microservices.

4. Middleware
Definition
Middleware is a function called before the route handler. It has direct access to the Request and Response objects, and the next() function in the application's request-response cycle.

Core Concepts
1. Middleware Capabilities

Executing any code (logging, security checks).

Making changes to the request and response objects.

Ending the request-response cycle (e.g., unauthorized access prevention).

Calling the next() middleware function in the stack.

2. Applying Middleware
Middleware is not applied via decorators in the class. Instead, it is configured in the module class using the configure() method of the NestModule interface.

Code Implementation Example
TypeScript
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
Why use Middleware? (CS Perspective)
Cross-Cutting Concerns: Ideal for logic that applies to many routes, such as authentication (attaching a user to the request) or header manipulation.

Loose Coupling: It keeps controllers clean by extracting non-business logic into a separate layer.

5. Exception Filters (@Catch)
Definition
Exception Filters are responsible for processing all unhandled exceptions across an application. Custom filters allow you to control the exact response content sent back to the client.

Core Concepts
1. Standard Exceptions
Nest provides a built-in HttpException class. It is best practice to use specific subclasses like BadRequestException, NotFoundException, or ForbiddenException.

2. ArgumentsHost
The catch() method receives an ArgumentsHost object, which allows you to access the underlying platform's Request and Response objects (Express or Fastify).

Code Implementation Example
TypeScript
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
Why use Exception Filters? (CS Perspective)
Standardization: It ensures every error response follows the same JSON structure, crucial for frontend integration.

User-Friendly Messaging: Prevents leaking sensitive server details by providing controlled error messages.

6. Pipes (PipeTransform)
Definition
Pipes operate on the arguments of a route handler just before the method is invoked. They are used for data transformation and validation.

Core Concepts
1. Transformation vs Validation

Transformation: Converts input data to the desired form (e.g., string to number).

Validation: Evaluates input data; if invalid, it throws an exception before reaching the controller.

2. Built-in Pipes

ParseIntPipe, ParseBoolPipe, ParseUUIDPipe.

ValidationPipe: Used for validating DTOs using class-validator.

Code Implementation Example
TypeScript
@Get(':id')
findOne(@Param('id', ParseIntPipe) id: number) {
  return this.stocksService.findOne(id);
}
Why use Pipes? (CS Perspective)
Data Integrity: Ensures business logic only receives clean, validated data.

DRY Principle: Validation logic is reusable across different controllers.

7. Guards (CanActivate)
Definition
Guards determine whether a request will be handled by the route handler based on specific conditions (permissions, roles, etc.) present at run-time.

Core Concepts
1. Guards vs Middleware
Middleware is "dumb" because it doesn't know which handler will be executed next. Guards have access to the ExecutionContext and know exactly what is going to be executed.

2. Execution Order
Guards are executed after all middleware but before any interceptor or pipe.

Code Implementation Example
TypeScript
@Injectable()
export class AuthGuard implements CanActivate {
  canActivate(context: ExecutionContext): boolean {
    const request = context.switchToHttp().getRequest();
    return validateRequest(request); 
  }
}
8. Interceptors (NestInterceptor)
Definition
Interceptors allow you to intercept the request/response stream to inject extra logic before or after the execution of a route handler.

Core Concepts
1. RxJS Observables
The intercept() method wraps the request/response stream using RxJS, allowing powerful stream manipulation (map, tap, catchError).

2. Use Cases

Response transformation (e.g., wrapping results in a { data: ... } object).

Performance monitoring (calculating request duration).

Caching.

Code Implementation Example
TypeScript
@Injectable()
export class TransformInterceptor implements NestInterceptor {
  intercept(context: ExecutionContext, next: CallHandler): Observable<any> {
    return next.handle().pipe(map(data => ({ data })));
  }
}
9. Custom Route Decorators
Definition
Nest allows you to create your own custom decorators to encapsulate repetitive logic for accessing request properties.

Core Concepts
1. Param Decorators
Commonly used to extract user information from the request object after authentication.

2. Composition
Multiple decorators (Guards, Metadata, API docs) can be composed into a single decorator using applyDecorators.

Code Implementation Example
TypeScript
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