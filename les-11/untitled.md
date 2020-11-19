# Sessions

In web applicaties wil je vaak tussen verschillende pagina's en requests bepaalde informatie gaan bijhouden. Er zijn hiervoor twee manieren hoe we dit kunnen doen. Je hebt hoogstwaarschijnlijk al vaak van cookies gehoord. Cookies zijn stukjes data die worden opgeslagen bij de web browser van de gebruiker. Cookies zijn redelijk beperkt in grootte en moeten met elke request meegestuurd worden. Bij sessies daarentegen wordt de data opgeslagen op de server zelf, en is er enkel maar een session\_id nodig in de cookies om deze te identificeren. Opgepast wel, een sessie blijft zolang geldig als je browser blijft open staan. Sluit je je venster, dan is de sessie ook weg. Ook hebben sessies maar een gelimiteerde tijdspanne \(meestal 20 minuten\)

### Gebruiken van sessions

In de startup klasse moeten we eerst de session middleware activeren en de services toevoegen. We voegen dus in `ConfigureServices` de volgende code toe

```csharp
services.AddSession(options => {
    options.IdleTimeout = TimeSpan.FromMinutes(60);//You can set Time   
```

Omdat we hier een ShoppingCart gaan implementeren, gaan we de sessie iets langer dan normaal actief houden \(60 minuten\).

Ook moeten we de SessionMiddleware nog gebruiken door 

```csharp
app.UseSession()
```

toe te voegen aan de `Configure` methode.

Je moet ook nog via NuGet `Microsoft.AspNetCore.Session` installeren.

![](../.gitbook/assets/image%20%2892%29.png)

Je kan heel simpelweg in je Controllers een sessie schrijven

```csharp
HttpContext.Session.SetString("sessionKey", "Dit is een test");
```

en lezen

```csharp
HttpContext.Session.getString("sessionKey");
```

### ShoppingCart

We gaan nu aan de hand van een praktisch voorbeeld het gebruik van sessions in ASP.NET Core uitleggen. We willen een winkelmandje gaan implementeren. Je moet een product kunnen toevoegen in de details view en uiteindelijk moet je een overzicht van je winkelmandje kunnen zien in een nieuwe view. Je hebt dus een manier nodig om dat winkelmandje bij te houden tussen de pagina's heen, je moet ze dus ergens tijdelijk opslagen. Hier zouden we cookies voor kunnen gebruiken, maar die zijn te gelimiteerd in grootte. We gebruiken dus sessions om ons winkelmandje bij te houden tussen requests.

Het eerste wat we gaan doen is een nieuwe `ShoppingCartController` aanmaken in de controllers directory en we injecteren de `IProductRepository` service hier in.

```csharp
public class ShoppingCartController : Controller
{
    private IProductRepository productRepository;

    public ShoppingCartController(IProductRepository productRepository)
    {
        this.productRepository = productRepository;
    }
}
```



