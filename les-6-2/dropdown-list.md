# Dropdown List

We gaan nu eens kijken hoe we om kunnen gaan met enums en hoe deze kunnen getoond worden aan de hand van een dropdown list.

We maken eerst een enum `ProductType` in onze Models directory

```csharp
public enum ProductType
{
    [Display(Name = "Stuffed Animal")] StuffedAnimal,
    [Display(Name = "Plastic Toy")] PlasticToy,
    [Display(Name = "Board Game")] BoardGame,
    [Display(Name = "Computer Game")] ComputerGame,
}
```

We willen natuurlijk niet dat de enum values in deze dropdown list komen, daarom hebben we dus de `Display` annotatie meegegeven met een meer gepaste naam. We voegen vervolgens een nieuwe property `Type` toe aan de `Product` klasse zodat onze producten ook een type hebben.

```csharp
public ProductType Type { get; set; }
```

We moeten uiteraard ook de types toevoegen in onze `ProductsInMemoryRepository` 

```csharp
public ProductsInMemoryRepository()
{
    this.products = new List<Product>();

    Create(new Product { Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M, ImageURL = "~/images/products/1.jpg", Type = ProductType.StuffedAnimal });
    Create(new Product { Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M, ImageURL = "~/images/products/2.jpg", Type = ProductType.StuffedAnimal });

}
```

Hoe zorgen we er nu voor dat voor dit type een select box in html wordt gegenereerd in de `Create` view?

Eenvoudig! We voegen gewoon de volgende html code toe aan onze view.

```aspnet
<div class="form-group">
    <select asp-for="Type" asp-items="Html.GetEnumSelectList<ProductType>()"></select>
</div>
```

Alle items worden voor ons gegenereerd aan de hand van de Html.GetEnumSelectList functie en meegegeven via de asp-items taghelper.

