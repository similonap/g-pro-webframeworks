# Sorteren, pagineren en filteren

Als voorbereiding van deze les gaan we even een aantal nieuwe producten toevoegen aan onze `ProductsInMemoryRepository` want we gaan iets meer data willen hebben waarmee we kunnen werken in onze voorbeelden. We voegen dus de volgende producten toe in de constructor van onze `ProductsInMemoryRepository` 

```csharp
Create(new Product { Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M, ImageURL = "~/images/products/1.jpg", Type = ProductType.StuffedAnimal });
Create(new Product { Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M, ImageURL = "~/images/products/2.jpg", Type = ProductType.StuffedAnimal });
Create(new Product { Name = "World of Llamacraft", Description = "An online RPG about llamas", Price = 59.99M, ImageURL = "~/images/products/3.png", Type = ProductType.ComputerGame });
Create(new Product { Name = "L.A.M.A", Description = "Llama card game", Price = 9.99M, ImageURL = "~/images/products/4.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "Altiplano", Description = "Flying llamas for everyone", Price = 12.99M, ImageURL = "~/images/products/5.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "Llamas unleashed", Description = "A fast paced llama game about crazy llamas", Price = 12.99M, ImageURL = "~/images/products/6.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "Leipe lama's", Description = "A card game about very dodgy llamas", Price = 12.99M, ImageURL = "~/images/products/7.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "Llama drama", Description = "Llamas can cause drama too", Price = 39.99M, ImageURL = "~/images/products/8.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "Llama lineup", Description = "Whodunnit game about llamas", Price = 20.99M, ImageURL = "~/images/products/9.jpg", Type = ProductType.BoardGame });
Create(new Product { Name = "VALA", Description = "Vicious llama apocalypse", Price = 49.99M, ImageURL = "~/images/products/10.jpg", Type = ProductType.ComputerGame });
Create(new Product { Name = "Booty shaking llama", Description = "a booty shaking llama", Price = 12.99M, ImageURL = "~/images/products/11.jpg", Type = ProductType.PlasticToy });
Create(new Product { Name = "Furreal llama", Description = "Furreal llama", Price = 12.99M, ImageURL = "~/images/products/12.jpg", Type = ProductType.StuffedAnimal });
Create(new Product { Name = "Fortnite llama", Description = "Fortnite llama", Price = 22.99M, ImageURL = "~/images/products/13.jpg", Type = ProductType.StuffedAnimal });
Create(new Product { Name = "Lego Llama", Description = "Lego llama", Price = 22.99M, ImageURL = "~/images/products/14.jpg", Type = ProductType.StuffedAnimal });
```

en we voegen een aantal nieuwe images toe aan onze `wwwroot/images/products` map:

{% file src=".gitbook/assets/images.zip" caption="Product images" %}

![](.gitbook/assets/image%20%2872%29.png)

We zien nu dat er wel wat producten aanwezig zijn in onze winkel en wordt het moeilijker om het product te vinden wat willen.

### Sortering

Door middel van LINQ quries kunnen we eenvoudig een sortering toevoegen aan onze producten. We maken eerst een enum aan voor de velden aan te duiden waarop gesorteerd kan worden:

```csharp
public enum SortField
{
    Name,
    Price,
    Description,
    Type
}
```

```csharp
public IActionResult Index([FromQuery] SortField sort = SortField.Type)
{
    var products = this.productRepository.GetAll();
    switch (sort)
    {
        case SortField.Name:
            products = products.OrderBy(p => p.Name);
            break;
        case SortField.Price:
            products = products.OrderBy(p => p.Price);
            break;
        case SortField.Description:
            products = products.OrderBy(p => p.Description);
            break;
        case SortField.Type:
            products = products.OrderBy(p => p.Type);
            break;
    }
    return View(products);
}
```

Als argument van de Index action gebruiken we hier de enum waarde voor de sorterings velden mee te geven. We geven hier expliciet aan dat deze parameter van de query string moet komen. In principe is dit niet nodig, maar voor de veiligheid geven we dit toch aan.

Als we nu in de browser naar `Products?sort=price` gaan dan worden onze producten gesorteerd op prijs. Als we deze query parameter niet meegeven zal hij deze standaard sorteren op het type.

We willen uiteraard niet enkel kunnen sorteren door de url aan te passen, maar we willen ook kunnen sorteren vanuit onze webapplicatie zelf. We passen de view van de Index action aan zodat de headers van de kolommen kunnen aangeklikt worden om te sorteren:

```markup
<th scope="col"><a asp-action="Index" asp-route-sort="Name">Name</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Description">Description</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Price">Price</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Type">Type</a></th>
```

Willen we nu ook de sort direction meegeven dan kunnen we dit ook nog doen aan de hand van een extra parameter. We maken eerst voor de richting van het sorteren een enum aan:

```csharp
public enum SortDirection {
    DESC,
    ASC
}
```

en dan passen we onze Index action lichtjes aan zodat we ook kunnen sorteren in andere richtingen:

```csharp
public IActionResult Index([FromQuery] SortField sort = SortField.Type, [FromQuery] SortDirection sortDirection = SortDirection.ASC)
{
    var products = this.productRepository.GetAll();
    switch (sort)
    {
        case SortField.Name:
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Name) : products.OrderByDescending(p => p.Name);
            break;
        case SortField.Price:
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Price) : products.OrderByDescending(p => p.Price);
            break;
        case SortField.Description:
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Description) : products.OrderByDescending(p => p.Description);
            break;
        case SortField.Type:
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Type) : products.OrderByDescending(p => p.Type);
            break;
    }
    ViewBag.SortDirection = sortDirection;
    ViewBag.SortField = sort;    
    return View(products);
}
```

Merk op dat we op het einde onze sorteer richting en veld meegeven in een ViewBag object zodat we deze kunnen opvragen vanuit onze view code.

Bovenaan onze `Index.cshtml` maken we een nieuwe variabele aan die bepaald wat de nieuwe sorteerrichting is. Dit komt er op neer dat we gewoon de omgekeerde richting nemen dan die van diegene die in de ViewBag is opgeslagen

```text
@{
    var newSortDirection = ViewBag.SortDirection == LlamaStore.Controllers.SortDirection.ASC ? LlamaStore.Controllers.SortDirection.DESC : LlamaStore.Controllers.SortDirection.ASC;
}
```

en dan kunnen we in de table header:

```csharp
<th scope="col"><a asp-action="Index" asp-route-sort="Name" asp-route-sortDirection="@newSortDirection">Name</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Description" asp-route-sortDirection="@newSortDirection">Description</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Price" asp-route-sortDirection="@newSortDirection">Price</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Type" asp-route-sortDirection="@newSortDirection">Type</a></th>
```

doen.

### Paginering

We willen nu gaan beperken hoeveel producten er maximaal op de pagina mogen staan om te voorkomen dat we hele grote lijsten van producten gaan laten zien aan de gebruiker wat het overzicht moeilijker maakt. We defineren bovenaan de ProductController klasse een static int field dat de grootte van de paginas bevat.

```csharp
public static int PAGE_SIZE = 5;
```

Vervolgens voegen we een extra query parameter toe voor de pagina mee te geven aan de Index action en passen we een aantal dingen aan:

```csharp
public IActionResult Index([FromQuery] SortField sort = SortField.Type, [FromQuery] SortDirection sortDirection = SortDirection.ASC, [FromQuery] int page = 1)
{
    ...
    ViewBag.CurrentPage = page;
    ViewBag.TotalPages = Math.Ceiling((double) products.Count() / PAGE_SIZE);
    return View(products.Skip((page-1) * PAGE_SIZE).Take(PAGE_SIZE));
}
```

We plaatsen dus de huidige pagina terug in de ViewBag en berekenen het aantal paginas er zijn in totaal. Dit doen we door het aantal producten te nemen en dit te delen door de PAGE\_SIZE. We moeten dit wel omzetten naar double zodat we met kommagetallen werken en dan moeten we dit naar boven afronden.

Vervolgens geven we nu niet meer de hele reeks producten mee maar slagen we de eerste paginas over door middel van `Skip` en nemen we met `Take` maar het aantal elemeten dat in `PAGE_SIZE` is opgegeven.  

We kunnen dit nu allemaal uitproberen door naar `Product?page=1`, `Product?page=2`, `Product?page=3` te gaan. Je ziet dat het nu allemaal blokken 5 zijn en dat op elke pagina andere items staan. Nu willen we nog onze view aanpassen zodat we ook daar de pagina's kunnen aanduiden.

Bovenaan het `Index.cshtml` plaatsen we nu

```csharp
<ul class="pagination">
    @for (int i = 1; i < ViewBag.TotalPages + 1; i++)
    {
        <li class="page-item @(i==ViewBag.CurrentPage?"active":"")"><a class="page-link" asp-action="Index" asp-route-page="@i" asp-route-sort="@ViewBag.SortField" asp-route-sortDirection="@ViewBag.SortDirection">@i</a></li>
    }
</ul>
```

We maken hier een lus van 1 tot het aantal paginas dat in de ViewBag zit \(+1 want we indexeren vanaf 1 niet 0\) en dan maken we een nieuwe link voor elke pagina. We markeren het list item als 'active' als de huidige index overeenkomt met de huidige pagina. Merk ook op dat we hier ook terug de sort en de sortDirection meegeven anders zouden we deze kwijtspelen als we naar de volgende pagina zouden gaan.

![](.gitbook/assets/image%20%2871%29.png)

### ViewModel

We zijn tot nu toe al heel de tijd dingen in de `ViewBag` aan het steken. Zoals de SortDirection, SortField,... Wat we nu willen doen is hiervoor een klasse aanmaken waarin we die properties kunnen steken en vervolgens onze view willen mee binden ipv met de lijst van producten zelf. Omdat we nu specifiek een klasse aanmaken voor de view noemen we deze klasse geen `Model` maar een `ViewModel` en plaatsen we die klasse ook in een aparte directory `ViewModels`

```csharp
namespace LlamaStore.ViewModels
{
    public class ProductListViewModel
    {

        public SortDirection SortDirection { get; set; }
        public SortField SortField { get; set; }
        public int CurrentPage { get; set; }    
        public int TotalPages { get; set; }
        public IEnumerable<Product> Products { get; set; }

    }
}
```

We passen nu ook onze Index action aan zodat deze niet meer gebruik maakt van de ViewBag maar van het ViewModel:

```csharp
ProductListViewModel productListViewModel = new ProductListViewModel
{
    SortDirection = sortDirection,
    SortField = sort,
    CurrentPage = page,
    TotalPages = (int)Math.Ceiling((double)products.Count() / PAGE_SIZE),
    Products = products.Skip((page - 1) * PAGE_SIZE).Take(PAGE_SIZE)
};

return View(productListViewModel);
```

We voegen nu ook `@using LlamaStore.ViewModels` toe aan de `_ViewImports.cshtml` file toe zodat deze namespace ook beschikbaar is vanuit alle views.

Nu moeten we uiteraard ook nog een aantal aanpassingen doen aan de `Index.cshtml` file want nu willen we niet meer binden met de IQueryable&lt;Product&gt; maar met `ProductListViewModel` dit doen we met 

```text
@model ProductListViewModel
```

toe te voegen bovenaan de pagina. Nu moeten we enkel de pagina nog aanpassen zodat we overal waar we ViewBag gebruiken nu in de plaats `Model` gebruiken.

```csharp
@{
    var newSortDirection = Model.SortDirection == LlamaStore.Controllers.SortDirection.ASC ? LlamaStore.Controllers.SortDirection.DESC : LlamaStore.Controllers.SortDirection.ASC;
}
```

```text
@for (int i = 1; i < Model.TotalPages + 1; i++)
{
    <li class="page-item @(i==Model.CurrentPage?"active":"")"><a class="page-link" asp-action="Index" asp-route-page="@i" asp-route-sort="@Model.SortField" asp-route-sortDirection="@Model.SortDirection">@i</a></li>
}
```

```csharp
@foreach (Product product in Model.Products)
{
    <tr style="height: 200px">
        <td><img src="@Url.Content(product.ImageURL)" width="100" /></td>
        <td><a asp-controller="Product" asp-action="Details" asp-route-id="@product.Id">@product.Name</a></td>
        <td><p>@product.Description</p></td>
        <td><p>@product.Price</p></td>
        <td><p>@Html.DisplayFor(m => product.Type)</p></td>
    </tr>
}
```

We starten alles nog eens op en kijken na of alles nog werkt. 

### Filtering

We willen nu ook de gebruiker de mogelijkheid geven om te filteren op het type van producten. Dit gaan we doen door nog een extra argument toe te voegen aan de Index action.

```csharp
public IActionResult Index([FromQuery] SortField sort = SortField.Type, [FromQuery] SortDirection sortDirection = SortDirection.ASC, [FromQuery] int page = 1, [FromQuery] ProductType? filter = null)
{
    ...

    if (filter != null)
    {
        products = products.Where(p => p.Type == filter);
    }

    ...
    ViewBag.Filter = filter;
    return View(products.Skip((page-1) * PAGE_SIZE).Take(PAGE_SIZE));
}
```

Merk op dat we achter het type van de ProductType een vraagteken plaatsen. Dit moeten we doen anders aanvaard deze parameter geen null value. En we gebruiken de null value als default om niets te filteren.

Nu kunnen we bijvoorbeeld naar `Product?filter=StuffedAnimal` surfen en dan krijgen we alleen de StuffedAnimals te zien.

Zoals bij filtering en sortering gaan we uiteraard dit ook nog toevoegen in onze view.

```markup
<form method="get" asp-action="Index">
    <select asp-items="Html.GetEnumSelectList<ProductType>()" name="Filter">
        <option value="">All</option>
    </select>
    <button type="submit">Filter</button>
</form>
```

Dit maakt een form element aan om via de get methode \(dus via query parameters\). Daarin hebben we een dropdown box met alle mogelijkheden in van een `ProductType` . We plaatsen hier nog wel een `All` option om terug onze filtering af te zetten. Let er op dat je bij het sorteren ook 

```text
asp-route-filter="@ViewBag.Filter"
```

toevoegd. Anders zal de filter verdwijnen bij het sorteren. Zo komen we dan op

```text
<th scope="col"><a asp-action="Index" asp-route-sort="Name" asp-route-sortDirection="@newSortDirection" asp-route-filter="@ViewBag.Filter">Name</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Description" asp-route-sortDirection="@newSortDirection" asp-route-filter="@ViewBag.Filter">Description</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Price" asp-route-sortDirection="@newSortDirection" asp-route-filter="@ViewBag.Filter">Price</a></th>
<th scope="col"><a asp-action="Index" asp-route-sort="Type" asp-route-sortDirection="@newSortDirection" asp-route-filter="@ViewBag.Filter">Type</a></th>
```



