<p align="center">
  <a href="http://nestjs.com/" target="blank"><img src="https://nestjs.com/img/logo-small.svg" width="120" alt="Nest Logo" /></a>
</p>

[circleci-image]: https://img.shields.io/circleci/build/github/nestjs/nest/master?token=abc123def456
[circleci-url]: https://circleci.com/gh/nestjs/nest

  <p align="center">A progressive <a href="http://nodejs.org" target="_blank">Node.js</a> framework for building efficient and scalable server-side applications.</p>
    <p align="center">
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/v/@nestjs/core.svg" alt="NPM Version" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/l/@nestjs/core.svg" alt="Package License" /></a>
<a href="https://www.npmjs.com/~nestjscore" target="_blank"><img src="https://img.shields.io/npm/dm/@nestjs/common.svg" alt="NPM Downloads" /></a>
<a href="https://circleci.com/gh/nestjs/nest" target="_blank"><img src="https://img.shields.io/circleci/build/github/nestjs/nest/master" alt="CircleCI" /></a>
<a href="https://discord.gg/G7Qnnhy" target="_blank"><img src="https://img.shields.io/badge/discord-online-brightgreen.svg" alt="Discord"/></a>
<a href="https://opencollective.com/nest#backer" target="_blank"><img src="https://opencollective.com/nest/backers/badge.svg" alt="Backers on Open Collective" /></a>
<a href="https://opencollective.com/nest#sponsor" target="_blank"><img src="https://opencollective.com/nest/sponsors/badge.svg" alt="Sponsors on Open Collective" /></a>
  <a href="https://paypal.me/kamilmysliwiec" target="_blank"><img src="https://img.shields.io/badge/Donate-PayPal-ff3f59.svg" alt="Donate us"/></a>
    <a href="https://opencollective.com/nest#sponsor"  target="_blank"><img src="https://img.shields.io/badge/Support%20us-Open%20Collective-41B883.svg" alt="Support us"></a>
  <a href="https://twitter.com/nestframework" target="_blank"><img src="https://img.shields.io/twitter/follow/nestframework.svg?style=social&label=Follow" alt="Follow us on Twitter"></a>
</p>
  <!--[![Backers on Open Collective](https://opencollective.com/nest/backers/badge.svg)](https://opencollective.com/nest#backer)
  [![Sponsors on Open Collective](https://opencollective.com/nest/sponsors/badge.svg)](https://opencollective.com/nest#sponsor)-->

## Description

[Nest](https://github.com/nestjs/nest) framework TypeScript starter repository.

## Project setup

```bash
$ npm install
```

## Compile and run the project

```bash
# development
$ npm run start

# watch mode
$ npm run start:dev

# production mode
$ npm run start:prod
```

## Run tests

```bash
# unit tests
$ npm run test

# e2e tests
$ npm run test:e2e

# test coverage
$ npm run test:cov
```

## Deployment

When you're ready to deploy your NestJS application to production, there are some key steps you can take to ensure it runs as efficiently as possible. Check out the [deployment documentation](https://docs.nestjs.com/deployment) for more information.

If you are looking for a cloud-based platform to deploy your NestJS application, check out [Mau](https://mau.nestjs.com), our official platform for deploying NestJS applications on AWS. Mau makes deployment straightforward and fast, requiring just a few simple steps:

```bash
$ npm install -g @nestjs/mau
$ mau deploy
```

With Mau, you can deploy your application in just a few clicks, allowing you to focus on building features rather than managing infrastructure.

## Resources

Check out a few resources that may come in handy when working with NestJS:

- Visit the [NestJS Documentation](https://docs.nestjs.com) to learn more about the framework.
- For questions and support, please visit our [Discord channel](https://discord.gg/G7Qnnhy).
- To dive deeper and get more hands-on experience, check out our official video [courses](https://courses.nestjs.com/).
- Deploy your application to AWS with the help of [NestJS Mau](https://mau.nestjs.com) in just a few clicks.
- Visualize your application graph and interact with the NestJS application in real-time using [NestJS Devtools](https://devtools.nestjs.com).
- Need help with your project (part-time to full-time)? Check out our official [enterprise support](https://enterprise.nestjs.com).
- To stay in the loop and get updates, follow us on [X](https://x.com/nestframework) and [LinkedIn](https://linkedin.com/company/nestjs).
- Looking for a job, or have a job to offer? Check out our official [Jobs board](https://jobs.nestjs.com).

## Support

Nest is an MIT-licensed open source project. It can grow thanks to the sponsors and support by the amazing backers. If you'd like to join them, please [read more here](https://docs.nestjs.com/support).

## Stay in touch

- Author - [Kamil Myśliwiec](https://twitter.com/kammysliwiec)
- Website - [https://nestjs.com](https://nestjs.com/)
- Twitter - [@nestframework](https://twitter.com/nestframework)

## License

Nest is [MIT licensed](https://github.com/nestjs/nest/blob/master/LICENSE).
#  NestJS Architecture Mastery: Overview

This repository is a systematic record of learning the core building blocks and data flow of NestJS.

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

## 🛠 2. Core Building Blocks

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


