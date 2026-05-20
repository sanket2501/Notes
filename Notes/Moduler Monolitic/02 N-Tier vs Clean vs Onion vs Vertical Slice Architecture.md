# N-Tier vs Clean vs Onion vs Vertical Slice Architecture

---

# Table of Contents

1. Introduction
2. N-Tier Architecture
3. Clean Architecture
4. Onion Architecture
5. Vertical Slice Architecture (VSA)
6. Architecture Comparison
7. Dependency Flow
8. Which Architecture to Choose
9. Real-World Usage
10. Interview Questions
11. Final Summary

---

# 1. Introduction

Software architecture defines:

- How code is organized
- How layers communicate
- How dependencies are managed
- How maintainable the system becomes

Common architectures in .NET:

- N-Tier Architecture
- Clean Architecture
- Onion Architecture
- Vertical Slice Architecture (VSA)

---

# 2. N-Tier Architecture

## Definition

N-Tier Architecture divides the application into multiple logical layers.

Usually:

```text
Presentation Layer
    ↓
Business Layer
    ↓
Data Access Layer
    ↓
Database
```

---

## Common Layers

### Presentation Layer

Handles:

- UI
- API Controllers
- HTTP Requests

Example:

```csharp
OrderController
```

---

### Business Layer

Contains:

- Business rules
- Services
- Validation

Example:

```csharp
OrderService
```

---

### Data Access Layer

Handles:

- Database operations
- Repositories
- EF Core

Example:

```csharp
OrderRepository
```

---

## Structure

```text
Project
├── UI
├── Business
├── DataAccess
└── Database
```

---

## Characteristics

- Layer-based structure
- Separation of concerns
- Easy to understand
- Sequential dependency flow

---

## Advantages

- Simple architecture
- Easy for beginners
- Good for CRUD applications
- Easy debugging

---

## Disadvantages

### Tight Layer Coupling

Upper layers depend on lower layers.

### Difficult Testing

Business logic often depends on database.

### Large Service Classes

Business layer can become huge.

### Low Flexibility

Hard to evolve for complex systems.

---

## Dependency Direction

```text
UI → Business → DataAccess → Database
```

---

# 3. Clean Architecture

## Definition

Clean Architecture focuses on:

- Dependency inversion
- Independent business logic
- Framework independence


---

## Core Principle

Dependencies must point inward.

Inner layers should not know about outer layers.

---

## Structure

```text
┌──────────────────────┐
│ Presentation Layer   │
├──────────────────────┤
│ Infrastructure Layer │
├──────────────────────┤
│ Application Layer    │
├──────────────────────┤
│ Domain Layer         │
└──────────────────────┘
```

---

## Layers

## Domain Layer

Contains:

- Entities
- Business rules
- Core logic

No external dependencies.

---

## Application Layer

Contains:

- Use cases
- Interfaces
- DTOs
- CQRS handlers

---

## Infrastructure Layer

Contains:

- EF Core
- Email service
- File storage
- External APIs

---

## Presentation Layer

Contains:

- Controllers
- APIs
- UI

---

## Dependency Rule

```text
Outer Layer → Inner Layer
```

But inner layers never depend on outer layers.

---

## Advantages

### Highly Testable

Business logic independent from infrastructure.

### Maintainable

Clear separation of responsibilities.

### Framework Independent

Core logic survives framework changes.

### Scalable

Works well for enterprise systems.

---

## Disadvantages

### More Boilerplate

More folders and abstractions.

### Initial Complexity

Can feel overwhelming for beginners.

### Overengineering Risk

Too much abstraction for small projects.

---

# 4. Onion Architecture

## Definition

Onion Architecture is similar to Clean Architecture.

Focuses on:

- Domain-centric design
- Dependency inversion
- Infrastructure isolation

---

## Structure

```text
        Infrastructure
              ↓
        Application
              ↓
           Domain
```

Domain is at the center.

---

## Core Idea

Business logic is the most important part.

Everything depends on the domain.

---

## Layers

## Domain Core

Contains:

- Entities
- Value Objects
- Domain Services

---

## Application Services

Contains:

- Use cases
- Interfaces
- Application logic

---

## Infrastructure

Contains:

- Database
- External services
- APIs

---

## Advantages

- Strong domain focus
- Highly maintainable
- Excellent for DDD
- Easy testing

---

## Disadvantages

- Complex for beginners
- Many abstractions
- Requires discipline

---

## Difference from Clean Architecture

Very similar.

Main difference:

| Clean Architecture | Onion Architecture |
|---|---|
| Use-case focused | Domain-focused |
| More generalized | More DDD-oriented |

---

# 5. Vertical Slice Architecture (VSA)

## Definition

Vertical Slice Architecture organizes code by feature instead of technical layers.

Instead of:

```text
Controllers
Services
Repositories
```

We organize by:

```text
Features
```

---

# Structure

```text
Features
├── Orders
│   ├── CreateOrder
│   ├── GetOrder
│   └── DeleteOrder
│
├── Products
└── Users
```

---

# Example

## CreateOrder Feature

```text
CreateOrder
├── Command.cs
├── Handler.cs
├── Validator.cs
├── Endpoint.cs
└── Response.cs
```

---

# Key Idea

Each feature contains everything required for that use case.

---

## Common Technologies

Often used with:

- CQRS
- MediatR
- Minimal APIs

In :contentReference[oaicite:1]{index=1} .NET applications.

---

## Advantages

### Better Feature Isolation

Everything related to one feature stays together.

### Easier Maintenance

Developers can quickly locate code.

### Scalable for Teams

Different teams work on different features.

### Reduced Coupling

Less dependency between features.

---

## Disadvantages

### Duplicate Code

Some duplication may occur.

### Requires Discipline

Need proper feature boundaries.

### Learning Curve

Different from traditional layered architecture.

---

# 6. Architecture Comparison

| Feature | N-Tier | Clean | Onion | Vertical Slice |
|---|---|---|---|---|
| Structure Type | Layer-based | Layer-based | Domain-centric | Feature-based |
| Complexity | Low | Medium/High | High | Medium |
| Testability | Medium | High | High | High |
| Scalability | Medium | High | High | High |
| Domain Focus | Low | Medium | High | Medium |
| Best For | CRUD Apps | Enterprise Apps | DDD Systems | Modern APIs |
| Coupling | Higher | Low | Low | Low |
| Learning Curve | Easy | Medium | Hard | Medium |

---

# 7. Dependency Flow

## N-Tier

```text
UI → Business → DataAccess
```

---

## Clean Architecture

```text
Presentation → Application → Domain
Infrastructure → Application → Domain
```

Dependencies point inward.

---

## Onion Architecture

```text
Infrastructure → Application → Domain
```

Domain is at the center.

---

## Vertical Slice

```text
Feature → Feature Independence
```

Each slice owns its own logic.

---

# 8. Which Architecture Should You Choose?

## Choose N-Tier When

- Small applications
- Simple CRUD systems
- Beginner projects

---

## Choose Clean Architecture When

- Enterprise applications
- Long-term maintainability needed
- Large teams

---

## Choose Onion Architecture When

- Domain-Driven Design (DDD)
- Complex business rules
- Enterprise systems

---

## Choose Vertical Slice Architecture When

- Modern APIs
- CQRS/MediatR usage
- Modular monolith systems
- Feature-based development

---

# 9. Real-World Usage

## N-Tier

Common in older enterprise applications.

---

## Clean Architecture

Popular in modern ASP.NET Core enterprise systems.

---

## Onion Architecture

Used in DDD-heavy systems.

---

## Vertical Slice Architecture

Very popular in modern .NET applications.

Especially with:

- Minimal APIs
- CQRS
- MediatR

---

# 10. Interview Questions

## What is N-Tier Architecture?

An architecture that separates application into multiple logical layers.

---

## What is Clean Architecture?

An architecture where dependencies point inward and business logic remains independent.

---

## Difference Between Clean and Onion Architecture?

### Clean Architecture

- Use-case focused

### Onion Architecture

- Domain-focused

---

## What is Vertical Slice Architecture?

An architecture where code is organized by feature/use case instead of technical layers.

---

## Why is Vertical Slice becoming popular?

Because it:

- Reduces coupling
- Improves maintainability
- Fits modern CQRS systems

---

# 11. Final Summary

## N-Tier

Best for:

- Simple CRUD applications

---

## Clean Architecture

Best for:

- Enterprise maintainable systems

---

## Onion Architecture

Best for:

- Complex domain-driven systems

---

## Vertical Slice Architecture

Best for:

- Modern .NET APIs
- CQRS systems
- Modular monoliths

---

# Modern Recommendation

A common modern combination is:

```text
Modular Monolith
    +
Vertical Slice Architecture
    +
Clean Architecture Principles
```

This gives:

- Feature isolation
- Maintainability
- Scalability
- Clean dependency management

---