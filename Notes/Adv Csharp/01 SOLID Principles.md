# 01. SOLID Principles

Tags: dotnet

The SOLID principles are a set of design rules for object-oriented code. They help create maintainable, testable, and loosely coupled systems. In real-world software, these principles matter when business rules become complex and teams need to change code without causing regressions.

## Table of Contents

- [What SOLID means](#what-solid-means)
- [Single Responsibility Principle](#single-responsibility-principle)
- [Open/Closed Principle](#openclosed-principle)
- [Liskov Substitution Principle](#liskov-substitution-principle)
- [Interface Segregation Principle](#interface-segregation-principle)
- [Dependency Inversion Principle](#dependency-inversion-principle)
- [Real-world example](#real-world-example)

## What SOLID means

SOLID is an acronym for:

- Single Responsibility Principle (SRP)
- Open/Closed Principle (OCP)
- Liskov Substitution Principle (LSP)
- Interface Segregation Principle (ISP)
- Dependency Inversion Principle (DIP)

A loosely coupled design is easier to reuse, deploy, and change. This reduces hidden side effects during application evolution.

## Single Responsibility Principle

A class should have one reason to change.

Bad example:

```csharp
public class InvoiceService
{
    public void CreateInvoice(Customer customer)
    {
        // create invoice
    }

    public void SendEmail(Customer customer)
    {
        // send notification
    }

    public void SaveInvoiceToDatabase(Customer customer)
    {
        // save to DB
    }
}
```

This class has multiple responsibilities: invoice creation, email logic, and persistence. If the database changes, the class changes even when billing logic is untouched.

Better design:

```csharp
public class InvoiceService
{
    public Invoice CreateInvoice(Customer customer)
    {
        // billing logic only
        return new Invoice();
    }
}

public class EmailService
{
    public void SendInvoiceEmail(Customer customer, Invoice invoice)
    {
        // email logic only
    }
}

public class InvoiceRepository
{
    public void Save(Invoice invoice)
    {
        // persistence logic only
    }
}
```

## Open/Closed Principle

Classes should be open for extension but closed for modification.

Instead of changing old code every time a new payment type appears, use abstraction.

```csharp
public interface IPaymentProcessor
{
    void Process(decimal amount);
}

public class CreditCardProcessor : IPaymentProcessor
{
    public void Process(decimal amount)
    {
        Console.WriteLine($"Charging credit card: {amount}");
    }
}

public class PayPalProcessor : IPaymentProcessor
{
    public void Process(decimal amount)
    {
        Console.WriteLine($"Charging PayPal: {amount}");
    }
}

public class Checkout
{
    private readonly IPaymentProcessor _processor;

    public Checkout(IPaymentProcessor processor)
    {
        _processor = processor;
    }

    public void Pay(decimal amount)
    {
        _processor.Process(amount);
    }
}
```

This lets you add new payment methods without changing the checkout flow itself.

## Liskov Substitution Principle

Objects of a base type should be replaceable with objects of a derived type without breaking the program.

A classic violation is a subclass that overrides a method and throws an exception unexpectedly.

```csharp
public class Bird
{
    public virtual void Fly()
    {
        Console.WriteLine("Flying");
    }
}

public class Penguin : Bird
{
    public override void Fly()
    {
        throw new InvalidOperationException("Penguins cannot fly");
    }
}
```

This breaks LSP because `Penguin` cannot behave like a normal bird in all contexts.

Correct version:

```csharp
public class Bird
{
    public void Eat()
    {
        Console.WriteLine("Eating");
    }
}

public interface IFlyable
{
    void Fly();
}

public class Sparrow : Bird, IFlyable
{
    public void Fly()
    {
        Console.WriteLine("Sparrow is flying");
    }
}

public class Penguin : Bird
{
    public void Swim()
    {
        Console.WriteLine("Penguin is swimming");
    }
}
```

The behavior is now consistent with the domain model.

## Interface Segregation Principle

No client should be forced to depend on methods it does not use.

Avoid large interfaces that force implementations to provide unnecessary methods.

```csharp
public interface ICustomerRepository
{
    void AddCustomer();
    void UpdateCustomer();
    void DeleteCustomer();
}
```

This is too broad when only read operations are needed.

Instead:

```csharp
public interface ICustomerReader
{
    Customer GetById(int id);
    IEnumerable<Customer> GetAll();
}

public interface ICustomerWriter
{
    void Add(Customer customer);
    void Update(Customer customer);
    void Delete(int id);
}
```

This keeps contracts specific and reduces unnecessary coupling.

## Dependency Inversion Principle

High-level modules should depend on abstractions, not concrete implementations.

```csharp
public interface IVersionControl
{
    void Commit(string message);
    void Push();
    void Pull();
}

public class GitVersionControl : IVersionControl
{
    public void Commit(string message) => Console.WriteLine($"Git commit: {message}");
    public void Push() => Console.WriteLine("Git push");
    public void Pull() => Console.WriteLine("Git pull");
}

public class DevelopmentTeam
{
    private readonly IVersionControl _versionControl;

    public DevelopmentTeam(IVersionControl versionControl)
    {
        _versionControl = versionControl;
    }

    public void Work()
    {
        _versionControl.Commit("Initial commit");
        _versionControl.Push();
        _versionControl.Pull();
    }
}
```

This allows the team to use Git, Azure Repos, or another tool by swapping implementations without changing business logic.

## Real-world example

In an e-commerce app, order processing should not directly depend on a specific payment gateway or database technology. Instead, use interfaces and inject implementations.

```csharp
public interface IOrderRepository
{
    void Save(Order order);
}

public class SqlOrderRepository : IOrderRepository
{
    public void Save(Order order)
    {
        Console.WriteLine("Order saved to SQL database");
    }
}

public class OrderService
{
    private readonly IOrderRepository _repository;

    public OrderService(IOrderRepository repository)
    {
        _repository = repository;
    }

    public void PlaceOrder(Order order)
    {
        _repository.Save(order);
    }
}
```

This pattern is the base of dependency injection and good architecture in ASP.NET Core applications.

## Summary

SOLID is a practical design toolkit. These principles help you keep code readable, stable, and easier to extend as the system grows.
