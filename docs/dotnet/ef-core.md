# Entity Framework Core

## Migraciones Code First

Las migraciones en EF Core permiten mantener el esquema de la base de datos sincronizado con el modelo de datos de tu aplicación.

### Requisitos Previos

1. Instalar las herramientas de EF Core:

```bash
dotnet tool install --global dotnet-ef
```

2. Agregar los paquetes necesarios al proyecto:

```bash
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.EntityFrameworkCore.Tools
```

### Comandos Básicos

#### Crear una nueva migración

```bash
dotnet ef migrations add NombreMigracion
```

Ejemplo:

```bash
dotnet ef migrations add InitialCreate
dotnet ef migrations add AddProductTable
dotnet ef migrations add UpdateProductFields
```

#### Aplicar migraciones

```bash
# Aplicar todas las migraciones pendientes
dotnet ef database update

# Aplicar hasta una migración específica
dotnet ef database update NombreMigracion
```

#### Revertir migraciones

```bash
# Revertir la última migración
dotnet ef database update NombreMigracionAnterior

# Revertir todas las migraciones
dotnet ef database update 0
```

#### Listar migraciones

```bash
dotnet ef migrations list
```

#### Eliminar la última migración

```bash
dotnet ef migrations remove
```

### Ejemplo Práctico

1. Crear el modelo:

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```

2. Crear el DbContext:

```csharp
public class StoreContext : DbContext
{
    public DbSet<Product> Products { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("tu_cadena_de_conexion");
    }
}
```

3. Crear y aplicar la migración:

```bash
dotnet ef migrations add CreateProductTable
dotnet ef database update
```

4. Modificar el modelo:

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    public string Description { get; set; }  // Nuevo campo
    public bool IsAvailable { get; set; }    // Nuevo campo
}
```

5. Crear una nueva migración para los cambios:

```bash
dotnet ef migrations add AddProductDetails
dotnet ef database update
```

### Buenas Prácticas

1. **Nombres descriptivos**: Usa nombres claros para las migraciones que indiquen su propósito.

   ```bash
   dotnet ef migrations add AddProductCategoryRelation
   ```

2. **Revisión de migraciones**: Siempre revisa el código generado en la carpeta Migrations antes de aplicarlo.

3. **Control de versiones**: Incluye las migraciones en el control de versiones.

4. **Entornos**: Usa diferentes cadenas de conexión para desarrollo y producción.

   ```csharp
   optionsBuilder.UseSqlServer(Configuration.GetConnectionString("DefaultConnection"));
   ```

5. **Backup**: Realiza copias de seguridad antes de aplicar migraciones en producción.

### Solución de Problemas Comunes

1. **Error de migración pendiente**:

   ```bash
   dotnet ef database update --verbose
   ```

2. **Resetear la base de datos**:

   ```bash
   dotnet ef database drop
   dotnet ef database update
   ```

3. **Generar script SQL**:

   ```bash
   dotnet ef migrations script
   ```

4. **Migración específica**:
   ```bash
   dotnet ef migrations script MigracionOrigen MigracionDestino
   ```

### Referencias

- [Documentación oficial de EF Core](https://docs.microsoft.com/es-es/ef/core/managing-schemas/migrations/)
- [Guía de migraciones](https://docs.microsoft.com/es-es/ef/core/managing-schemas/migrations/?tabs=dotnet-core-cli)
