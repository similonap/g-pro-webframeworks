# Routering

## Conventionele routing

We hebben in een van de voorgaande hoofdstukken geleerd hoe we in de `Startup` klasse routing op zetten aan de hand van een pattern:

```csharp
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllerRoute(
        name: "default",
        pattern: "{controller=Home}/{action=Index}/{id?}");
});
```

Dit is een voorbeeld van conventionele routing. Het noemt conventionele routing omdat het een conventie vastlegt voor URL paden. Even een herhaling van wat dit allemaal weer betekende:

* Het `{controller=Home}` deel komt overeen met de controller naam en zet deze default op Home als deze weggelaten wordt.
* Het `{action=Index}` gedeelte komt overeen met de actie
* en het laatste deel is gebruikt voor een optionele id. De `?` in het pad maakt het optioneel. 

Dus bijvoorbeeld:

* `/Products/List` komt overeen met de `ProductsController.List` action.
* `/Blog/Article/17` mapt naar de `BlogController.Article` en bindt de id parameter aan 17.

Omdat we gebruik maken van conventionele routing moeten we niet voor elke actie een nieuw pad definieren en helpt dit om consistentie in de urls te bewaren.

Voordat we verder gaan gaan we even de lijst met producten in een private field zetten zodat deze beschikbaar is voor alle acties

```csharp
private List<Product> products = new List<Product>
{
    new Product { Id = 1, Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M },
    new Product { Id = 2, Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M }
};
```

We gaan nu een Detail page aanmaken voor de producten zodat we elk product in detail kunnen bekijken door door te klikken op de overzichtspagina. We willen dit volgens het pad `/Product/Details/{id}` doen. We maken dus een nieuwe action `Details` aan in de `Product` controller.

```csharp
public IActionResult Details(int id)
{
    foreach (Product product in products)
    {
        if (product.Id == id)
        {
            return View(product);
        }
    }
    return NotFound();
}
```

Zoals je ziet zal de argument `id` automatisch gemapped worden naar de parameter in het pad. We gebruiken onze `products` lijst hier om de gevraagde student op te vragen. Als deze niet gevonden wordt  geven we `NotFound` terug wat zal mappen naar een http error code 404. Als deze wel gevonden wordt dan zal de details page worden getoond, dit zal weer een formulier zijn om de student aan te passen. We maken dus een view `Details.cshtml` aan.

```markup
@model Product
@{
    ViewBag.Title = "Products - Detail";
}
<img src="~/images/products/@(Model.Id).jpg" />
<p>@Model.Name</p>
<p>@Model.Description</p>
<p>@Model.Price</p>
```

### Route Tag Helper

We hebben al hiervoor geleerd hoe we links maken aan de hand van de link tag helper. Zo kunnen we dus een link maken naar de `Details` action van de `ProductController`. Maar we missen hier nog een manier om de `id` mee te geven van de route. Hiervoor hebben we de `asp-route-*` tag helper. De \* kan je hier vervangen met de naam van het argument. In dit geval is dit dus `asp-route-id`.

Een link naar de detail pagina kan dan zijn

```markup
<a asp-action="Details" asp-controller="Product" asp-route-id="2">Detail</a>
```

