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

We hebben een model klasse nodig waarin we kunnen bijhouden hoeveel items er van een bepaald product in ons winkelmandje liggen. We maken een `ShoppingCartLine` klasse aan in onze Models directory:

```csharp
public class ShoppingCartLine
{
    public Product Product { get; set; }
    public int Amount { get; set; }
}
```

Nu hebben we een Collection nodig waar we onze `ShoppingCartLine` elementen in kunnen opslaan. Omdat we snel willen kunnen zoeken aan de hand van de `Id` van een product, gaan we hier voor een `Dictionary`. Deze laat toe om voor een bepaalde key een value op te zoeken. De key zal hier de Id van het product zijn, en de value de ShoppingCartLine objecten. Deze Dictionary willen we dan bijhouden in onze session, zodat deze tussen alle requests beschikbaar blijft.

We maken een methode in onze `ShoppingCartController` om deze dictionary weg te schrijven in ons session object:

```csharp
private void saveShoppingCartToSession(Dictionary<int, ShoppingCartLine> shoppingCart)
{
    HttpContext.Session.SetString("ShoppingCart", JsonConvert.SerializeObject(shoppingCart));
}
```

Omdat je eigenlijk alleen maar strings kan opslagen in een session object moeten we onze `Dictionary` eerst via `JsonConvert` omzetten naar een json string, dat we simpel kunnen wegschrijven.

We hebben nu een methode voor de dictionary weg te schrijven, en nu moeten we uiteraard ook een maken om deze uit te lezen.

```csharp
private Dictionary<int, ShoppingCartLine> getShoppingCartFromSession()
{
    string sessionString = HttpContext.Session.GetString("ShoppingCart");
    Dictionary<int, ShoppingCartLine> shoppingCart = sessionString != null ? JsonConvert.DeserializeObject<Dictionary<int, ShoppingCartLine>>(HttpContext.Session.GetString("ShoppingCart")) : new Dictionary<int, ShoppingCartLine>();
    return shoppingCart;
}
```

Nu zijn we klaar om de echte Actions te schrijven voor onze Controller. We beginnne met de `Add` action om een product toe te voegen aan ons mandje.

```csharp
public IActionResult Add(int id)
{
    Product product = productRepository.Get(id);

    Dictionary<int, ShoppingCartLine> shoppingCart = getShoppingCartFromSession();

    shoppingCart[id] = shoppingCart.GetValueOrDefault(id, new ShoppingCartLine { Product = product, Amount = 0 });
    shoppingCart[id].Amount++;

    saveShoppingCartToSession(shoppingCart);

    return RedirectToAction("Index", "ShoppingCart", new { id });
}
```

Wat doen we hier nu allemaal?

* Eerst vragen we het product op uit onze productRepository zodat we een product object hebben waar we mee kunnen werken.
* Daarna vragen we via de `getShoppingCartFromSession` die we hiervoor geschreven hebben de ShoppingCart uit de session op.
* We halen de `ShoppingCartLine` op via de id en indien deze nog niet bestaat maken we er een aan voor het nieuwe product en zetten we de amount op 0.
* We verhogen de hoeveelheid van de producten en vervolgens slagen we de shopping cart weer op in onze session.
* De browser wordt nu geredirect naar de Index action van de ShoppingCart controller.



