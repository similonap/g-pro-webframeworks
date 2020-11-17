# Entity Framework - Migrations

In projecten in de echte wereld veranderen data models zeer vaak. Bij elke feature dat er bij komt, zal wel ergens een wijzing in het model vereisen. Nieuwe entiteiten of properties worden toegevoegd, en dus moeten de database schemas mee volgen zodat ze synchroon blijven met de rest van de applicatie. Hier komen migrations aan te pas. Dit is een feature die in Entity Framework zit die het mogelijk maakt om eenvoudig de database synchroon te houden met de applicatie. En het maakt het mogelijk om de bestaande data in de database te behouden.

Op de manier dat we het in de vorige les hebben gedaan is dit niet mogelijk of heel moeilijk en moet de database heel de tijd terug gecleared worden naar zijn originele staat.

Migraties werken op de volgende wijze:

* Als er een data model verandering is, dan moet de developer de Enitity Framework Core Tools gebruiken om een nieuwe migratie file te generen die beschrijft welke wijzigingen er moeten gedaan worden om de database schemas in sync te houden. EF Core vergelijk het huidige model met een oudere versie van het model, en bepaald zo de verschillen. Met deze verschillen genereerd hij dan migraties files. Deze files kunnen gewoon samen met je versie beheersysteem bijgehouden worden \(bv met git\)
* Alle migraties die worden gedaan worden in een speciale tabel bijgehouden in de database. Zo weet het framework welke wijzigingen er al gedaan werden en welke niet.

### Aan de slag met de tools

Het eerste wat we moeten doen is de dotnet ef tools installeren. We gaan dit globaal doen want moeten we dit maar 1 keer doen en niet voor elk project:

```text
dotnet tool install --global dotnet-ef
```

We installeren ook via NuGet de volgende dependency

```text
Microsoft.EntityFrameworkCore.Design (3.1.10)
```

### Het eerste migratie bestand aanmaken

We kunnen nu ons eerste migratie bestand aanmaken. We noemen deze migratie InitialCreate, want deze gaat het aanmaken van de initiele staat beschrijven

```
dotnet ef migrations add InitialCreate
```

EF Core maakt dan een directory aan genaamd Migrations in je project, en genereert wat bestanden. Het is een goed idee om eens te gaan kijken wat daar allemaal instaat.

![](../.gitbook/assets/image%20%2885%29.png)

Je ziet in dit bestand twee interessante methoden. De Up methode beschrijft wat er moet gebeuren als de migratiefile moet toegepast worden, en de Down methode als ze ongedaan moet maken. Uiteraard moet de Down methode alles ongedaan maken wat de Up methode toevoegd.

![](../.gitbook/assets/image%20%2890%29.png)

### Tabellen aanmaken

Zorg ervoor dat je tabel gedeleted is!

Op dit punt kan je EF je tabellen \(schemas\) laten aanmaken vanuit de database migratie files. Dit kan je doen via

```text
dotnet ef database update
```

Dit is alles wat er moet gebeuren. De database zal worden aangemaakt \(als deze niet bestaat\) en de tabellen zullen worden aangemaakt met de juiste velden en types. We hebben hier net zoals in vorige les geen enkele lijn SQL moeten voor schrijven. 

![](../.gitbook/assets/image%20%2884%29.png)

### Initiele data

Op dit moment hebben we onze applicatie zo gebouwd dat er initiele data wordt aangemaakt in de ProductsDbRepository. Dit is iets wat je normaal gezien niet wil doen. We gaan deze code dus wegdoen en op een andere plaats zetten.

```csharp
public ProductsDbRepository(StoreContext storeContext)
{
    this.storeContext = storeContext;
}
```

In de StoreContext plaatsen we nu de volgende code. 

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

Het is nu tijd om nog eens een migratie file aan te maken voor deze nieuwe wijzigingen.

```text
dotnet ef migrations add SeedData
```

Als je nu gaat kijken in de nieuwe migratiefile zie je dat er code is bij gegenereerd die al deze producten gaat toevoegen aan onze database.

![](../.gitbook/assets/image%20%2882%29.png)

Als we nu weer

```text
dotnet ef database update
```

uitvoeren dan zullen ook deze waarden worden toegevoegd aan onze tabel. 

### Evoluerende modellen

Stel je voor dat er een aantal dagen zijn gepasseerd en iemand vraagt om nu ook bij te houden wanneer producten zijn aangemaakt. We moeten hier een CreatedTimestamp voor aanmaken in ons model. 

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

```csharp
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

Maar nu als we de applicatie opstarten dan loopt alles mis:

![](../.gitbook/assets/image%20%2883%29.png)

Ons model is nu wel aangepast maar de database is dus nog niet mee aangepast naar de nieuwe situatie. 

Het eerste wat we nu nog moeten doen is het aanpassen van de initiele data in de StoreContext zodat alle producten nu ook een initiele waarde hebben.

![](../.gitbook/assets/image%20%2888%29.png)

Als je nu terug een migration bestand laat aanmaken met

```text
dotnet ef migrations add AddedTimestamp
```

dan zie je dat in de migration file die is aangemaakt al die wijzigingen zijn gedetecteerd en mee opgenomen zijn in het migration bestand

![](../.gitbook/assets/image%20%2887%29.png)

Willen we nu die wijziging in de database laten gebeuren dan voeren we simpelweg

```text
dotnet ef database update
```

uit.

![](../.gitbook/assets/image%20%2886%29.png)

