# 1. Controllers (@Controller)
 Definition
Controllers are responsible for handling incoming requests and returning responses to the client. In NestJS, a controller is a class decorated with @Controller(), which defines the routing path for the application.

 Core Concepts
1. Routing
The routing mechanism determines which controller handles which request. By providing a path prefix in @Controller('stocks'), you can group related routes and avoid repetitive code.

2. Request Object Access
NestJS provides specific decorators to access parts of the HTTP request directly, ensuring type safety and readability.

@Body(): Access the request payload (e.g., POST data).

@Query(): Access URL query parameters (e.g., ?page=1).

@Param(): Access dynamic route parameters (e.g., :id).

@Headers(): Access HTTP headers.

3. Standard Response Handling
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
이 문서는 NestJS의 핵심 빌딩 블록과 데이터 흐름을 체계적으로 정리한 학습 기록입니다.

# Total

##  0. Core Philosophy of NestJS
NestJS is a framework built on top of **Node.js**, leveraging **TypeScript** as its primary language. Its main objective is to provide a **"Scalable, Loosely Coupled, and Highly Maintainable Server Architecture."**

---

##  1. Request Lifecycle
The following is the sequential execution flow from the moment a client request hits the server until a response is sent back.



1. **Incoming Request**
2. **Middleware**: Handles common logic such as logging, cookie parsing, etc.
3. **Guards**: Handles Authentication and Authorization checks.
4. **Interceptors (Pre-handle)**: Executes logic before the handler (e.g., data transformation or performance timing).
5. **Pipes**: Performs Input Data Validation and Transformation (e.g., string to int).
6. **Controller (Handler)**: Routes the request and maps it to the appropriate business logic.
7. **Service (Provider)**: Performs actual business logic and database operations.
8. **Interceptors (Post-handle)**: Transforms or "wraps" the response data (Mapping).
9. **Exception Filters**: Catches unhandled exceptions throughout the cycle and returns standardized error responses.

---

## 2. Core Building Blocks

###  Modules (`@Module`)
- **Role**: The fundamental unit used to organize the application structure.
- **Key Properties**: `providers`, `controllers`, `imports`, `exports`.

###  Controllers (`@Controller`)
- **Role**: Responsible for handling incoming HTTP requests and returning responses to the client.
- **Features**: Uses classes and decorators to define routing maps.

###  Providers / Services (`@Injectable`)
- **Role**: The actual implementation of business logic.
- **Core Concept**: **Dependency Injection (DI)**. Managed by the NestJS IoC (Inversion of Control) container.

---

##  3. Middleware, Guards, and Pipes

###  Middleware
- **Features**: Executed **before** the route handler. Requires calling the `next()` function to proceed.

###  Guards (`CanActivate`)
- **Features**: Executed after middleware but before pipes. Used for permission checks (e.g., Admin roles).

### Pipes (`PipeTransform`)
- **Usage**: Data transformation (`ParseIntPipe`) and validation (`ValidationPipe`).

---

## 4. Advanced: Interceptors and Filters

###  Interceptors (`NestInterceptor`)
- **Usage**: Caching, standardizing response formats, and measuring request/response time.

###  Exception Filters (`@Catch`)
- **Usage**: Customizing the format of error responses generated globally across the application.

---

## 5. Recommended Folder Structure
```text
src/
├── common/             # Global guards, interceptors, filters, etc.
├── modules/            # Feature-based domain modules
│   ├── stocks/         # Example: Stock data module
│   │   ├── dto/        # Data Transfer Objects
│   │   ├── controllers/
│   │   ├── services/
│   │   └── stocks.module.ts
├── app.module.ts       # Root Module
└── main.ts             # Entry Point

- How to Save and Exit (Terminal)
If using nano: Ctrl + O (Save) -> Enter -> Ctrl + X (Exit)

If using vi: Press Esc -> Type :wq -> Enter  


