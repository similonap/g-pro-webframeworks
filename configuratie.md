# Configuratie en Logging

Het configureerbaar maken van je applicatie is een belangrijk aspect voor de flexibiliteit van je web applicatie te garanderen. Wil je een andere database connection string gebruiken? Wil je bepaalde constanten ergens definieren? Dit doe je allemaal in de Configuration van je applicatie. 

### Waar haalt ASP.NET Core je configuratie

Configuration providers lezen configuratie data van key value pairs van een aantal verschillende configuratie bronnen:

* Settings files zoals appsettings.json
* Environment variabelen
* Azure
* Command line argumenten
* ...

Hij zal dit ook in een bepaald volgorde doen. Bijvoorbeeld als een key in appsettings.json en op environment variabelen gezet is dan zal de configuratie setting van de environment variables genomen worden.

### appsettings.json

Als je een empty project aanmaakt maakt visual studio altijd een appsettings.json bestand aan met de volgende inhoud:

```javascript
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

Dit zijn al voorgedefinieerde configuratie variabelen die worden gebruikt voor het configureren van ASP.NET Core. Zo kan je hier de log levels aanpassen en aangeven welke ip adressen kunnen connecteren met je web applicatie. We gaan dit bestand aanpassen met een nieuwe configuratie. 

We willen graag de naam van onze winkel configureerbaar maken en onze navigatie balk kunnen in dark mode zetten en in light mode. Dus we voegen twee configuratie variabelen toe

```javascript
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "StoreName": "The Llama store",
  "NavBar": "dark",
  "AllowedHosts": "*"
}
```

### Configuration in razor pages

Nu moeten we onze algemene layout gaan aanpassen zodat die gebruik gaat maken van deze configuratie variabelen. Omdat we graag de configuratie beschikbaar hebben op elke pagina van onze webapplicatie plaatsen we de volgende lijn code in onze `_ViewImports.cshtml` .

```aspnet
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
```

De eerste lijn code importeert de namespace van `Configuration` zodat we het `Configuration` object kunnen gebruiken. 

De tweede lijn code gebruikt dependency injection om het `Configuration` object te injecteren in onze view zodat we het kunnen gebruiken. Meer over dependency injection zien we later. 

Als we nu terug gaan naar onze `_Layout.cshtml` kunnen we nu de volgende lijnen code bovenaan de pagina plaatsen.

```text
@{ 
    var navBarColor = @Configuration["NavBar"];
    var storeName = @Configuration["StoreName"];
}
```

We maken een variabele aan voor de navBarColor en de storeName die we vervolgens gaan gebruiken in onze code.

```markup
<title>@storeName - @ViewBag.Title</title>
...
<nav class="navbar navbar-expand-lg navbar-@navBarColor bg-@navBarColor">
<a class="navbar-brand" href="#">@storeName</a>
```

Als we nu de appsettings.json aanpassen en de web applicatie terug starten gaat deze de configuratie variabelen gebruiken om de titel van de pagina te zetten en de kleur van de navigatiebalk.

### Configuration in je controller

We kunnen uiteraard ook configuratie variabelen gaan uitlezen vanuit je `Controller` klassen. Dit gaan we ook aan de hand van Dependency Injection laten doen. We maken een constructor aan in de `HomeController` klasse

```csharp
public class HomeController : Controller
{
    private IConfiguration configuration;
    private ILogger<HomeController> logger;

    public HomeController(IConfiguration configuration, ILogger<HomeController> logger)
    {
        this.configuration = configuration;
        this.logger = logger;
    }

    // Hier staan nog actions
}
```

We gaan hier een `IConfiguration` injecteren en een `ILogger` injecteren zodat we die kunnen gebruiken in onze `HomeController` om een aantal configuratie variabelen in onze logging te printen.

we kunnen nu onderaan in de constructor de volgende code zetten om de 2 configuratie variabelen te loggen in onze log output:

```text
logger.LogInformation($"NavBar color {configuration["NavBar"]}");
logger.LogInformation($"StoreName {configuration["StoreName"]}");
```

![](.gitbook/assets/image%20%2845%29.png)

### Environment variables

Zoals hierboven al uitgelegd gaan environment variabelen altijd voor op de waarden in `appsettings.json`. Dus wil je tijdelijk de applicatie opstarten met een andere configuration variabele dan kan je dat doen op de volgende manier.

![](.gitbook/assets/image%20%2843%29.png)

![](.gitbook/assets/image%20%2844%29.png)

Als we dan de web applicatie afsluiten en terug opstarten dan zullen we de nieuwe StoreName te zien krijgen.

