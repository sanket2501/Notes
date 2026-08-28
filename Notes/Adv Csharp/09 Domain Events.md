# 09. Domain Events

Tags: dotnet

A domain event represents a meaningful business change in the problem domain. It is typically used in Domain-Driven Design (DDD) to notify other parts of the system that something important happened, such as a customer placing an order or a payment being approved.

## Table of Contents

- [What is a domain event](#what-is-a-domain-event)
- [Why they matter](#why-they-matter)
- [Example pattern](#example-pattern)
- [Real-world example](#real-world-example)
- [Best practices](#best-practices)

## What is a domain event

A domain event is a business fact that occurred within the domain model.

Examples:

- `OrderPlaced`
- `InvoiceGenerated`
- `CustomerActivated`
- `InventoryReduced`

These events are not just generic technical notifications. They carry business meaning.

## Why they matter

Domain events help decouple the system.

Instead of putting all logic directly into one class, an event can trigger additional behavior such as:

- sending an email
- updating inventory
- writing to audit logs
- triggering notifications or workflows

This keeps the domain model focused and flexible.

## Example pattern

```csharp
public record OrderPlacedEvent(int OrderId, string CustomerName, DateTime OccurredOn);

public class Order
{
    public int Id { get; set; }
    public string CustomerName { get; set; }

    public event EventHandler<OrderPlacedEvent>? OrderPlaced;

    public void PlaceOrder()
    {
        var e = new OrderPlacedEvent(Id, CustomerName, DateTime.UtcNow);
        OrderPlaced?.Invoke(this, new OrderPlacedEventArgs(e));
    }
}

public class OrderPlacedEventArgs : EventArgs
{
    public OrderPlacedEvent Event { get; }

    public OrderPlacedEventArgs(OrderPlacedEvent e)
    {
        Event = e;
    }
}
```

This is a clean way to notify others when a business action occurs.

## Real-world example

### Order processing workflow

```csharp
public record OrderPlacedEvent(int OrderId, string CustomerName, DateTime OccurredOn);

public class OrderService
{
    public event EventHandler<OrderPlacedEvent>? OnOrderPlaced;

    public void PlaceOrder(int orderId, string customerName)
    {
        Console.WriteLine($"Order {orderId} created for {customerName}");
        OnOrderPlaced?.Invoke(this, new OrderPlacedEvent(orderId, customerName, DateTime.UtcNow));
    }
}

public class InventoryService
{
    public void Subscribe(OrderService service)
    {
        service.OnOrderPlaced += (_, e) =>
        {
            Console.WriteLine($"Inventory updated for order {e.OrderId}");
        };
    }
}

public class EmailService
{
    public void Subscribe(OrderService service)
    {
        service.OnOrderPlaced += (_, e) =>
        {
            Console.WriteLine($"Sending confirmation email to {e.CustomerName}");
        };
    }
}
```

Usage:

```csharp
var orderService = new OrderService();
var inventoryService = new InventoryService();
var emailService = new EmailService();

inventoryService.Subscribe(orderService);
emailService.Subscribe(orderService);

orderService.PlaceOrder(101, "Amit");
```

This is a realistic example of a business action triggering several workflows after the event is raised.

## Domain event characteristics

Good domain events are usually:

- meaningful to the business
- immutable
- descriptive and specific
- published by domain logic rather than infrastructure code

Example:

```csharp
public record CustomerActivatedEvent(int CustomerId, DateTime ActivatedOn);
```

This clearly describes the domain change and carries exactly the relevant data.

## Best practices

- Keep event names business-focused and clear.
- Store only the data needed by handlers.
- Make events immutable when possible.
- Avoid mixing domain events with database or HTTP concerns.
- Use events to coordinate workflows, not as a replacement for actual business logic.

## Summary

Domain events are a strong pattern for building maintainable, decoupled business systems. They allow operations such as order placement, payment processing, or user activation to trigger additional reactions without forcing all logic into a single class.
