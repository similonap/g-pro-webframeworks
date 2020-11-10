# Filtering, Sortering en Paginering

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

![](.gitbook/assets/image%20%2871%29.png)

We zien nu dat er wel wat producten aanwezig zijn in onze winkel en wordt het moeilijker om het product te vinden wat willen.

### Sortering

Door middel van LINQ quries kunnen we eenvoudig een sortering toevoegen aan onze producten. 

```csharp
public IActionResult Index([FromQuery] string sort = "type")
{
    var products = this.productRepository.GetAll();
    switch (sort)
    {
        case "name":
            products = products.OrderBy(p => p.Name);
            break;
        case "price":
            products = products.OrderBy(p => p.Price);
            break;
        default:
            products = products.OrderBy(p => p.Type);
            break;
    }
    return View(products);
}
```

Als argument van de Index action gebruiken we hier een string voor de sorterings velden mee te geven. We geven hier expliciet aan dat deze parameter van de query string moet komen. In principe is dit niet nodig, maar voor de veiligheid geven we dit toch aan.

Als we nu in de browser naar `Products?sort=price` gaan dan worden onze producten gesorteerd op prijs. Als we deze query parameter niet meegeven zal hij deze standaard sorteren op het type.

Willen we nu ook de sort direction meegeven dan kunnen we dit ook nog doen aan de hand van een extra parameter. We maken eerst voor de richting van het sorteren een enum aan:

```csharp
public enum SortDirection {
    DESC,
    ASC
}
```

en dan passen we onze Index action lichtjes aan zodat we ook kunnen sorteren in andere richtingen:

```csharp
public IActionResult Index([FromQuery] string sort = "type", [FromQuery] SortDirection sortDirection = SortDirection.ASC)
{
    var products = this.productRepository.GetAll();
    switch (sort)
    {
        case "name":
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Name) : products.OrderByDescending(p => p.Name);
            break;
        case "price":
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Price) : products.OrderByDescending(p => p.Price);
            break;
        case "type":
            products = (sortDirection == SortDirection.ASC) ? products.OrderBy(p => p.Type) : products.OrderByDescending(p => p.Type);
            break;
    }
    return View(products);
}
```

Tot nu toe hebben we de sortering gedaan aan de hand van gewoon in de adresbalk de query parameters in te geven. Nu willen we uiteraard dit ook kunnen doen vanuit onze eigen webapplicatie.



