# Entity Framework - Introductie

Het Entity Framework is een Object-Relational mapping-technologie, waarmee gegevens uit een relationele database kunnen worden omgezet in objecten met gegevens die door objectgeörienteerde programmeertalen gebruikt kunnen worden.

Een belangrijke functie binnen het framework is het automatiseren van taken. Ontwikkelaars moeten niet meer zelf in hun code de conversieslag tussen database en applicatie maken. Het Entity Framework zorgt ervoor dat data op een eenduidige wijze aan de ontwikkelaar ter beschikking wordt gesteld zodat er op een abstractere manier mee gewerkt kan worden.Voordat je met EF kan beginnen, moet je de EF assemblies lokaal installeren. Microsoft's nieuwe distributiemodel is niet meer gebaseerd op de ouderwetse Windows installers, maar op nieuwe technologieën zoals [NuGet ](https://www.nuget.org/)en [Git](http://git-scm.com/).

We gaan in deze cursus werken met een code first aanpak. Dit betekent dat we niet gaan starten vanuit een bestaande database die we zelf hebben aangemaakt aan de hand van SQL queries. Dit is buiten de scope van deze cursus. Wat wij gaan doen is starten van ons eigen model en daaruit gaan we de database en zijn tabellen laten genereren.

## EF Core installeren

[NuGet](https://www.nuget.org/) staat in voor .NET pakketbeheer. Met NuGet kan je in Visual Studio projecten dependencies op software-pakketten installen, assemblies, broncode bestanden, PowerShell-scripts, enz. Die dependancies worden opgeslagen in remote repositories.

EF heeft zijn eigen assembly, en wordt los van de reguliere NET releases vrijgegeven. Om het te installeren in een bestaand project, open je de **Manage NuGet Packages for Solution**:

![](../.gitbook/assets/image%20%2877%29.png)

Hier kan je de nodige dependencies installeren

![](../.gitbook/assets/image%20%2879%29.png)

Installeer de volgende dependencies. Die zijn nodig voor EntityFramework te kunnen gebruiken met MySQL.

1. **Microsoft.EntityFrameworkCore** \(3.1.10\)
2. **Pomelo.EntityFrameworkCore.MySql** \(3.2.4\)
3. **Microsoft.EntityFrameworkCore.Tools** \(3.1.0\)

**Let op:** Installeer niet EntityFrameworkCore 5.0.0 want de MySQL driver ondersteund deze nog niet.

Als je nu in het csproj bestand gaat kijken dan zal je zien dat er voor elke dependency die we hebben geinstalleerd hebben met NuGet een extra lijn is toegevoegd die de dependency voorstelt.

```markup
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="3.1.10">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
    <PackageReference Include="Pomelo.EntityFrameworkCore.MySql" Version="3.2.4" />
  </ItemGroup>

</Project>


```

Voorlopig zijn we klaar met de NuGet tool.

## Context

De kern van het Entity Framework Code First is de System.Data.Entity.DbContext klasse. Het is deze klasse \(of, beter gezegd, de klassen die jij maakt op basis van deze klasse\) die de toegangspoort tot de database is en alle methoden biedt die je nodig hebt om met de database te werken.

Voordat een klassemodel kan worden gebruikt om een query op een database uit te voeren, moet Entity Framework weten hoe het code \(klassen, eigenschappen, en instanties\) heen en weer moeten vertalen van C\# naar SQL \(in het bijzonder, tabellen, kolommen en rijen \). Daarvoor gebruikt het ORM of object relational mapping.

Een context is een klasse die erft van DbContext en die een aantal entiteit-collecties toegankelijk maakt in de vorm van DbSet eigenschappen.

We maken een context voor onze school database genaamd `StoreContext.cs` en we plaatsen die in de `Models` map:

```csharp
using Microsoft.EntityFrameworkCore;

namespace LlamaStore.Models
{
    public class StoreContext : DbContext
    {
        public StoreContext(DbContextOptions options) : base(options)
        {

        }

        public DbSet<Product> Products { get; set; }
    }
}
```

De context klasse hierboven heeft een constructor die `DbContextOptions` als argument heeft. `dbContextOptions` bevat de configuratie informatie die nodig is om de `DbContext` te configureren. Het zal bijvoorbeeld de connection string bevatten die nodig is om met de database te verbinden.

Deze klasse zal ook een aantal `DbSet` properties bevatten voor elke entiteit \(database tabel\). In ons voorbeeld is dat dus de `Products` tabel.

## Connection Settings

Een connection string bevat:

* de mysql **server** url
* de **poort** van de server
* **database** naam 
* username
* passwoord

Deze connection string gaan we opslagen in ons configuratie bestand. Open `appsettings.json` en plaats deze connection string hier \(met de juiste login gegevens\)

```text
  "ConnectionStrings": {
    "DefaultConnection": "server=xchk.be;port=3306;database=database;user=username;password=password"
  },
```

Nu moeten we deze toegang tot de `StoreContext` die we hebben aangemaakt toevoegen aan ConfigureServices zodat we deze ook kunnen injecteren aan de hand van dependency injection.

Vooraleer we aan de configuratie kunnen in de `Startup` klasse moeten we deze ook nog wel injecteren via de constructor van de Startup klasse. Zo kunnen we ook aan het configuratie object in de `ConfigureServices` methode.

```csharp
private IConfiguration configuration;
public Startup(IConfiguration configuration)
{
    this.configuration = configuration;
}
```

nu kunnen we in de ConfigureServices

```csharp
services.AddDbContextPool<StoreContext>(
    options => options.UseMySql(configuration.GetConnectionString("DefaultConnection")));
```

toevoegen om de StoreContext injecteerbaar te maken en deze in te stellen.

We kunnen om te proberen even de `StoreContext` in de `HomeController` injecteren en zien of we kunnen verbinden met de database.

```csharp
public HomeController(IConfiguration configuration, ILogger<HomeController> logger, StoreContext storeContext)
{
    ...

    logger.LogInformation($"Can connect to database: {storeContext.Database.CanConnect()}");

}
```

je zal dan een message zien passeren in je Output als je de applicatie start.

## Dependency injection

In dit deeltje gaan jullie zien waarom dependency injection zo interessant is. Tot nu toe hadden we altijd gebruik gemaakt van de `ProductsInMemoryRepository` wat een implementatie was van de `IProductsRepository`. Nu gaan we een nieuwe implementatie van deze interface maken `ProductsDbRepository`.

```csharp
using System;
using System.Linq;

namespace LlamaStore.Models
{

    public class ProductsDbRepository : IProductRepository
    {

        private StoreContext storeContext;

        public ProductsDbRepository(StoreContext storeContext)
        {
            this.storeContext = storeContext;
        }

        public void Create(Product product)
        {
            throw new NotImplementedException();
        }

        public void Delete(Product product)
        {
            throw new NotImplementedException();
        }

        public Product Get(int id)
        {
            throw new NotImplementedException();
        }

        public IQueryable<Product> GetAll()
        {
            return storeContext.Products;
        }

        public void Update(Product product)
        {
            throw new NotImplementedException();
        }
    }
}

```

We gebruiken dependency injection om in deze klasse de `StoreContext` te injecteren. Voorlopig implementeren we enkel nog maar de `GetAll` methode om alle producten in te laden. We zullen later systematisch de andere methoden ook invullen.

Als we nu gebruik willen maken van de `ProductsDbRepository` in plaats van de `ProductsInMemoryRepository` hoeven we maar 1 lijn code aan te passen.

```text
services.AddSingleton<IProductRepository, ProductsInMemoryRepository>();
```

wordt

```text
services.AddTransient<IProductRepository, ProductsDbRepository>();
```

Je ziet hier dat we `AddSingleton` hebben vervangen door `AddTransient`. Het was initieel nodig om de `ProductsInMemoryRepository` maar 1 keer aan te maken in het geheugen omdat we wilden dat de lijst van studenten niet elke keer opnieuw leeg werd gemaakt elke keer de service werd opgevraagd. Nu is dit niet meer nodig want de gegevens zitten in een database.

Als je nu de applicatie gaat opstarten dan ga je zien dat er een error komt omdat de tabel nog niet bestaat. 

## Database aanmaken

Voordat we de applicatie kunnen opstarten willen we er voor zorgen dat de database aangemaakt is en de tabellen aanwezig zijn. Dit kunnen we doen aan de hand van de `EnsureCreated` methode. Deze methode gaat er voor zorgen dat de database aangemaakt is. Als ze al aangemaakt is, dan zal deze methode niets doen.

We plaatsen in onze constructor van onze `ProductsDbRepositoy` de code om de tabellen aan te maken.

```csharp
if (this.storeContext.Database.EnsureCreated())
{
    this.storeContext.Products.Add(new Product { Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M, ImageURL = "~/images/products/1.jpg", Type = ProductType.StuffedAnimal });
    this.storeContext.Products.Add(new Product { Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M, ImageURL = "~/images/products/2.jpg", Type = ProductType.StuffedAnimal });
    this.storeContext.Products.Add(new Product { Name = "World of Llamacraft", Description = "An online RPG about llamas", Price = 59.99M, ImageURL = "~/images/products/3.png", Type = ProductType.ComputerGame });
    this.storeContext.Products.Add(new Product { Name = "L.A.M.A", Description = "Llama card game", Price = 9.99M, ImageURL = "~/images/products/4.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "Altiplano", Description = "Flying llamas for everyone", Price = 12.99M, ImageURL = "~/images/products/5.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "Llamas unleashed", Description = "A fast paced llama game about crazy llamas", Price = 12.99M, ImageURL = "~/images/products/6.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "Leipe lama's", Description = "A card game about very dodgy llamas", Price = 12.99M, ImageURL = "~/images/products/7.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "Llama drama", Description = "Llamas can cause drama too", Price = 39.99M, ImageURL = "~/images/products/8.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "Llama lineup", Description = "Whodunnit game about llamas", Price = 20.99M, ImageURL = "~/images/products/9.jpg", Type = ProductType.BoardGame });
    this.storeContext.Products.Add(new Product { Name = "VALA", Description = "Vicious llama apocalypse", Price = 49.99M, ImageURL = "~/images/products/10.jpg", Type = ProductType.ComputerGame });
    this.storeContext.Products.Add(new Product { Name = "Booty shaking llama", Description = "a booty shaking llama", Price = 12.99M, ImageURL = "~/images/products/11.jpg", Type = ProductType.PlasticToy });
    this.storeContext.Products.Add(new Product { Name = "Furreal llama", Description = "Furreal llama", Price = 12.99M, ImageURL = "~/images/products/12.jpg", Type = ProductType.StuffedAnimal });
    this.storeContext.Products.Add(new Product { Name = "Fortnite llama", Description = "Fortnite llama", Price = 22.99M, ImageURL = "~/images/products/13.jpg", Type = ProductType.StuffedAnimal });
    this.storeContext.Products.Add(new Product { Name = "Lego Llama", Description = "Lego llama", Price = 22.99M, ImageURL = "~/images/products/14.jpg", Type = ProductType.StuffedAnimal });
    this.storeContext.SaveChanges();
}
```

Deze code zal enkel lopen als de database en de tabellen nog niet zijn aangemaakt. 

Als we nu bijvoorbeeld met MySQL workbench inloggen op onze sql database dan kunnen we nu zien dat de tabel is aangemaakt en we SQL query kunnen uitvoeren om de inhoud te bekijken

![](../.gitbook/assets/image%20%2880%29.png)

We merken nu op dat de precisie van de decimal voor prijs fout staat. We hebben maar 2 cijfers na de comma nodig. We zouden dit nu kunnen oplossen met SQL queries, maar dat gaan we niet doen want we werken hier met een Code First approach, dus de code moet de enige waarheid bevatten.

Als we nu de 

```csharp
[Column(TypeName = "decimal(4,2)")]
public decimal Price {get;set;}
```

annotatie toevoegen, dan zal bij het genereren van een tabel hier rekening mee gehouden worden dat je maar 2 cijfers na de komma wilt en er maar 4 cijfers voor de komma gaan staan. Je moet hierna wel de tabel terug volledig droppen om het nieuwe schema aan te maken.

## CRUD

Momenteel hebben we nog niet alle methoden van de interface `IProductRepository` geïmplementeerd. De applicatie werkt momenteel dus nog niet volledig. 

We maken de Create methode. We voegen een product toe door de Add methode aan de roepen. Elke keer als een transactie moet uitgevoerd worden dan moeten we SaveChanges doen anders worden de wijzigingen niet opgeslagen in de database.

```csharp
public void Create(Product product)
{
    this.storeContext.Products.Add(product);
    this.storeContext.SaveChanges();
}
```

De Delete methode is zeer analoog

```csharp
public void Delete(Product product)
{
    this.storeContext.Products.Remove(product);
    this.storeContext.SaveChanges();
}
```

Voor de Get kunnen we de Find methode gebruiken op de Context

```csharp
public Product Get(int id)
{
    return this.storeContext.Products.Find(id);
}
```

En de update is ook gelijkaardig aan de Create en de Delete

```csharp
public void Update(Product product)
{
    this.storeContext.Products.Update(product);
    this.storeContext.SaveChanges();
}
```

## Begeleidend videomateriaal

{% embed url="https://ap.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=8b1b9390-0038-47fd-9d9a-ac7800b08098" %}



