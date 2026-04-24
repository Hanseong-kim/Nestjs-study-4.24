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
# 🚀 NestJS Architecture Mastery: Overview

이 문서는 NestJS의 핵심 빌딩 블록과 데이터 흐름을 체계적으로 정리한 학습 기록입니다.

## 📌 0. NestJS의 핵심 철학
NestJS는 **Node.js** 위에서 동작하는 프레임워크로, **TypeScript**를 기본으로 채택합니다. 
가장 큰 목적은 **"확장 가능하고(Scalable), 느슨한 결합(Loosely coupled)을 가진 유지보수가 용이한 서버 아키텍처"**를 제공하는 것입니다. 

---

## 🔄 1. Request Lifecycle (요청 생명주기)
클라이언트의 요청이 서버에 도달한 후 응답으로 돌아가기까지의 순서입니다.



1. **Incoming Request**
2. **Middleware**: 공통 로직(로깅, 쿠키 파싱 등) 처리.
3. **Guards**: 인증(Authentication) 및 인가(Authorization) 체크.
4. **Interceptors (Pre-handle)**: 핸들러 실행 전 데이터 가공이나 시간 측정.
5. **Pipes**: 입력 데이터 검증(Validation) 및 타입 변환(Transformation).
6. **Controller (Handler)**: 요청을 라우팅하고 비즈니스 로직으로 연결.
7. **Service (Provider)**: 실제 비즈니스 로직 및 DB 연산 수행.
8. **Interceptors (Post-handle)**: 응답 결과를 최종적으로 포장(Mapping).
9. **Exception Filters**: 전 과정에서 발생한 에러를 처리하여 표준 응답 반환.

---

## 🛠 2. Core Building Blocks

### 📦 Modules (`@Module`)
- **역할**: 애플리케이션의 구조를 조직화하는 기본 단위.
- **주요 속성**: `providers`, `controllers`, `imports`, `exports`.

### 🎮 Controllers (`@Controller`)
- **역할**: 외부로부터 들어오는 HTTP 요청을 받고 응답을 반환.
- **특징**: 클래스와 데코레이터를 사용하여 라우팅 맵을 생성.

### 💉 Providers / Services (`@Injectable`)
- **역할**: 비즈니스 로직의 실제 구현체.
- **핵심 개념**: **의존성 주입(Dependency Injection)**. NestJS IoC 컨테이너가 인스턴스를 관리.

---

## 🛡 3. Middleware, Guards, and Pipes

### 🚦 Middleware
- **특징**: 라우트 핸들러가 호출되기 **전**에 실행. `next()` 호출 필요.

### 🛡 Guards (`CanActivate`)
- **특징**: 미들웨어 다음, 파이프 전에 실행. 권한 체크(Admin 등)에 사용.

### 🧪 Pipes (`PipeTransform`)
- **용도**: 데이터 변환(`ParseIntPipe`) 및 유효성 검증(`ValidationPipe`).

---

## 🕵️ 4. Advanced: Interceptors and Filters

### 🕵️ Interceptors (`NestInterceptor`)
- **용도**: 캐싱, 응답 포맷 통일, 요청/응답 시간 측정.

### ⚠️ Exception Filters (`@Catch`)
- **용도**: 애플리케이션 전역에서 발생하는 에러 응답 형식을 커스텀화.

---

## 📂 5. Recommended Folder Structure
```text
src/
├── common/             # 공통 가드, 인터셉터, 필터
├── modules/            # 도메인별 기능 모듈
│   ├── stocks/         # 예: 주식 데이터 모듈
│   │   ├── dto/
│   │   ├── controllers/
│   │   ├── services/
│   │   └── stocks.module.ts
├── app.module.ts       # Root Module
└── main.ts             # Entry Point
### 4. 저장 및 종료
* **nano**를 사용 중이라면: `Ctrl + O` (저장) -> `Enter` -> `Ctrl + X` (종료)
* **vi**를 사용 중이라면: `Esc` 누른 후 `:wq` 입력 -> `Enter`

---

### 5. Git 커밋 (기록 남기기)
이제 문서가 완성되었으니 Git에 기록을 남기세요.

```bash
git add README.md
git commit -m "docs: update comprehensive overview guide in README"
git push origin main


