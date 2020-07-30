# Model-View-Controller

### Inleiding

In het vorige hoofdstuk hebben we een eenvoudig project aangemaakt waar er gebruik gemaakt wordt van een endpoint waar er via 

```csharp
await context.Response.WriteAsync("Hello World!");
```

rechstreeks naar de response stream werd geschreven. Dit is uiteraard zeer omslachtig als je dit zou moeten doen voor elke web pagina van jouw webapplicatie. We gaan hier gebruik maken van ASP.NET MVC \(Model View Controller\) om dit te vereenvoudigen.

### MVC Configureren

Om ons project van vorig hoofdstuk klaar te maken voor MVC moeten we een aantal aanpassingen maken aan het `Startup.cs` bestand. 

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
}
```

Bovenstaande code zal ondersteuning voor Controllers en Views \(Razor Views\) toevoegen aan je project en deze configureren. 

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
        endpoints.MapControllerRoute(
            name: "default",
            pattern: "{controller=Home}/{action=Index}/{id?}");
    });
}
```

We gaan hier niet meer zelf de routes definieren en zelf de resultaten naar de response stream schrijven. We vervangen dit door `endpoints.MapControllerRoute` waar we een naam en een pattern meegeven. 

Het pattern geeft aan welke pattroon de urls van de web applicatie zullen volgen. Gewoonlijk moeten we dit niet aanpassen in de meeste web applicaties.

Het pattern opgesplitst en uitgelegd geeft dit:

* **`{controller=Home}`** 
  * Dit geeft aan dat HomeController de default controller is. Dus als de gebruiker naar de hoofdpagina surft dat dan default de HomeController gebruikt zal worden.
* **`{action=Index}`** 
  * Dit geeft aan dat Index de default action is als er geen wordt opgegeven.
* **`{id?}`**
  * Er wordt gebruikt van een optionele id die mag weggelaten worden. Dit wordt aangegeven door een vraagteken in de url

Deze applicatie mist uiteraard nog een Controller voordat we deze kunnen opstarten. 

### Conventie boven configuratie

Dit betekent dat, in plaats van te vertrouwen op expliciete configuratie-instellingen, ASP.NET MVC gewoon ervan uit gaat dat de ontwikkelaars bepaalde conventies zullen volgen bij het bouwen van hun applicaties. De folderstructuur voor ASP.NET MVC project is een goed voorbeeld van het gebruik van de conventie eerder dan de configuratie. Er zijn drie speciale mappen in het project die overeenkomen met de elementen van het MVC patroon: de **Controllers**, **Models**, en **Views** mappen. 

Het is in één oogopslag duidelijk wat elk van deze mappen bevat. De figuur _De structuur van een ASP.NET MVC projectfolder_ is overduidelijk.

Als je kijkt naar de inhoud van deze mappen, ga je nog meer conventies tegenkomen.

![Conventie boven configuratie. De mappen Controllers, Models en Views.](../.gitbook/assets/image%20%285%29.png)

De Controllers map bevat niet alleen alle controller klassen van de toepassing, maar de controller klassen volgen allemaal de conventie om hun namen te beëindigen met het **Controller** achtervoegsel. Het framework gebruikt deze conventie om controllers van de toepassing te registreren bij het opstarten en de controllers te associëren met de bijbehorende routes.

De afspraak is dat alle views van de applicatie in de folder **Views** staan. Maar de afspraken gaan nog verder. De **Views** map wordt verder onderverdeeld in submappen:

1. een **Shared** map;
2. een optionele map die de views voor elke controller bevatten;

### Een controller aanmaken

Zoals al aangegeven moeten alle controllers in een map Controllers staan. Maak eerst de map Controllers aan en maak daarna een **Empty Controller Class** aan met de naam HomeController.

![](../.gitbook/assets/image%20%287%29.png)

Pas de inhoud van het nieuw aangemaakte `HomeController.cs`aan.

```csharp
public class HomeController : Controller
{
    public String Index()
    {
        return "Hello World!";
    }
}
```

Dit zal er voor zorgen dat de gebruiker **Hello World** te zien krijgt als hij naar de root pagina surft.  We gaan de applicatie opstarten aan de hand van Visual Studio deze keer. Klik op **Debug &gt; Start debugging** om de applicatie in debug modus op te starten. 

![](../.gitbook/assets/image%20%286%29.png)

