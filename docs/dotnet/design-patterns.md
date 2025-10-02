# Patrones de Diseño en .NET

## SOLID Principles

### Single Responsibility (SRP)

Una clase debe tener una única razón para cambiar.

✅ Bueno:

```csharp
public class OrderProcessor
{
    private readonly IEmailService _emailService;
    private readonly IInventoryService _inventoryService;

    public OrderProcessor(IEmailService emailService, IInventoryService inventoryService)
    {
        _emailService = emailService;
        _inventoryService = inventoryService;
    }

    public async Task ProcessOrder(Order order)
    {
        await _inventoryService.ReserveItems(order.Items);
        await _emailService.SendOrderConfirmation(order);
    }
}
```

### Open/Closed (OCP)

Las entidades deben estar abiertas para extensión pero cerradas para modificación.

```csharp
public interface IDiscountStrategy
{
    decimal CalculateDiscount(Order order);
}

public class SeasonalDiscount : IDiscountStrategy
{
    public decimal CalculateDiscount(Order order) => order.Total * 0.2m;
}

public class VolumeDiscount : IDiscountStrategy
{
    public decimal CalculateDiscount(Order order) =>
        order.Items.Count > 10 ? order.Total * 0.1m : 0;
}
```

### Liskov Substitution (LSP)

Los objetos de una clase derivada deben poder sustituir a los objetos de la clase base.

```csharp
public abstract class Bird
{
    public abstract void Move();
}

public class FlyingBird : Bird
{
    public override void Move()
    {
        // Implementación del vuelo
    }
}

public class WalkingBird : Bird
{
    public override void Move()
    {
        // Implementación del caminar
    }
}
```

### Interface Segregation (ISP)

Los clientes no deben depender de interfaces que no usan.

❌ Malo:

```csharp
public interface IRepository
{
    Task<T> GetById<T>(int id);
    Task<IEnumerable<T>> GetAll<T>();
    Task<T> Add<T>(T entity);
    Task Update<T>(T entity);
    Task Delete<T>(int id);
    Task<int> Count<T>();
    Task<bool> Exists<T>(int id);
}
```

✅ Bueno:

```csharp
public interface IReadRepository<T>
{
    Task<T> GetById(int id);
    Task<IEnumerable<T>> GetAll();
}

public interface IWriteRepository<T>
{
    Task<T> Add(T entity);
    Task Update(T entity);
    Task Delete(int id);
}
```

### Dependency Inversion (DIP)

Los módulos de alto nivel no deben depender de módulos de bajo nivel.

```csharp
// Abstracción
public interface IMessageSender
{
    void Send(string message);
}

// Implementación de bajo nivel
public class EmailSender : IMessageSender
{
    public void Send(string message)
    {
        // Implementación del envío por email
    }
}

// Clase de alto nivel
public class NotificationService
{
    private readonly IMessageSender _messageSender;

    public NotificationService(IMessageSender messageSender)
    {
        _messageSender = messageSender;
    }

    public void NotifyUser(string message)
    {
        _messageSender.Send(message);
    }
}
```

## Patrones Creacionales

### Factory Method

```csharp
public interface IPaymentProcessor
{
    void ProcessPayment(decimal amount);
}

public class PaymentProcessorFactory
{
    public IPaymentProcessor Create(string paymentMethod)
    {
        return paymentMethod switch
        {
            "credit" => new CreditCardProcessor(),
            "paypal" => new PayPalProcessor(),
            _ => throw new ArgumentException("Invalid payment method")
        };
    }
}
```

### Builder

```csharp
public class EmailBuilder
{
    private Email _email = new Email();

    public EmailBuilder WithSubject(string subject)
    {
        _email.Subject = subject;
        return this;
    }

    public EmailBuilder WithBody(string body)
    {
        _email.Body = body;
        return this;
    }

    public EmailBuilder WithRecipient(string recipient)
    {
        _email.Recipient = recipient;
        return this;
    }

    public Email Build() => _email;
}
```

## Patrones Estructurales

### Decorator

```csharp
public interface ILogger
{
    void Log(string message);
}

public class TimestampDecorator : ILogger
{
    private readonly ILogger _logger;

    public TimestampDecorator(ILogger logger)
    {
        _logger = logger;
    }

    public void Log(string message)
    {
        var timestampedMessage = $"{DateTime.Now}: {message}";
        _logger.Log(timestampedMessage);
    }
}
```

## Patrones de Comportamiento

### Strategy

```csharp
public interface ISortStrategy
{
    IList<T> Sort<T>(IList<T> list) where T : IComparable;
}

public class QuickSortStrategy : ISortStrategy
{
    public IList<T> Sort<T>(IList<T> list) where T : IComparable
    {
        // Implementación QuickSort
        return list;
    }
}

public class BubbleSortStrategy : ISortStrategy
{
    public IList<T> Sort<T>(IList<T> list) where T : IComparable
    {
        // Implementación BubbleSort
        return list;
    }
}
```

### Observer

```csharp
public interface IObserver
{
    void Update(string message);
}

public class NewsAgency
{
    private List<IObserver> _observers = new();

    public void Attach(IObserver observer)
    {
        _observers.Add(observer);
    }

    public void NotifyObservers(string news)
    {
        foreach (var observer in _observers)
        {
            observer.Update(news);
        }
    }
}
```

## Referencias

- [Refactoring Guru](https://refactoring.guru/design-patterns)
- [Clean Code en .NET](https://www.milanjovanovic.tech/blog/clean-code-principles)
- [Martin Fowler](https://martinfowler.com/articles/patterns-of-distributed-systems/)
