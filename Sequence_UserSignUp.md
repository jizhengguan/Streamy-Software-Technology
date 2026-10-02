# User Sign Up Flow — Sequence Diagram

This document models the service and user interactions for the Streamy user sign-up flow, covering synchronous/asynchronous messaging, lifelines, activation blocks, branching paths, notes, sequence numbering, and dropdown menus.

## Step 1: Actors, Participants, and Sequence Numbering

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
    autonumber
    actor Browser
    participant SignUpService as Sign Up Service
    participant UserService as User Service
    participant Kafka
```

## Step 2: Synchronous Messages and Activation Blocks

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
    autonumber
    actor Browser
    participant SignUpService as Sign Up Service
    participant UserService as User Service
    participant Kafka

    Browser->>+SignUpService: GET /sign_up
    SignUpService-->>-Browser: 200 OK (HTML page)
    Browser->>+SignUpService: POST /sign_up
    SignUpService->>SignUpService: Validate input
```

## Step 3: Branching Logic (Happy vs. Unhappy Paths)

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
    autonumber
    actor Browser
    participant SignUpService as Sign Up Service
    participant UserService as User Service
    participant Kafka

    Browser->>+SignUpService: GET /sign_up
    SignUpService-->>-Browser: 200 OK (HTML page)
    Browser->>+SignUpService: POST /sign_up
    SignUpService->>SignUpService: Validate input

    alt invalid input
        SignUpService-->>Browser: Error
    else valid input
        SignUpService->>UserService: POST /users
        UserService-->>SignUpService: 201 Created (User)
        SignUpService-->>Browser: 301 Redirect (Login page)
    end
```

## Step 4: Asynchronous Events, Notes, and Dropdown Links

```mermaid
---
title: User Sign Up Flow
---
sequenceDiagram
    autonumber
    actor Browser
    participant SignUpService as Sign Up Service
    participant UserService as User Service
    participant Kafka

    links UserService: {"Repository": "https://example.com/user-service"}

    Browser->>+SignUpService: GET /sign_up
    SignUpService-->>-Browser: 200 OK (HTML page)
    Browser->>+SignUpService: POST /sign_up
    SignUpService->>SignUpService: Validate input

    alt invalid input
        SignUpService-->>Browser: Error
    else valid input
        SignUpService->>UserService: POST /users
        UserService-)Kafka: User Created Event Published
        Note left of Kafka: other services take action based on this event
        UserService-->>SignUpService: 201 Created (User)
        SignUpService-->>Browser: 301 Redirect (Login page)
    end
```