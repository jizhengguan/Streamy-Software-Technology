# Streamy Code-Level Sequence Diagram: POST /users Registration

This document models the code-level sequence for the Streamy POST /users account-registration scenario, covering method invocations, return types, activation bars, loops, alternatives, and concurrent branches.

## 1. Full Sequence Diagram

```mermaid
---
title: Streamy POST /users Registration
---
sequenceDiagram
    autonumber
    participant C as UserController
    participant S as CreateUserService
    participant R as UserRepository
    participant M as WelcomeEmailService
    participant E as EventPublisher

    C->>+S: create_user(email: String) : Result
    S->>R: find_by_email(email: String) : User[]
    R-->>S: existingUsers: User[]

    loop each existingUser
        S->>S: is_active(existingUser) : Boolean
    end

    alt active account exists
        S-->>C: conflict: RegistrationError
    else registration permitted
        S->>R: insert_user(email: String) : User
        R-->>S: newUser: User

        par welcome email
            S->>M: send_welcome_email(newUser) : Boolean
            M-->>S: emailAccepted: Boolean
        and creation event
            S->>E: publish_user_created(newUser) : Boolean
            E-->>S: eventAccepted: Boolean
        end

        S-->>C: newUser: User
    end

    deactivate S
```

## 2. Architecture Analysis

### 2.1 Leaky Abstractions

Iterating over all matched users to identify active accounts may be inefficient. Consider a repository method such as `exists_active_by_email(email)`, supported by appropriate uniqueness rules.

### 2.2 Side-Effect Coupling

Sending email and publishing an event during registration can make retries and partial failures difficult. Consider an event-driven worker and, where needed, a transactional outbox for reliable delivery.

## 3. Verification

- ✅ Service activation begins at `C->>+S: create_user(...)` and ends at `deactivate S`.
- ✅ `loop`, `alt/else`, and `par/and` frames render with separate labels.
- ✅ Actual method signatures and return types are used throughout.