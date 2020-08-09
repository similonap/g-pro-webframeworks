# Project aanmaken

In dit onderdeel overlopen we samen stap per stap hoe we een nieuw project aanmaken. ASP.NET Core-projecten kan je maken met behulp van sjablonen in Visual Studio of vanaf de. NET Core command line interface \(.NET CLI\).

Visual Studio 2017 geeft een geweldige .NET Core-ontwikkelingservaring - met top-notch debugging, Docker-integratie en vele andere functies - maar ik gebruik de .NET CLI in deze les dan kunnen jullie deze les ook volgen op een Mac of Linux machine.

## .NET Core 3.1 SDK

We gaan er vanuit dat je de laatste nieuwe versie van de **.NET Core 3.1 SDK** geïnstalleerd hebt. Je kan de SDK het gemakkelijkst installeren met behulp van de Installer. Je kan deze vinden voor alle plaformen \(MacOS, Linux en Windows\) op [https://dotnet.microsoft.com/download\#/sdk](https://dotnet.microsoft.com/download#/sdk)

Je kan je versie van nakijken door het volgende commando in je command line \(powershell, bash,...\) naar keuze uit te voeren:

```bash
dotnet --version
3.1.302
```

Let er dus op dat deze altijd up to date is. We werken in deze cursus altijd op de laatste versie.

## Een leeg ASP.NET project aanmaken

Je kan een ASP.NET Core applicatie vanaf de command-line interface \(CLI\) of in Visual Studio. Hier gaan we dit dus doen via de CLI.

Voor we beginnen zorg ervoor dat je een map `WebFrameworks` hebt met daarin een map `EersteProject`. Je kan dit doen door

```text
mkdir -p WebFrameworks/EersteProject
```

in je command line interface uit te voeren en dan vervolgens naar die map te gaan met

```text
cd WebFrameworks/EersteProject
```

Met het commando `dotnet` kan je nieuwe .NET Core-projecten creëren. Het commando `dotnet new` zonder extra argumenten toont een lijst met de beschikbare project sjablonen \(templates\).

Wij willen een leeg ASP.NET Core project aanmaken zonder te veel extra's omdat we deze zelf gaan toevoegen. We maken dus een web project aan

```text
dotnet new web
```

Zodra het project is aangemaakt, moet je het kunnen uitvoeren met de instructie

```text
dotnet run
```

Open de website, die op basis van de template is gemaakt, door `dotnet run` uit te voeren en te navigeren naar de URL waarnaar de app luistert.  
In Windows is dat normaal gezien`http://localhost:5000`, de default url die aan je app wordt toegekend.

![Het project is aangemaakt en opgestart aan de hand van dotnet run](../.gitbook/assets/image%20%282%29.png)

Hier zal je Hello World te zien krijgen. Om te begrijpen hoe die Hello World daar te voorschijn komt moet je naar de twee bestanden `Startup.cs` en `Program.cs` kijken.

## Program klasse

De `Program` klasse bevat de methode `Main`, dit is het start punt van ASP.NET Core applicaties.

De `Main` methode is gelijkaardig aan de `Main` methode van een console applicatie. Eigenlijk zijn alle .NET Core applicaties console applicaties waarop andere types van applicaties zijn gemaakt. Zoals in ons geval een web applicatie.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStartup<Startup>();
            });
}
```

De bovenstaande code zorgt ervoor dat er een IWebHostwordt aangemaakt en dat deze wordt geconfigureerd aan de hand van de `Startup` klasse die we verder gaan bezien. Deze wordt dan gemaakt en daarna uitgevoerd aan de hand van de Run\(\) methode.

## Startup klasse

In Startup.cs staat wat de app moet doen bij het opstarten

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/", async context =>
        {
            await context.Response.WriteAsync("Hello World!");
        });
    });
}
```

De app configureert de applicatie zodat de ExceptionPage getoond wordt aan de gebruiker als er een probleem opduikt. Dit zal enkel gebeuren als we de app in development mode opstarten. Als de app naar de productie modus overschakelt wanneer deze in een live omgeving wordt ingezet, wordt dit niet uitgevoerd.

We kiezen in dit voorbeeld om gebruik te maken van een combinatie van `UseRouting` en `UseEndpoints` die samen worden gebruikt. UseRouting zorgt ervoor dat de requests die van de browser komen worden gematched met de endpoints. `UseEndpoints` zal de route dan uitvoeren.

In de `UseEndpoints` gaan we hier een GET registreren voor het pad / zodat als we naar de root pagina surfen dat we dan Hello World te zien krijgen. Deze wordt rechstreeks hier op de Response stream geschreven.

## Het Projectbestand

Tenslotte bekijken we het .csproj bestand. Je kan het project bestand bekijken door het EersteProject.csproj bestand te openen of door in visual studio op het root element te drukken in de solution explorer.

![Je kan het csproj bestand bekijken door op EersteProject te drukken.](../.gitbook/assets/image%20%283%29.png)

Dit bestand vertelt MSBuild hoe het project wordt gebouwd - van welke pakketten het afhankelijk is, welke versie van .NET Core te targeten, enzovoort. In vorige versies van .NET Core was dit projectbestand veel groter. .NET Core 3.1 heeft veel moeite gestoken in het korter, eenvoudiger en leesbaarder maken van .csproj-bestanden.

In dit bestand zal je vooral gebruiken voor het definieren van dependencies die je project zal gebruiken. Dit zal je meestal niet rechstreeks in dit bestand doen maar je zal hiervoor de package manager NuGet gebruiken. Dit zullen we later in deze cursus verder behandelen.

