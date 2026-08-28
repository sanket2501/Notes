# 05. Delegates, Lambdas, and Events

Tags: dotnet

Delegates are a core C# feature that allow methods to be treated as values. This makes it possible to pass behavior around as parameters, define callback methods, and build event-driven systems. Lambdas are a concise way to write delegate-based logic, while events provide a clean observer pattern for notifying subscribers.

## Table of Contents

- [What is a delegate](#what-is-a-delegate)
- [Single-cast delegate](#single-cast-delegate)
- [Multicast delegate](#multicast-delegate)
- [Lambda expressions](#lambda-expressions)
- [Func, Action, and Predicate](#func-action-and-predicate)
- [Events](#events)
- [Real-world examples](#real-world-examples)

## What is a delegate

A delegate defines a method signature. It can point to a method that matches that signature.

```csharp
public delegate int MathOperation(int x, int y);

public class Calculator
{
    public static int Add(int x, int y) => x + y;
    public static int Multiply(int x, int y) => x * y;
}

public class Program
{
    public static void Main()
    {
        MathOperation operation = Calculator.Add;
        Console.WriteLine(operation(10, 20)); // 30
    }
}
```

This is useful when you want to pass custom logic into another method.

## Single-cast delegate

```csharp
public delegate void GreetDelegate(string name);

public class Greeter
{
    private readonly string _prefix;

    public Greeter(string prefix)
    {
        _prefix = prefix;
    }

    public void SayHello(string name)
    {
        Console.WriteLine($"{_prefix}, {name}!");
    }
}

public class Program
{
    public static void Main()
    {
        Greeter greeter = new("Good morning");
        GreetDelegate greet = greeter.SayHello;
        greet("Alice");
    }
}
```

## Multicast delegate

A delegate can point to multiple methods using `+=` and remove them using `-=`.

```csharp
public delegate void GreetDelegate(string name);

public class Greeter
{
    public void SayHello(string name)
    {
        Console.WriteLine($"Hello, {name}!");
    }

    public void SayGoodbye(string name)
    {
        Console.WriteLine($"Goodbye, {name}!");
    }
}

public class Program
{
    public static void Main()
    {
        Greeter greeter = new();
        GreetDelegate greet = greeter.SayHello;
        greet += greeter.SayGoodbye;

        greet("Riya");
    }
}
```

This is a simple form of event-driven behavior.

## Lambda expressions

A lambda is a short, inline way to create a delegate.

```csharp
public delegate int MathOperation(int a, int b);

MathOperation multiply = (a, b) => a * b;
Console.WriteLine(multiply(4, 5)); // 20
```

This is heavily used in LINQ and callback patterns.

## Func, Action, and Predicate

These are built-in generic delegates.

```csharp
Func<int, int, int> add = (a, b) => a + b;
Console.WriteLine(add(3, 4));

Action<int, int> printSum = (a, b) => Console.WriteLine($"Sum: {a + b}");
printSum(5, 10);

Predicate<int> isEven = x => x % 2 == 0;
Console.WriteLine(isEven(8));
```

- `Func<T, TResult>`: delegate that returns a value
- `Action<T>`: delegate that performs an action and returns void
- `Predicate<T>`: delegate that returns `bool`

## Events

Events are a special kind of delegate that allows publisher/subscriber communication without direct coupling.

```csharp
public class Publisher
{
    public delegate void MyEventHandler(object sender, EventArgs e);
    public event MyEventHandler MyEvent;

    protected virtual void OnMyEvent(EventArgs e)
    {
        MyEvent?.Invoke(this, e);
    }

    public void TriggerEvent()
    {
        Console.WriteLine("Event about to be triggered");
        OnMyEvent(EventArgs.Empty);
    }
}

public class Subscriber
{
    public void HandleEvent(object sender, EventArgs e)
    {
        Console.WriteLine("Event handled by subscriber");
    }
}

public class Program
{
    public static void Main()
    {
        Publisher publisher = new();
        Subscriber subscriber = new();

        publisher.MyEvent += subscriber.HandleEvent;
        publisher.TriggerEvent();
    }
}
```

Use events in UI applications, message processing, and service integrations.

## Real-world examples

### Example 1: email notification

```csharp
public class OrderService
{
    public event EventHandler<OrderPlacedEventArgs> OrderPlaced;

    public void PlaceOrder(string customerName)
    {
        var args = new OrderPlacedEventArgs { CustomerName = customerName };
        OnOrderPlaced(args);
    }

    protected virtual void OnOrderPlaced(OrderPlacedEventArgs e)
    {
        OrderPlaced?.Invoke(this, e);
    }
}

public class OrderPlacedEventArgs : EventArgs
{
    public string CustomerName { get; set; }
}
```

When an order is placed, another part of the application can listen and send an email or update inventory.

### Example 2: generic event-driven system

```csharp
public class UserService
{
    public event EventHandler<UserEventArgs> UserSignedUp;

    public void SignUp(string username, string email)
    {
        var args = new UserEventArgs
        {
            Username = username,
            Email = email,
            SignupMethod = "email"
        };

        UserSignedUp?.Invoke(this, args);
    }
}
```

## Best practices

- Use events for notifications rather than direct method calls.
- Keep event arguments typed and meaningful.
- Use null-conditional invocation (`?.Invoke`) to avoid null reference issues.
- Prefer `event` over public delegate fields.

## Summary

Delegates, lambdas, and events are essential building blocks for flexible and decoupled C# applications. They are the foundation for callback systems, event-driven design, and many framework features in .NET.
