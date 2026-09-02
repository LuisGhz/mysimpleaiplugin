---
name: nestjs-ddd-architecture
description: A scalable and maintainable NestJS project structure following Domain-Driven Design (DDD) principles, with a focus on TypeScript best practices, modular architecture, and clean code organization.
---

You are an expert in TypeScript, Nestjs, and scalable web application development. You write maintainable, performant, and accessible code following Angular and TypeScript best practices.

## TypeScript Best Practices

- Use strict type checking
- Prefer type inference when the type is obvious
- Avoid the `any` type; use `unknown` when type is uncertain
- Always keep in mind existing paths from `tsconfig.json` for cleaner imports
- Group related files and implement barrel files for dtos, services, interfaces, entities, etc., to simplify imports, if they exists, update them accordingly, Here's a barrel file example:
```typescript
// dtos/index.ts
export { CreateUserReqDto, CreateUserResDto } from './createUser.dto';
export { UpdateUserReqDto, UpdateUserResDto } from './updateUser.dto';
```
- Don't utilize barrel files at module level to avoid circular dependencies
- Avoid the use of `Error` type in favor of custom nestjs exceptions like `BadRequestException`, `NotFoundException`, etc.
- For one-line `if` statements, prefer not using curly braces `{}` for better readability, unless necessary for clarity.
- Use `#methodName` | `#propertyName` for private methods/properties instead of the `private` keyword for better encapsulation, these private members should be at the bottom of the class after public and protected members.
- If a method is gonna required many parameters, consider using a single object parameter with named properties for better readability and maintainability.

## DTOS

- Look up for existing DTOs before creating new ones and understand the current structure if exists
- Use class-validator decorators for validation and class-transformer decorators for transformation
- Keep DTOs simple and focused on a single purpose
- Use partial DTOs for update operations
- File naming convention: `<operation><entity>.dto.ts` (e.g., `createUser.dto.ts`)
- Group related DTOs in the same file by request and response (if applicable), e.g.:
  - `createUser.dto.ts` for `CreateUserReqDto` and `CreateUserResDto`
  - `updateUser.dto.ts` for `UpdateUserReqDto` and `UpdateUserResDto`
- Use `PascalCase` for DTO class names


## Services
- Keep services focused on a single responsibility
- Use dependency injection for better testability
- Avoid business logic in controllers; delegate to services
- Use async/await for asynchronous operations
- Handle exceptions gracefully using NestJS's built-in exception filters

## Controllers
- Keep controllers thin; delegate business logic to services
- Use decorators for routing and request handling
- Validate incoming requests using DTOs
- Use appropriate HTTP status codes for responses
- Group related endpoints in the same controller
- File naming convention: `<entity>.controller.ts` (e.g., `user.controller.ts`)
- Use `PascalCase` for controller class names

## Modules
- Organize related components (controllers, services, etc.) into modules
- Use feature modules to encapsulate functionality
- Import only necessary modules to keep the application lightweight
- File naming convention: `<entity>.module.ts` (e.g., `user.module.ts`)
- Use `PascalCase` for module class names

## Migrations
- If you need to create (generate) a migration, use TypeORM CLI or NestJS migration tools, you can find the scripts in the `package.json` file
- Name migrations descriptively, e.g., `AddUserTable`, `UpdatePromptEntity`
- Keep migrations focused on a single change
- Once a migration is created, apply it with the corresponding `migration:run` command in the `package.json` file, you can find different scripts for different entities/modules so only run the necessary migration.

## Project structure
- Follow a modular structure, grouping related files together
- Use a consistent naming convention for files and directories
- This is a project structure example:
```
src/
│── modules/
│   ├── user/
│   │   ├── dtos/
│   │   ├── entities/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── user.module.ts
│   ├── auth/
│   │   ├── dtos/
│   │   ├── controllers/
│   │   ├── services/
│   │   ├── auth.module.ts
│   └── ...
│── core/
│   ├── decorators/
│   ├── guards/
│   └── ...
│── config/
│   ├── app.config.ts
│   ├── env
│        │── env.schema.ts
│        │── env.service.ts
│   └── db
│   └── ...
│── main.ts
└── app.module.ts
```