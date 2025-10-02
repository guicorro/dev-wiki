# Arquitecturas en .NET

## Clean Architecture

### Principios Fundamentales

1. **Independencia de Frameworks**
2. **Testeable**
3. **Independencia de la UI**
4. **Independencia de la Base de Datos**
5. **Independencia de cualquier agente externo**

### Capas

```
┌──────────────────────────┐
│    Enterprise Business   │
│         Entities        │
├──────────────────────────┤
│    Application Business  │
│       Use Cases         │
├──────────────────────────┤
│    Interface Adapters   │
│   Controllers/Gateways  │
├──────────────────────────┤
│     Frameworks and      │
│       Drivers          │
└──────────────────────────┘
```

### Implementación en .NET

#### 1. Domain Layer

```csharp
public class Order
{
    public Guid Id { get; private set; }
    public string CustomerName { get; private set; }
    public List<OrderLine> Lines { get; private set; }
    public OrderStatus Status { get; private set; }

    public void AddLine(Product product, int quantity)
    {
        // Lógica del dominio
    }

    public decimal CalculateTotal()
    {
        // Cálculos del dominio
    }
}
```

#### 2. Application Layer

```csharp
public class CreateOrderUseCase
{
    private readonly IOrderRepository _orderRepository;
    private readonly IProductRepository _productRepository;

    public CreateOrderUseCase(
        IOrderRepository orderRepository,
        IProductRepository productRepository)
    {
        _orderRepository = orderRepository;
        _productRepository = productRepository;
    }

    public async Task<OrderDto> Execute(CreateOrderCommand command)
    {
        // Lógica de aplicación
    }
}
```

#### 3. Infrastructure Layer

```csharp
public class OrderRepository : IOrderRepository
{
    private readonly ApplicationDbContext _context;

    public OrderRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<Order> GetById(Guid id)
    {
        return await _context.Orders
            .Include(o => o.Lines)
            .FirstOrDefaultAsync(o => o.Id == id);
    }
}
```

## Vertical Slice Architecture

### Características

- Organización por funcionalidad
- Minimiza la abstracción innecesaria
- Facilita los cambios
- Mejor para equipos autónomos

### Estructura de Proyecto

```
src/
├── Features/
│   ├── Orders/
│   │   ├── Create/
│   │   ├── List/
│   │   └── Cancel/
│   └── Products/
│       ├── Search/
│       └── Update/
├── Infrastructure/
└── Shared/
```

### Ejemplo de Implementación

#### Feature: Crear Orden

```csharp
public class CreateOrder
{
    public record Command(
        string CustomerName,
        List<OrderLineDto> Lines);

    public record OrderLineDto(
        Guid ProductId,
        int Quantity);

    public class Handler : IRequestHandler<Command, Guid>
    {
        private readonly ApplicationDbContext _context;

        public Handler(ApplicationDbContext context)
        {
            _context = context;
        }

        public async Task<Guid> Handle(
            Command command,
            CancellationToken cancellationToken)
        {
            // Implementación específica del feature
        }
    }
}
```

## CQRS (Command Query Responsibility Segregation)

### Principios

- Separación de modelos de lectura y escritura
- Optimización independiente
- Escalabilidad diferenciada

### Implementación

#### Commands

```csharp
public record CreateOrderCommand(
    string CustomerName,
    List<OrderLineDto> Lines);

public class CreateOrderCommandHandler : ICommandHandler<CreateOrderCommand>
{
    private readonly IOrderRepository _repository;

    public CreateOrderCommandHandler(IOrderRepository repository)
    {
        _repository = repository;
    }

    public async Task Handle(CreateOrderCommand command)
    {
        var order = new Order(command.CustomerName);
        foreach (var line in command.Lines)
        {
            order.AddLine(line.ProductId, line.Quantity);
        }

        await _repository.SaveAsync(order);
    }
}
```

#### Queries

```csharp
public record GetOrderQuery(Guid OrderId);

public class GetOrderQueryHandler : IQueryHandler<GetOrderQuery, OrderDto>
{
    private readonly IReadOnlyOrderRepository _repository;

    public GetOrderQueryHandler(IReadOnlyOrderRepository repository)
    {
        _repository = repository;
    }

    public async Task<OrderDto> Handle(GetOrderQuery query)
    {
        return await _repository.GetOrderDtoAsync(query.OrderId);
    }
}
```

## Event Sourcing

### Conceptos

- Almacenamiento de eventos en lugar de estado
- Reconstrucción del estado
- Auditoría completa

### Implementación

```csharp
public abstract class AggregateRoot
{
    private readonly List<DomainEvent> _changes = new();

    public Guid Id { get; protected set; }
    public int Version { get; private set; }

    public IReadOnlyList<DomainEvent> GetUncommittedChanges()
    {
        return _changes.AsReadOnly();
    }

    protected void ApplyChange(DomainEvent @event)
    {
        ApplyChange(@event, true);
    }

    protected void ApplyChange(DomainEvent @event, bool isNew)
    {
        this.AsDynamic().Apply(@event);
        if (isNew)
        {
            _changes.Add(@event);
        }
    }
}

public class Order : AggregateRoot
{
    public void Apply(OrderCreatedEvent @event)
    {
        Id = @event.OrderId;
        CustomerName = @event.CustomerName;
    }

    public void Apply(ProductAddedToOrderEvent @event)
    {
        Lines.Add(new OrderLine(@event.ProductId, @event.Quantity));
    }
}
```

## Arquitectura de Microservicios

### Características

- Servicios independientes
- Base de datos por servicio
- Comunicación vía mensajes/eventos

### Ejemplo de Microservicio

```csharp
public class OrdersService
{
    private readonly IEventBus _eventBus;
    private readonly IOrderRepository _repository;

    public OrdersService(
        IEventBus eventBus,
        IOrderRepository repository)
    {
        _eventBus = eventBus;
        _repository = repository;
    }

    public async Task CreateOrder(CreateOrderCommand command)
    {
        var order = new Order(command.CustomerId);
        await _repository.SaveAsync(order);

        await _eventBus.PublishAsync(new OrderCreatedEvent(order.Id));
    }
}
```

### Comunicación entre Servicios

```csharp
public class OrderCreatedEventHandler : IEventHandler<OrderCreatedEvent>
{
    private readonly IInventoryService _inventoryService;

    public OrderCreatedEventHandler(IInventoryService inventoryService)
    {
        _inventoryService = inventoryService;
    }

    public async Task Handle(OrderCreatedEvent @event)
    {
        await _inventoryService.ReserveInventory(@event.OrderId);
    }
}
```

## Mejores Prácticas

1. **Elegir la arquitectura adecuada**

   - Tamaño del proyecto
   - Complejidad del dominio
   - Equipo de desarrollo
   - Requisitos de escalabilidad

2. **Mantener la consistencia**

   - Convenciones de nombres
   - Estructura de carpetas
   - Patrones de diseño

3. **Documentación**
   - Decisiones arquitectónicas
   - Patrones utilizados
   - Guías de implementación

## Referencias

- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [CQRS Journey](<https://docs.microsoft.com/es-es/previous-versions/msp-n-p/jj554200(v=pandp.10)>)
- [Vertical Slice Architecture](https://www.milanjovanovic.tech/blog/vertical-slice-architecture)
- [Event Sourcing Pattern](https://docs.microsoft.com/es-es/azure/architecture/patterns/event-sourcing)
