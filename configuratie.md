# Configuratie

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

Nu moeten we onze algemene layout gaan aanpassen zodat die gebruik gaat maken van deze configuratie variabelen. Omdat we graag de configuratie beschikbaar hebben op elke pagina van onze webapplicatie plaatsen we de volgende lijn code in onze `_ViewImports.cshtml` .

```aspnet
@using Microsoft.Extensions.Configuration
@inject IConfiguration Configuration
```

De eerste lijn code importeert de namespace van `Configuration` zodat we het `Configuration` object kunnen gebruiken. 

De tweede lijn code gebruikt dependency injection om het `Configuration` object te injecteren in onze view zodat we het kunnen gebruiken. Meer over dependency injection zien we later. 

