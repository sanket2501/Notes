# Domain-Driven Design (DDD) Notes

# What is DDD?

## Definition

Domain-Driven Design (DDD) is a software design approach focused on:

- Business domain
- Real-world business problems
- Maintainable architecture
- Scalable enterprise applications

DDD helps developers and business experts speak the same language.

---

# Simple Meaning

> DDD means designing software based on business rules and domain logic instead of only database tables or technical implementation.

---

# Real-World Example

## E-Commerce Domain

Business concepts:

- Customer
- Order
- Payment
- Product
- Cart
- Shipment

DDD models software around these business concepts.

---

# Goal of DDD

DDD aims to:

- Reduce complexity
- Organize business logic
- Improve maintainability
- Separate responsibilities
- Handle large enterprise systems

---

# When to Use DDD

DDD is useful when:

✅ Complex business logic exists  
✅ Large enterprise applications  
✅ Multiple business rules  
✅ Multiple teams/modules  
✅ Long-term maintainability needed

---

# When NOT to Use DDD

Avoid DDD for:

❌ Small CRUD apps  
❌ Simple admin panels  
❌ Tiny projects  
❌ Very small APIs

DDD adds complexity.

---

# Core Concepts of DDD

| Concept | Purpose |
|---|---|
| Entity | Object with identity |
| Value Object | Object without identity |
| Aggregate | Group of related objects |
| Aggregate Root | Main controller of aggregate |
| Repository | Data access abstraction |
| Domain Service | Business logic service |
| Application Service | Use-case orchestration |
| Domain Event | Business event |
| Bounded Context | Logical domain boundary |
| Ubiquitous Language | Shared business language |

---

# 1. Entity

# What is Entity?

An Entity is an object that has:

- Unique identity
- Lifecycle
- Mutable data

Even if properties change, identity remains same.

---

# Example

```csharp
public class Customer
{
    public Guid Id { get; private set; }

    public string Name { get; private set; }
}
```

---

# Why Entity?

Because:

```text
Customer ID = 101
```

Even if:

- Name changes
- Email changes

It is still same customer.

Identity matters.

---

# Entity Characteristics

| Feature | Entity |
|---|---|
| Has Identity | ✅ |
| Mutable | ✅ |
| Equality by ID | ✅ |
| Lifecycle | ✅ |

---

# Examples of Entities

| Domain | Entity |
|---|---|
| E-Commerce | Customer |
| Banking | Account |
| Hospital | Patient |
| School | Student |

---

# 2. Value Object

# What is Value Object?

A Value Object:

- Has NO identity
- Immutable
- Compared by values

---

# Example

```csharp
public class Address
{
    public string City { get; }

    public string State { get; }

    public string ZipCode { get; }

    public Address(
        string city,
        string state,
        string zipCode)
    {
        City = city;
        State = state;
        ZipCode = zipCode;
    }
}
```

---

# Why Value Object?

Because:

```text
Two addresses with same values are same.
```

Identity does not matter.

---

# Value Object Characteristics

| Feature | Value Object |
|---|---|
| Identity | ❌ |
| Immutable | ✅ |
| Equality by Values | ✅ |
| Small Concept | ✅ |

---

# Examples of Value Objects

| Domain | Value Object |
|---|---|
| E-Commerce | Address |
| Banking | Currency |
| Travel | Location |
| HR | Email |

---

# Entity vs Value Object

| Feature | Entity | Value Object |
|---|---|---|
| Identity | Yes | No |
| Mutable | Usually | Immutable |
| Equality | By ID | By Values |
| Lifecycle | Exists | Temporary |

---

# 3. Aggregate

# What is Aggregate?

An Aggregate is a group of related entities/value objects treated as ONE unit.

---

# Example

```text
Order
   ├── OrderItems
   ├── Address
   ├── PaymentInfo
```

---

# Why Aggregate?

To maintain:

- Consistency
- Transaction boundaries
- Business rules

---

# 4. Aggregate Root

# What is Aggregate Root?

Main entity controlling aggregate access.

---

# Example

```text
Order → Aggregate Root
```

Other objects accessed only through Order.

---

# Example

```csharp
public class Order
{
    private readonly List<OrderItem> _items;

    public void AddItem(Product product)
    {
    }
}
```

---

# Rule

❌ Direct access to child entities

✅ Access via aggregate root

---

# 5. Repository

# What is Repository?

Repository abstracts data access.

DDD prefers repositories over direct DbContext usage.

---

# Example

```csharp
public interface IOrderRepository
{
    Task<Order> GetByIdAsync(Guid id);

    Task AddAsync(Order order);
}
```

---

# Why Repository?

Benefits:

- Loose coupling
- Testability
- Domain-focused access
- Hides database details

---

# Repository Implementation

```csharp
public class OrderRepository : IOrderRepository
{
    private readonly AppDbContext _context;

    public OrderRepository(AppDbContext context)
    {
        _context = context;
    }
}
```

---

# 6. Domain Service

# What is Domain Service?

Business logic that does not naturally belong to one entity.

---

# Example

```csharp
public class PaymentService
{
    public bool ProcessPayment(Order order)
    {
        return true;
    }
}
```

---

# Why Domain Service?

Because payment logic may involve:

- Order
- Bank
- Transaction
- Validation

Not suitable inside one entity.

---

# 7. Application Service

# What is Application Service?

Coordinates use cases.

Acts between:

```text
Controller ↔ Domain
```

---

# Responsibilities

- Transaction handling
- Orchestration
- Calling repositories
- Calling domain services

---

# Example

```csharp
public class OrderApplicationService
{
    public async Task PlaceOrder()
    {
    }
}
```

---

# 8. Domain Event

# What is Domain Event?

Represents something important happened in business domain.

---

# Examples

```text
OrderPlaced
PaymentCompleted
UserRegistered
```

---

# Example

```csharp
public class OrderPlacedEvent
{
    public Guid OrderId { get; set; }
}
```

---

# Benefits

- Loose coupling
- Async processing
- Event-driven architecture

---

# 9. Bounded Context

# What is Bounded Context?

Logical boundary for specific business domain.

---

# Example

```text
Inventory Context
Payment Context
Shipping Context
Customer Context
```

Each context has own:

- Rules
- Models
- Terminology

---

# Why Important?

Same word may mean different things.

Example:

```text
Customer
```

In billing:

- Paying customer

In support:

- Service ticket owner

---

# 10. Ubiquitous Language

# What is Ubiquitous Language?

Shared language between:

- Developers
- Business experts
- Stakeholders

---

# Example

Everyone uses same terms:

```text
Order
Cart
Shipment
Invoice
```

Avoid technical confusion.

---

# DDD Layers

# Common DDD Architecture

```text
Presentation Layer
        ↓
Application Layer
        ↓
Domain Layer
        ↓
Infrastructure Layer
```

---

# Domain Layer

Contains:

- Entities
- Value Objects
- Aggregates
- Domain Services
- Repository Interfaces

---

# Infrastructure Layer

Contains:

- EF Core
- Database access
- Repository implementation
- External APIs

---

# Application Layer

Contains:

- Use cases
- DTOs
- Orchestration

---

# Presentation Layer

Contains:

- Controllers
- APIs
- UI

---

# DDD Folder Structure Example

```text
src
 ├── Domain
 │     ├── Entities
 │     ├── ValueObjects
 │     ├── Aggregates
 │     ├── Repositories
 │     ├── Events
 │     └── Services
 │
 ├── Application
 │     ├── DTOs
 │     ├── Interfaces
 │     └── Services
 │
 ├── Infrastructure
 │     ├── Persistence
 │     ├── Repositories
 │     └── ExternalServices
 │
 └── API
       ├── Controllers
       └── Middleware
```

---

# DDD with EF Core

| DDD Concept | EF Core Mapping |
|---|---|
| Entity | Table |
| Value Object | Owned Entity |
| Repository | Db Access |
| Aggregate | Transaction Boundary |

---

# Example Value Object in EF Core

```csharp
modelBuilder.Entity<Customer>()
    .OwnsOne(x => x.Address);
```

---

# Advantages of DDD

| Benefit | Description |
|---|---|
| Maintainability | Cleaner architecture |
| Scalability | Easy modular growth |
| Business Focus | Better domain modeling |
| Testability | Loosely coupled |
| Flexibility | Easier changes |

---

# Disadvantages of DDD

| Problem | Description |
|---|---|
| Complexity | Hard for beginners |
| Overengineering | Bad for small apps |
| Learning Curve | High |
| More Code | Many abstractions |

---

# DDD vs Traditional CRUD

| Traditional CRUD | DDD |
|---|---|
| Database-centric | Business-centric |
| Simple logic | Rich domain logic |
| Fast development | Better maintainability |
| Small apps | Enterprise apps |

---

# Interview Questions

# What is DDD?

DDD is a software design approach focused on modeling business domains and business logic.

---

# Difference Between Entity and Value Object?

| Entity | Value Object |
|---|---|
| Has identity | No identity |
| Mutable | Immutable |
| Equality by ID | Equality by values |

---

# What is Aggregate Root?

Main entity controlling aggregate consistency and access.

---

# Why Repository Pattern in DDD?

To abstract persistence and keep domain independent of database.

---

# What is Bounded Context?

Logical boundary separating domain models and business rules.

---

# Easy Memory Trick

```text
Entity → Has Identity

Value Object → Has Value

Aggregate → Group

Aggregate Root → Controller

Repository → Data Access

Domain Service → Business Logic

Application Service → Use Case

Domain Event → Something Happened

Bounded Context → Domain Boundary
```
