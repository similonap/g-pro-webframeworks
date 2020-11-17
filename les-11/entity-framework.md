# Entity Framework

Het Entity Framework is een Object-Relational mapping-technologie, waarmee gegevens uit een relationele database kunnen worden omgezet in objecten met gegevens die door objectgeörienteerde programmeertalen gebruikt kunnen worden.

Een belangrijke functie binnen het framework is het automatiseren van taken. Ontwikkelaars moeten niet meer zelf in hun code de conversieslag tussen database en applicatie maken. Het Entity Framework zorgt ervoor dat data op een eenduidige wijze aan de ontwikkelaar ter beschikking wordt gesteld zodat er op een abstractere manier mee gewerkt kan worden.Voordat je met EF kan beginnen, moet je de EF assemblies lokaal installeren. Microsoft's nieuwe distributiemodel is niet meer gebaseerd op de ouderwetse Windows installers, maar op nieuwe technologieën zoals [NuGet ](https://www.nuget.org/)en [Git](http://git-scm.com/).

We gaan in deze cursus werken met een code first aanpak. Dit betekent dat we niet gaan starten vanuit een bestaande database die we zelf hebben aangemaakt aan de hand van SQL queries. Dit is buiten de scope van deze cursus. Wat wij gaan doen is starten van ons eigen model en daaruit gaan we de database en zijn tabellen laten genereren.

## EF Core installeren

[NuGet](https://www.nuget.org/) staat in voor .NET pakketbeheer. Met NuGet kan je in Visual Studio projecten dependencies op software-pakketten installen, assemblies, broncode bestanden, PowerShell-scripts, enz. Die dependancies worden opgeslagen in remote repositories.

EF heeft zijn eigen assembly, en wordt los van de reguliere NET releases vrijgegeven. Om het te installeren in een bestaand project, open je de **Manage NuGet Packages for Solution**:

![](../.gitbook/assets/image%20%2877%29.png)

Hier kan je de nodige dependencies installeren

![](../.gitbook/assets/image%20%2878%29.png)

Installeer de volgende dependencies. Die zijn nodig voor EntityFramework te kunnen gebruiken met MySQL.

1. **Microsoft.EntityFrameworkCore** \(5.0.0\)
2. **Microsoft.EntityFrameworkCore.Relational** \(5.0.0\)
3. **Pomelo.EntityFrameworkCore.MySql** \(3.2.4\)
4. **Microsoft.EntityFrameworkCore.Tools** \(5.0.0\)

Als je nu in het csproj bestand gaat kijken dan zal je zien dat er voor elke dependency die we hebben geinstalleerd hebben met NuGet een extra lijn is toegevoegd die de dependency voorstelt.

```markup
<Project Sdk="Microsoft.NET.Sdk.Web">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.1</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="5.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Relational" Version="5.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="5.0.0">
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

We maken een context voor onze school database genaamd `SchoolContext.cs` en we plaatsen die in de `Models` map:

```csharp
using Microsoft.EntityFrameworkCore;

namespace EersteProjectWebFrameworks.Models
{
    public class SchoolContext : DbContext
    {
        public SchoolContext(DbContextOptions options) : base(options)
        {
        }

        public DbSet<Student> Students { get; set; }
    }
}
```

De context klasse hierboven heeft een constructor die `DbContextOptions` als argument heeft. `dbContextOptions` bevat de configuratie informatie die nodig is om de `DbContext` te configureren. Het zal bijvoorbeeld de connection string bevatten die nodig is om met de database te verbinden.

Deze klasse zal ook een aantal `DbSet` properties bevatten voor elke entiteit \(database tabel\). In ons voorbeeld is dat dus de `Students` tabel.

## Dependency injection

In dit deeltje gaan jullie zien waarom dependency injection zo interessant is. Tot nu toe hadden we altijd gebruik gemaakt van de `StudentInMemoryRepository` wat een implementatie was van de `IStudentRepository`. Nu gaan we een nieuwe implementatie van deze interface maken `StudentDbRepository`.

```csharp
using System.Collections.Generic;
using System.Linq;

namespace EersteProjectWebFrameworks.Models
{
    public class StudentDbRepository : IStudentRepository
    {
        private SchoolContext schoolContext;

        public StudentDbRepository(SchoolContext context)
        {
            schoolContext = context;
        }

        public IEnumerable<Student> GetAll()
        {
            return schoolContext.Students;
        }

        public void Create(Student student)
        {
            schoolContext.Students.Add(student);
            schoolContext.SaveChanges();
        }

        public void Update(int id, Student student)
        {
            schoolContext.Students.Update(student);
            schoolContext.SaveChanges();
        }

        public Student Get(int id)
        {
            return schoolContext.Students.Find(id);
        }
    }
}
```

Deze klasse heeft als constructor een `SchoolContext` object als argument. Hoe deze wordt doorgegeven zien we straks. We zetten deze in een private variabele zodat we deze kunnen gebruiken in de rest van de klasse. We implementeren de hele interface gebruik makend van de `schoolContext`. Belangrijk hier om te weten dat we bij wijzigingen altijd de `SaveChanges` methode moeten aanroepen anders worden de wijzigingen niet uitgevoerd in de database.

Als we nu gebruik willen maken van de `StudentDbRepository` in plaats van de `StudentInMemoryRepository` hoeven we maar 1 lijn code aan te passen.

```text
services.AddSingleton<IStudentRepository, StudentInMemoryRepository>();
```

wordt

```text
services.AddTransient<IStudentRepository, StudentDbRepository>();
```

Je ziet hier dat we `AddSingleton` hebben vervangen door `AddTransient`. Het was initieel nodig om de `StudentInMemoryRepository` maar 1 keer aan te maken in het geheugen omdat we wilden dat de lijst van studenten niet elke keer opnieuw leeg werd gemaakt elke keer de service werd opgevraagd. Nu is dit niet meer nodig want de gegevens zitten in een database.

Nog 1 detail waar we moeten voor zorgen. De `StudentDbRepository` maakt gebruik van de `SchoolContext` dus we moeten deze service nog ook beschikbaar stellen via dependency injection.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddDbContext<SchoolContext>(options => options.UseSqlite("Data Source=student.db"));
    services.AddTransient<IStudentRepository, StudentDbRepository>();
}
```

## Database aanmaken

Voordat we de applicatie kunnen opstarten willen we er voor zorgen dat de database aangemaakt is. Dit kunnen we doen aan de hand van de `EnsureCreated` methode. Deze methode gaat er voor zorgen dat de database aangemaakt is. Als ze al aangemaakt is, dan zal deze methode niets doen.

We maken hiervoor een klasse aan `DbInitializer`

```csharp
namespace EersteProjectWebFrameworks.Models
{
    public class DbInitializer
    {
        public static void Initialize(SchoolContext context)
        {
            context.Database.EnsureCreated();
        }
    }
}
```

We willen dat deze `Initialize` methode opgeroepen wordt voordat de applicatie opgestart wordt. We kunnen dit doen door de main methode van de `Program` klasse aan te passen

```csharp
public static void Main(string[] args)
{
    IHost host = CreateHostBuilder(args).Build();
    using (IServiceScope scope = host.Services.CreateScope())
    {
        IServiceProvider services = scope.ServiceProvider;
        SchoolContext context = services.GetRequiredService<SchoolContext>();
        DbInitializer.Initialize(context);
    }
    host.Run();
}
```

**Een beetje uitleg:**

Merk op dat we

```csharp
CreateWebHostBuilder(args).Build().Run();
```

opsplitsen in 2 delen. Het aanmaken van het `Host` object in het begin en het uitvoeren van de `host.Run()` op het einde.

Daartussen doen we drie dingen:

1. We halen de service provider op door `host.Services.CreateScope().ServiceProvider` op te roepen. 
2. We halen de database context instantie op van de dependency injection container door `GetRequiredService<SchoolContext>()` uit te voeren
3. We roepen de `DbInitializer.Initialize(context)` methode op om de database aan te maken. 

Merk op dat we hier `using (host.Services.CreateScope())` gebruiken. We willen dat na het uitvoeren van deze instructies de Scope terug wordt gedisposed.

