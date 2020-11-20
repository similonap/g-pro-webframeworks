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

![](../.gitbook/assets/image%20%2893%29.png)

Je kan heel simpelweg in je Controllers een sessie schrijven

```csharp
HttpContext.Session.SetString("sessionKey", "Dit is een test");
```

en lezen

```csharp
HttpContext.Session.getString("sessionKey");
```

### Een voorbeeld

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
* De browser wordt nu geredirect naar de `Index` action van de ShoppingCart controller.

We hebben momenteel nog geen Index action dus hier gaan we nu voor zorgen. 

```csharp
public IActionResult Index()
{
    Dictionary<int, ShoppingCartLine> shoppingCart = getShoppingCartFromSession();
    List<ShoppingCartLine> lines = shoppingCart.Values.ToList<ShoppingCartLine>();
    ShoppingCartViewModel shoppingCartViewModel = new ShoppingCartViewModel
    {
        ShoppingCart = lines
    };
    return View(shoppingCartViewModel);
}
```

Het enige wat we hier doen is het opvragen van de shopping cart uit onze sessie en we zetten deze vervolgens om naar een gewone lijst. Deze lijst plaatsen we in een ShoppingCartViewModel en geven die door aan de view. We maken deze ShoppingCartViewModel ook aan in onze ViewModels directory:

```csharp
public class ShoppingCartViewModel
{
    public List<ShoppingCartLine> ShoppingCart { get; set; }

}
```

We maken nu de View aan voor deze Action. Dus we maken hier een nieuwe directory `ShoppingCart` aan in de Views directory. Daarin maken we een nieuwe Index.cshtml file aan:

```markup
@model ShoppingCartViewModel
<table class="table">
    <thead>
        <tr>
            <th scope="col"></th>
            <th scope="col">Name</th>
            <th scope="col">Amount</th>
            <th scope="col"></th>
        </tr>
    </thead>
    <tbody>
        @foreach (ShoppingCartLine cartLine in Model.ShoppingCart)
        {
        <tr style="height: 200px">
            <td><img src="@Url.Content(cartLine.Product.ImageURL)" width="100" /></td>
            <td>@cartLine.Product.Name</td>
            <td>@cartLine.Amount</td>
        </tr>
        }
    </tbody>

</table>
```

We voegen ook nog een link toe in onze Details pagina om een product toe te voegen aan ons winkelmandje:

```markup
<a asp-controller="ShoppingCart" asp-action="Add" asp-route-id="@Model.Id">Add to shopping cart</a>
```

Onze nieuwe controller moet nu ook nog toegevoegd worden aan de navigatiebalk bovenaan de webpagina. Dus we plaatsen in \_Layout.cshtml nog de volgende link: 

```markup
<a class="nav-link" asp-controller="ShoppingCart" asp-action="Index">ShoppingCart</a>
```

Nu kunnen we een shopping item toevoegen:

![](../.gitbook/assets/image%20%2892%29.png)

![](../.gitbook/assets/image%20%2894%29.png)

We willen ook nog vanuit de ShoppingCart de hoeveelheid van een bepaald product gaan verhogen of verlagen dus we voegen ook nog eens 2 buttons toe met een plus en een min:

```markup
<td><a asp-action="Add" asp-controller="ShoppingCart" asp-route-id="@cartLine.Product.Id" role="button" class="btn btn-labeled btn-primary"><i class="fas fa-plus"></i></a>
    <a asp-action="Remove" asp-controller="ShoppingCart" asp-route-id="@cartLine.Product.Id" role="button" class="btn btn-labeled btn-primary"><i class="fas fa-minus"></i></a></td>
```

We verwijzen hier naar een Remove action die we nog niet hebben, dus we gaan deze nu schrijven in onze ShoppingCartController:

```csharp
public IActionResult Remove(int id)
{
    Dictionary<int, ShoppingCartLine> shoppingCart = getShoppingCartFromSession();

    shoppingCart[id].Amount--;
    if (shoppingCart[id].Amount == 0)
    {
        shoppingCart.Remove(id);
    }

    saveShoppingCartToSession(shoppingCart);

    return RedirectToAction("Index", "ShoppingCart", new { id });
}
```

Nu kunnen we naar hartenlust producten toevoegen en verwijderen aan de hand van de + en - button.

Willen we nu ook een totaal bedrag van alle aankopen laten zien. Dan kan dit heel eenvoudig door een extra property toe te voegen aan ons viewmodel:

```csharp
public Decimal TotalAmountMoney { get; set; }
```

In de Index action maken we dan de berekening van de som van alle producten:

```csharp
public IActionResult Index()
{
    Dictionary<int, ShoppingCartLine> shoppingCart = getShoppingCartFromSession();
    List<ShoppingCartLine> lines = shoppingCart.Values.ToList<ShoppingCartLine>();
    Decimal sum = 0;
    foreach (ShoppingCartLine line in lines) {
        sum += line.Amount * line.Product.Price;
    }
    ShoppingCartViewModel shoppingCartViewModel = new ShoppingCartViewModel
    {
        ShoppingCart = lines,
        TotalAmountMoney = sum
    };
    return View(shoppingCartViewModel);
}

```

en we tonen de som in de Index.cshtml view:

```markup
<tr>
    <td></td>
    <td></td>
    <td><b>Total Price:</b> €@Model.TotalAmountMoney</td>
    <td></td>
</tr>
```



