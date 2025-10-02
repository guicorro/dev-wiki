# Inyección de Dependencias en .NET

## Conceptos Básicos

La inyección de dependencias (DI) es un patrón de diseño fundamental en .NET que permite:

- Reducir el acoplamiento entre clases
- Mejorar la testeabilidad
- Facilitar el mantenimiento del código

## Ciclos de Vida de los Servicios

### 1. Transient

- Se crea una nueva instancia cada vez que se solicita
- Ideal para servicios ligeros y sin estado

```csharp
services.AddTransient<IMyService, MyService>();
```

### 2. Scoped

- Se crea una instancia por scope (por ejemplo, por request en una aplicación web)
- Útil para servicios que necesitan mantener estado durante una request

```csharp
services.AddScoped<IMyService, MyService>();
```

### 3. Singleton

- Se crea una única instancia para toda la aplicación
- Usar con precaución, ideal para servicios sin estado que son costosos de crear

```csharp
services.AddSingleton<IMyService, MyService>();
```

## Buenas Prácticas

### 1. Constructor Injection

```csharp
public class OrderService
{
    private readonly IRepository _repository;
    private readonly ILogger<OrderService> _logger;

    public OrderService(IRepository repository, ILogger<OrderService> logger)
    {
        _repository = repository;
        _logger = logger;
    }
}
```

### 2. Registro de Servicios por Interfaz

```csharp
services.AddScoped<IOrderService, OrderService>();
services.AddScoped<IRepository, SqlRepository>();
```

### 3. Evitar Service Locator

❌ Malo:

```csharp
var service = serviceProvider.GetService<IMyService>();
```

✅ Bueno:

```csharp
public class MyClass
{
    private readonly IMyService _service;

    public MyClass(IMyService service)
    {
        _service = service;
    }
}
```

## Casos de Uso Avanzados

### 1. Inyección de Múltiples Implementaciones

```csharp
services.AddScoped<INotifier, EmailNotifier>();
services.AddScoped<INotifier, SmsNotifier>();

public class NotificationService
{
    private readonly IEnumerable<INotifier> _notifiers;

    public NotificationService(IEnumerable<INotifier> notifiers)
    {
        _notifiers = notifiers;
    }
}
```

### 2. Factory Pattern con DI

```csharp
services.AddScoped<Func<string, IService>>(serviceProvider => key =>
{
    return key switch
    {
        "A" => serviceProvider.GetService<ServiceA>(),
        "B" => serviceProvider.GetService<ServiceB>(),
        _ => throw new ArgumentException("Invalid key")
    };
});
```

### 3. Decorador Pattern

```csharp
services.AddScoped<IRepository, Repository>();
services.Decorate<IRepository, CachingRepositoryDecorator>();
services.Decorate<IRepository, LoggingRepositoryDecorator>();
```

## Resolución de Problemas Comunes

### 1. Circular Dependencies

❌ Evitar:

```csharp
public class A
{
    public A(B b) { }
}

public class B
{
    public B(A a) { }
}
```

✅ Solución:

- Rediseñar las clases
- Usar un evento mediator
- Implementar el patrón observer

### 2. Disposable Services

```csharp
public class MyService : IDisposable
{
    public void Dispose()
    {
        // Cleanup code
    }
}

// Registrar como transient o scoped
services.AddScoped<MyService>();
```

## Testing con DI

### 1. Mock de Dependencias

```csharp
[Fact]
public void Test_With_Mocked_Dependencies()
{
    // Arrange
    var mockRepository = new Mock<IRepository>();
    var service = new OrderService(mockRepository.Object);

    // Act & Assert
    // ...
}
```

### 2. Test Container Configuration

```csharp
public class TestStartup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddScoped<IRepository, FakeRepository>();
        services.AddScoped<IOrderService, OrderService>();
    }
}
```

## Referencias

- [Documentación oficial de MS](https://docs.microsoft.com/es-es/aspnet/core/fundamentals/dependency-injection)
- [Clean DI en .NET](https://www.milanjovanovic.tech/blog/dependency-injection-in-dotnet)
