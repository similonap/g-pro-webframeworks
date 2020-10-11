# Gegevens doorgeven

Tot nu toe waren de Views en de Controllers volledig van elkaar gescheiden. Het enige wat de controller deed was aangeven dat een View moet getoond worden maar er werd geen gegevens tussen beiden doorgegeven. Views zijn normaal gezien niet statisch en zullen afhankelijk wat er aan de controller meegegeven wordt \(aan de hand van query parameters in de url bv\) een ander resultaat geven. In dit deel gaan we zien hoe we dit kunnen doen.

## ViewData and ViewBag

### ViewData

`ViewData` is een object waar informatie kan doorgegeven worden tussen de controller en de view. Het object is een Dictionary van string sleutels met object waarden. Je kan er alle soorten types van objecten in opslagen. Het `ViewData` object is zonder extra code altijd beschikbaar in de View.

Om te laten zien hoe we het `ViewData` object gebruiken gaan we een nieuwe action `BrowserInfo` aanmaken in de `HomeController`. Deze action gaat simpelweg informatie over jouw browser laten zien aan de gebruiker. Deze informatie wordt meegestuurd bij elke request die je met je browser doet en is beschikbaar in het `Headers` object.

```csharp
public IActionResult BrowserInfo()
{
    string userAgent = Request.Headers["User-Agent"].ToString();

    ViewData["UserAgent"] = userAgent;

    return View();
}
```

Het `Request` object is in elke controller standaard beschikbaar. Hier kan je een `Headers` object vinden. Hier kan je via de key `User-Agent` de waarde vinden die de informatie over de browser bevat.

Vervolgens gebruiken we de `ViewData` dictionary en plaatsen we deze string waarde in deze dictionary. We maken nu een View `BrowserInfo.cshtml` in de `Views/Home` map.

```csharp
@{
    Layout = "_Layout";
    ViewBag.Title = "Browser Info";
}
<b>@ViewData["userAgent"]</b>
```

Je kan dan gewoon in je html code de waarde uit de ViewData dictionary halen. Let hier op dat je voor elk stuk code in `cshtml` een @ symbool moet zetten.

### ViewBag

Het `ViewBag` object is een soort laag bovenop het `ViewData` object. Het laat toe om complexe objecten zonder cast te kunnen gebruiken. Dit komt omdat het van het type `DynamicViewData` is en dit herkent welk type het opgevraagde object is. Het is ook iets handiger in gebruik dankzij zijn dot notatie manier van toegang. Zo zouden we bovenstaande code kunnen omzetten naar

```csharp
public IActionResult BrowserInfo()
{
    string userAgent = Request.Headers["User-Agent"].ToString();

    ViewBag.UserAgent = userAgent;

    return View();
}
```

en het gebruik is gelijkaardig in de View:

```csharp
@{
    Layout = "_Layout";
    ViewBag.Title = "Browser Info";
}
<b>@ViewBag.UserAgent</b>
```

We spreken af dat we vooral het `ViewBag` object zullen gebruiken omdat dit eenvoudiger in gebruik is.

## Model

Het model is verantwoordelijk voor de informatie die door de toepassing wordt weergegeven. In de context van ASP.NET stelt het model bijvoorbeeld de data die wordt gepost vanuit een formulier voor, de data die moet worden weergegeven in een view,... Het zijn de bedrijfsspecifieke concepten die daar worden weergegeven en doorgegeven aan de controller door middel van klassen.

In ASP.NET is mogelijk om een View aan een specifiek klasse te binden en deze door te geven via de controller. We zullen eerst zien hoe we een eenvoudige string doorgeven.

Eerst moeten we een map `Models` aanmaken waar we onze modellen in zullen opslaan. Hierin maken we de klasse `Product` aan.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Description { get; set; }
    public decimal Price { get; set; }
}
```

We maken hiervoor ook een nieuwe controller `ProductController.cs` aan.

```csharp
public class ProductController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
}
```

We passen de Index action hier aan zodat we eerst een `Product` Object aanmaken via zijn constructor.

```csharp
public class ProductController : Controller
{
    public IActionResult Index()
    {
        Product product = new Product { Id = 1, Name = "Fluffy Llama", Description = "A fluffy llama that is very nice for small children", Price = 13.99M };
        return View(product);
    }
}
```

Zoals je hier boven ziet geven we de Product klasse niet mee aan de hand van een `ViewBag` object maar geven we dit rechtstreeks aan de View functie mee. Zo zal de view gebonden worden met het Product object.

We maken een nieuwe View aan in `Views/Product` genaamd `Index.cshtml`. Omdat we deze view willen binden met het model voor de Product klasse plaatsen we als eerste lijn van de view:

```csharp
@model LlamaStore.Models.Product
```

Als we nu de inhoud van het model willen gebruiken in de view kunnen we dit eenvoudig doen door deze aan te spreken met `@Model.PropertyName`. Zo komen we in ons voorbeeld op:

```markup
@model LlamaStore.Models.Product
@{ ViewBag.Title = "Product"; }

<img src="~/images/products/@(Model.Id).jpg"/>
<p>@Model.Name</p>
<p>@Model.Description</p>
<p>@Model.Price</p>
```

Zorg ervoor dat voor elk product een image bestand wordt gemaakt in een products folder in de images folder. De naam moet bestaan uit de id gevolgd door .jpg.

Omdat we niet voor elk model de hele namespace willen opgeven kunnen we in het `_ViewImports.cs` bestand ook de namespace includeren.

```csharp
@using LlamaStore.Models
```

dan moeten we enkel maar

```text
@model Product
```

opgeven in het View bestand.

Willen we nu een hele lijst van producten meegeven dan is dit ook eenvoudig mogelijk.

```csharp
public IActionResult Index()
{
    var products = new List<Product>
    {
        new Product { Id = 1, Name = "Fluffy Llama", Description = "A fluffy llama that is very nice for small children", Price = 13.99M },
        new Product { Id = 2, Name = "Colorful Llama", Description = "A colorful llama that will make you warm with joy", Price = 20.99M },
    };

    return View(products);
}
```

en de Index.cshtml file:

```markup
@model List<Product>
@{ ViewBag.Title = "Products"; }

@foreach (Product product in Model)
{
    <img src="~/images/products/@(product.Id).jpg"/>
    <p>@product.Name</p>
    <p>@product.Description</p>
    <p>@product.Price</p>
}

```

en dankzij wat Bootstrap magie vormen we dit om tot een mooie tabel.

```aspnet
@model List<Product>
@{ ViewBag.Title = "Products"; }

<table class="table">
    <thead>
        <tr>
            <th scope="col"></th>
            <th scope="col">Name</th>
            <th scope="col">Description</th>
            <th scope="col">Price</th>
        </tr>
    </thead>
    <tbody>
        @foreach (Product product in Model)
        {
        <tr>
            <td><img src="~/images/products/@(product.Id).jpg" width="50" height="50"/></td>
            <td>@product.Name</td>
            <td>@product.Description</td>
            <td>@product.Price</td>
        </tr>
        }
    </tbody>
</table>
```

### Begeleidend videomateriaal

{% embed url="https://ap.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=cdb6bc97-de58-4616-84a7-ac4d007a53c4" %}



