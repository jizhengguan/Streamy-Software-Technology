# UML Class Diagram Refactoring

This document models the refactoring of a tightly coupled user registration flow, addressing two common code smells: **Data Clumps** and **Tight Coupling**.

## Step 1: Initial Tightly Coupled Classes

```mermaid
---
title: Initial Tightly Coupled Classes
---
classDiagram
    class UserController {
        -CreateUserService createUserService
        +UserController(CreateUserService createUserService)
        +Create(string email, string username) User
    }
    class CreateUserService {
        -UserModel userModel
        -SendWelcomeEmailService sendWelcomeEmailService
        -Kafka kafka
        +CreateUserService(UserModel um, SendWelcomeEmailService es, Kafka k)
        +Call(string email, string username) User
        -CheckActiveUsers(List~User~ users) bool
    }
    class UserModel {
        +FindUsersByEmail(string email) List~User~
        +CreateUser(string email, string username) User
    }
    class User {
        +int UserId
        +string Username
        +string Email
    }
    class SendWelcomeEmailService {
        +Call(string email, string username) bool
    }
    class Kafka {
        +PublishUserCreatedEvent(string email, string username) bool
    }

    UserController ..> CreateUserService : depends on
    CreateUserService ..> UserModel : depends on
    CreateUserService ..> SendWelcomeEmailService : depends on
    CreateUserService ..> Kafka : depends on
```

## Step 2: Refactored Data Clumps

```mermaid
---
title: Refactored Data Clumps
---
classDiagram
    class UserController {
        -CreateUserService _createUserService
        +UserController(CreateUserService createUserService)
        +Create(string email, string username) User
    }
    class CreateUserRequest {
        +string Email
        +string Username
        +Validate() bool
    }
    class CreateUserService {
        -UserModel _userModel
        -SendWelcomeEmailService _sendWelcomeEmailService
        -Kafka _kafka
        +CreateUserService(UserModel um, SendWelcomeEmailService es, Kafka k)
        +Call(CreateUserRequest createUserRequest) User
        -CheckActiveUsers(List~User~ users) bool
    }
    class UserModel {
        +FindUsersByEmail(string email) List~User~
        +CreateUser(string email, string username) User
    }
    class User {
        +int UserId
        +string Username
        +string Email
    }
    class SendWelcomeEmailService {
        +Call(User user) bool
    }
    class Kafka {
        +PublishUserCreatedEvent(User user) bool
    }

    UserController ..> CreateUserRequest : depends on
    UserController ..> CreateUserService : depends on
    CreateUserService ..> UserModel : depends on
    CreateUserService ..> SendWelcomeEmailService : depends on
    CreateUserService ..> Kafka : depends on
```

## Step 3: Refactored via Interface Realization

```mermaid
---
title: Refactored via Interface Realization
---
classDiagram
    class UserController {
        <<Class>>
        -ICreateUserService createUserService
        +UserController(ICreateUserService createUserService)
        +Create(string email, string username) User
    }
    class CreateUserRequest {
        <<Class>>
        +string Email
        +string Username
        +Validate() bool
    }
    class ICreateUserService {
        <<Interface>>
        +Call(CreateUserRequest createUserRequest) User
    }
    class CreateUserService {
        <<Class>>
        -UserModel _userModel
        -SendWelcomeEmailService _sendWelcomeEmailService
        -Kafka _kafka
        +CreateUserService(UserModel um, SendWelcomeEmailService es, Kafka k)
        +Call(CreateUserRequest createUserRequest) User
        -CheckActiveUsers(List~User~ users) bool
    }
    class UserModel {
        <<Class>>
        +FindUsersByEmail(string email) List~User~
        +CreateUser(string email, string username) User
    }
    class User {
        <<Class>>
        +int UserId
        +string Username
        +string Email
    }
    class SendWelcomeEmailService {
        <<Class>>
        +Call(User user) bool
    }
    class Kafka {
        <<Class>>
        +PublishUserCreatedEvent(User user) bool
    }

    UserController ..> CreateUserRequest : depends on
    UserController ..> ICreateUserService : depends on
    CreateUserService ..|> ICreateUserService : implements
    CreateUserService ..> UserModel : depends on
    CreateUserService ..> SendWelcomeEmailService : depends on
    CreateUserService ..> Kafka : depends on
```