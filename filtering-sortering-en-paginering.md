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

### Paginering

