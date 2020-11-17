# Entity Framework - Migrations

In real world projects, data models change as features get implemented: new entities or properties are added and removed, and database schemas needs to be changed accordingly to be kept in sync with the application. The migrations feature in EF Core provides a way to incrementally update the database schema to keep it in sync with the application's data model while preserving existing data in the database.

At a high level, migrations function in the following way:

* When a data model change is introduced, the developer uses EF Core tools to add a corresponding migration describing the updates necessary to keep the database schema in sync. EF Core compares the current model against a snapshot of the old model to determine the differences, and generates migration source files; the files can be tracked in your project's source control like any other source file.
* Once a new migration has been generated, it can be applied to a database in various ways. EF Core records all applied migrations in a special history table, allowing it to know which migrations have been applied and which haven't.

### Installing the tools

`dotnet ef` can be installed as either a global or local tool. Most developers prefer installing `dotnet ef` as a global tool using the following command:.NET Core CLICopy

```text
dotnet tool install --global dotnet-ef
```

Via nuget

```text
Microsoft.EntityFrameworkCore.Design (3.1.10)
```

### Create your first migration

You're now ready to add your first migration! Instruct EF Core to create a migration named **InitialCreate**:

```text
dotnet ef migrations add InitialCreate
```

EF Core will create a directory called **Migrations** in your project, and generate some files. It's a good idea to inspect what exactly EF Core generated - and possibly amend it - but we'll skip over that for now.

![](../.gitbook/assets/image%20%2885%29.png)

### Create your database and schema

Zorg ervoor dat je database gedeleted is!

At this point you can have EF create your database and create your schema from the migration. This can be done via the following:

```text
dotnet ef database update
```

That's all there is to it - your application is ready to run on your new database, and you didn't need to write a single line of SQL. Note that this way of applying migrations is ideal for local development, but is less suitable for production environments - see the [Applying Migrations page](https://docs.microsoft.com/en-us/ef/core/managing-schemas/migrations/applying) for more info.

![](../.gitbook/assets/image%20%2884%29.png)

### Initial Seed Data

Verwijder de EnsureCreated

```csharp
public ProductsDbRepository(StoreContext storeContext)
{
    this.storeContext = storeContext;
}
```

voeg toe OnModelCreating

```text
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Product>().HasData(
        new Product { Id = 1, Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M, ImageURL = "~/images/products/1.jpg", Type = ProductType.StuffedAnimal },
        new Product { Id = 2, Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M, ImageURL = "~/images/products/2.jpg", Type = ProductType.StuffedAnimal },
        new Product { Id = 3, Name = "World of Llamacraft", Description = "An online RPG about llamas", Price = 59.99M, ImageURL = "~/images/products/3.png", Type = ProductType.ComputerGame },
        new Product { Id = 4, Name = "L.A.M.A", Description = "Llama card game", Price = 9.99M, ImageURL = "~/images/products/4.jpg", Type = ProductType.BoardGame },
        new Product { Id = 5, Name = "Altiplano", Description = "Flying llamas for everyone", Price = 12.99M, ImageURL = "~/images/products/5.jpg", Type = ProductType.BoardGame },
        new Product { Id = 6, Name = "Llamas unleashed", Description = "A fast paced llama game about crazy llamas", Price = 12.99M, ImageURL = "~/images/products/6.jpg", Type = ProductType.BoardGame },
        new Product { Id = 7, Name = "Leipe lama's", Description = "A card game about very dodgy llamas", Price = 12.99M, ImageURL = "~/images/products/7.jpg", Type = ProductType.BoardGame },
        new Product { Id = 8, Name = "Llama drama", Description = "Llamas can cause drama too", Price = 39.99M, ImageURL = "~/images/products/8.jpg", Type = ProductType.BoardGame },
        new Product { Id = 9, Name = "Llama lineup", Description = "Whodunnit game about llamas", Price = 20.99M, ImageURL = "~/images/products/9.jpg", Type = ProductType.BoardGame },
        new Product { Id = 10, Name = "VALA", Description = "Vicious llama apocalypse", Price = 49.99M, ImageURL = "~/images/products/10.jpg", Type = ProductType.ComputerGame },
        new Product { Id = 11, Name = "Booty shaking llama", Description = "a booty shaking llama", Price = 12.99M, ImageURL = "~/images/products/11.jpg", Type = ProductType.PlasticToy },
        new Product { Id = 12, Name = "Furreal llama", Description = "Furreal llama", Price = 12.99M, ImageURL = "~/images/products/12.jpg", Type = ProductType.StuffedAnimal },
        new Product { Id = 13, Name = "Fortnite llama", Description = "Fortnite llama", Price = 22.99M, ImageURL = "~/images/products/13.jpg", Type = ProductType.StuffedAnimal },
        new Product { Id = 14, Name = "Lego Llama", Description = "Lego llama", Price = 22.99M, ImageURL = "~/images/products/14.jpg", Type = ProductType.StuffedAnimal }
    );
}
```

Voer uit

```text
dotnet ef migrations add SeedData
```

![](../.gitbook/assets/image%20%2882%29.png)

### Evolving your model

A few days have passed, and you're asked to add a creation timestamp to your blogs. You've done the necessary changes to your application, and your model now looks like this. Added CreatedTimeStamp

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    [Column(TypeName = "decimal(4,2)")]
    public decimal Price { get; set; }
    public string ImageURL { get; set; }
    public ProductType Type { get; set; }
    public DateTime CreatedTimestamp { get; set; }
}
```

Bij de `Create` action voegen we nu ook nog een CreatedTimestamp die we op DateTime.Now zetten 

```text
[HttpPost]
public IActionResult Create(ProductCreateViewModel productCreateViewModel)
{
...
        Product product = new Product
        {
            Description = productCreateViewModel.Description,
            Name = productCreateViewModel.Name,
            Type = productCreateViewModel.Type,
            ImageURL = productCreateViewModel.ImageURL,
            Price = productCreateViewModel.Price,
            CreatedTimestamp = DateTime.Now
        };
...
}
```

Als we nu de applicatie opstarten en naar de producten gaan kijken loopt er nog iets mis:

![](../.gitbook/assets/image%20%2883%29.png)

zetten in StoreContext alle Products een CreatedTimestamp op 01/01/2020.

```text
dotnet ef migrations add AddedTimestamp
```

uitvoeren alle migrations

```text
dotnet ef database update
```



