# Formulieren

In dit deel gaan we leren hoe we een formulier aanmaken in html code en vervolgens deze doorsturen naar de controller. Ook validatie van de gegevens zal hier kort aan bod komen.

Vooraleer we beginnen met die labo moeten we nog een aantal kleine wijzigingen aanbrengen omdat we nu ook nieuwe producten willen toevoegen met hun eigen image.

We voegen nu de volgende property toe aan de `Product` klasse

```csharp
public string ImageURL { get; set; }
```

we willen nu niet meer werken met vaste paden voor bepaalde id's voor de images, dus we geven nu bij het aanmaken van de producten het pad van de image mee.

```csharp
public ProductsInMemoryRepository()
{
    this.products = new List<Product>();

    Create(new Product { Name = "Fluffy Llama", Description = "A fluffy llama for the kids", Price = 9.99M, ImageURL = "/images/products/1.jpg" });
    Create(new Product { Name = "Colorful llama", Description = "A colorful llama for the larger kids", Price = 19.99M, ImageURL = "/images/products/2.jpg" });

}
```

Nu moeten we uiteraard nog twee kleine dingen aanpassen in de `Index.cshtml` en de `Details.cshtml` view om de ImageUrl te gebruiken als image:

```text
<img src="@product.ImageURL" />
```

## Formulier via argumenten

Voor we beginnen gaan we een link aanmaken op de `Index` view van de `ProductController` die naar de `Create` actie verwijst \(deze bestaat nog niet\). We voegen dus

```csharp
<a asp-controller="Product" asp-action="Create">Add product</a>
```

toe en dan gaan we verder met de `ProductController` die we in vorig deeltje hebben aangemaakt. Hier maken we een nieuwe actie `Create` aan om een nieuw product aan te maken.

```csharp
public IActionResult Create()
{
    return View();
}
```

Momenteel doet deze actie nog niet veel. We moeten ook nog een view aanmaken hiervoor. We maken dus een nieuw bestand `Create.cshthml` met de inhoud:

```markup
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Products - Create"; 
}
<form method="post" asp-controller="Product" asp-action="Create">
    <div class="form-group">
        <label for="Name">Name</label>
        <input class="form-control" id="Name" name="Name">
    </div>
    <div class="form-group">
        <label for="Description">Description</label>
        <input class="form-control" id="Description" name="Description">
    </div>
    <div class="form-group">
        <label for="Price">Price</label>
        <input class="form-control" id="Price" name="Price">
    </div>
    <div class="form-group">
        <label for="ImageURL">ImageURL</label>
        <input class="form-control" id="ImageURL" name="ImageURL">
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

We willen het formulier via een `post` methode versturen. Dit is gangbaar bij formulieren. Het alternatief is via een `get` methode maar dan komen alle doorgestuurde variabelen in de url te staan en dit is niet wenselijk.

Je merkt hierboven dat we gebruik maken van de taghelpers `asp-controller` en `asp-action` om de juiste action te gebruiken voor het formulier. Zo zal het formulier worden doorgestuurd naar de `Create` action van de `ProductController`.

De rest van het formulier is standaard html en gebruik gemaakt van bootstrap om te stijlen. Meer informatie kan je vinden op [https://getbootstrap.com/docs/4.0/components/forms/](https://getbootstrap.com/docs/4.0/components/forms/)

![](../.gitbook/assets/image%20%2861%29.png)

Als we nu het formulier doorsturen door op submit te drukken gebeurt er uiteraard niet veel. We moeten nog controller code schrijven om met deze doorgestuurde gegevens te kunnen werken.

We voegen dus een extra action toe aan de `ProductController`. Deze zal ook `Create` noemen maar zal de andere `Create` action overloaden

```csharp
[HttpPost]
public IActionResult Create([FromForm] string name, [FromForm] string description, [FromForm] decimal price, [FromForm] string imageUrl)
{
    Product product = new Product { Name = name, Description = description, Price = price, ImageURL = imageUrl };

    productRepository.Create(product);
    return RedirectToAction("Index", "Product");
}
```

We overlopen even de nieuwe dingen hier:

* `[HttpPost]` is een `Attribute` die aangeeft dat deze action enkel moet gebruikt worden bij een POST vanuit een formulier. Zo heb je voor alle http methoden \(GET, POST, DELETE,...\) een eigen variant.
* Als argumenten van de methode `Create` gebruiken we de argumenten die we doorsturen vanuit het formulier. De namen moeten overeenkomen met de `name` uit het formulier in html. Dit kan je niet wijzigen, zorg er dus altijd voor dat deze overeenkomen.
* We maken hier een instantie van Product aan met de waarden die zijn doorgegeven. 
* We voegen deze toe aan de `ProductRepository` met de zelfgemaakte `Create` methode.
* Omdat we bij het succesvol toevoegen van een gebruiker terug de lijst van studenten willen laten zien gebruiken we hier de `RedirectToAction` die als eerste argument de `Action` aanneemt en als 2de de `Controller`.

## Model Binding

De manier die we hierboven hebben beschreven is omslachtig. We willen hier gebruik maken van model binding om het formulier automatisch om te zetten naar een object. We passen de `Create` action aan van de `ProductController`

```csharp
[HttpPost]
public IActionResult Create(Product product)
{
    productRepository.Create(product);
    return RedirectToAction("Index", "Product");
}
```

We gebruiken hier gewoon de `Product` klasse als argument van de methode en de omzetting van de form body wordt automatisch voor jou gedaan. Er zijn hier een paar kleine dingen waar je moet op letten:

* Zorg voor een empty constructor als je al een constructor hebt \(als je gewoon de default constructor hebt hoeft dit niet\)

  ```csharp
  public Product()
  {

  }
  ```

* Zorg ervoor dat de properties van de klasse overeenkomen met de name attributen van het form element.

Als je nu terug naar de web applicatie gaat zal je zien dat het gedrag ongewijzigd blijft.

## Model Validatie

Zoals je misschien wel al opgemerkt hebt kan je in ons formulier invullen wat je wil. Laat je velden leeg, dan zal er ook een `Product` aangemaakt worden. Dit willen we uiteraard niet.

Microsoft heeft een zeer effectieve en gemakkelijk te gebruiken gegevensvalidering API ontwikkeld in de kern .NET Framework met de naam Data Annotations. Zoals de naam impliceert, biedt de met de naam Data Annotations API een set .NET attributen die ontwikkelaars kunnen toegepassen op klasse-eigenschappen van data-objecten. Deze eigenschappen bieden een declaratieve manier om validatieregels rechtstreeks op een model toe te passen.

### Verplichte velden

Alle velden in onze `Product` klasse zijn verplicht \(buiten de `Id` momenteel\). Om dit aan te geven gebruiken we het `[Required]` attribuut. We geven dit dus ook aan in de `Product` klasse.

```csharp
[Required]
public string Name { get; set; }
[Required]
public string Description { get; set; }
[Required]
public decimal Price { get; set; }
[Required]
public string ImageURL { get; set; }
```

### Andere attributen

Er zijn nog veel andere attributen. Zo vindt je op de [officiele documentatie](https://docs.microsoft.com/en-us/aspnet/core/mvc/models/validation?view=aspnetcore-3.1#built-in-attributes).

Zo kan bijvoorbeeld het `Range` attribuut wel interessant zijn voor het jaartal van de `EnrollmentYear`. We willen bijvoorbeeld alleen getallen tussen 2010 en 2022 toelaten.

```csharp
[Range(2010, 2021)]
[Required]
public int EnrollmentYear { get; set; }
```

### ModelState

Wanneer het ASP.NET MVC Framework de actiemethode van een controller uitvoert kan je ook de gegevens die worden doorgegeven aan die controller actiemethode validereren. Foutmeldingen worden opgeslagen in een ModelState object. Controller acties kunnen de ModelState opvragen om na te gaan of de request geldig is en die overeenkomstig te reageren. Als een veld niet correct is ingevuld kan je de gebruiker laten terugkeren naar de pagina met invulformulier om de validatie fouten te corrigeren en een nieuwe request in te dienen.

```csharp
[HttpPost]
public IActionResult Create(Product product)
{
    if (ModelState.IsValid)
    {
        productRepository.Create(product);
        return RedirectToAction("Index", "Product");
    } else
    {
        return View();
    }
}
```

### Validatie foutmeldingen tonen

We weten nu al hoe we validatie foutmeldingen moeten maken, maar nog niet hoe we die aan de gebruiker moeten tonen. Dat gebeurt op een andere plaats in de MVC architectuur, namelijk in de view. We gaan dus naar de `Create.cshtml` view.

Als we ons niet te veel van de layout aantrekken en we gewoon een lijstje willen laten zien kunnen we gebruik maken van de `asp-validation-summary` tag helper die een overzicht van alle validatie fouten laat zien in html.

Dan wordt `Create.cshtml` gewoon

```markup
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Products - Create"; 
}
<div asp-validation-summary="All"></div>
<form method="post" asp-controller="Product" asp-action="Create">
   ...
</form>
```

![](../.gitbook/assets/image%20%2863%29.png)

### Toevoegen van fout boodschappen

Je kan de foutboodschappen aanpassen om een meer gebruiksvriendelijke tekst mee te geven aan de gebruiker. Dit kan je doen door aan de data annotaties een parameter `ErrorMessage` mee te geven

```csharp
[Required(ErrorMessage = "Naam is een verplicht veld")]
public string Name { get; set; }

[Required(ErrorMessage ="Beschrijving is een verplicht veld")]
public string Description { get; set; }

[Required]
[Range(0, 999.99, ErrorMessage = "Prijs moet tussen 0 en 1000 euro liggen")]
public decimal Price { get; set; }

[Required(ErrorMessage = "Afbeelding is een verplicht veld")]
public string ImageURL { get; set; }
```

![](../.gitbook/assets/image%20%2864%29.png)

## TempData

We hebben in de `Create` action van de `ProductController` gebruik gemaakt van `RedirectToAction` om na het toevoegen terug naar de lijst van producten te gaan. We zouden graag ook nog een message willen meegeven aan deze view als het toevoegen gelukt is. Je zou denken dat dit een goede use case is voor het gebruik van een `ViewBag` maar als je dit zou proberen zal dit niet werken. Hiervoor bestaat het `TempData` object. Dit object blijft bestaan tussen een redirect dus is hier ideaal voor.

We passen de `Create` action aan zodat er in TempData een message wordt gestoken als het toevoegen gelukt is.

```csharp
[HttpPost]
public IActionResult Create(Product product)
{
    if (ModelState.IsValid)
    {
        productRepository.Create(product);
        TempData["Message"] = $"{product.Name} was added succesfully";
        return RedirectToAction("Index", "Product");
    } else
    {
        return View();
    }
}
```

Nu is het enige wat we nog moeten doen is het aanpassen van de `Index.cshtml` view zodat het dit bericht laat zien als het meegegeven wordt.

```markup
@model IQueryable<Product>
@{ ViewBag.Title = "Product"; }

@if (TempData["Message"] != null)
{
<div class="alert alert-success" role="alert">
    @TempData["Message"]
</div>
}
...
```

![](../.gitbook/assets/image%20%2862%29.png)

## Tag helpers

Je zult hopelijk al opgemerkt hebben dat het vrij veel werk is om de `name` en `id` attributen opgelijnd te houden met het model. Als er iets veranderd in het model dan moeten deze in het formulier ook aangepast worden. Hiervoor zijn er een aantal handige tag helpers.

### Input tag helper

De Input Tag Helper bindt een HTML `<input>` element aan property van het model.

Syntax:

```markup
<input asp-for="<Expression Name>">
```

Deze genereert de `id` en `name` attributen voor het input veld. Het vult ook dynamisch het `type` attribuut in van het input veld gebaseerd op het type in het model.

Zo zal in onze `Create.cshtml` het formulier er al veel overzichtelijker worden:

```markup
@model Product
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Products - Create"; 
}
<div asp-validation-summary="All"></div>
<form method="post" asp-controller="Product" asp-action="Create">
    <div class="form-group">
        <label for="Name">Name</label>
        <input class="form-control" asp-for="Name">
    </div>
    <div class="form-group">
        <label for="Description">Description</label>
        <input class="form-control" asp-for="Description">
    </div>
    <div class="form-group">
        <label for="Price">Price</label>
        <input class="form-control" asp-for="Price">
    </div>
    <div class="form-group">
        <label for="ImageURL">ImageURL</label>
        <input class="form-control" asp-for="ImageURL">
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

Vergeet hier bovenaan niet het model te definieren anders weet asp-for niet naar welk model object hij moet verwijzen.

### Label tag helper

Momenteel maken we nog altijd gewoon gebruik van de html label tag en vullen we deze nog zelf in.

```markup
<label for="Name">Name</label>
```

Dankzij de label tag helpers kunnen we dit ook nog sterk vereenvoudigen. Als we nu dit vervangen met

```markup
<label asp-for="Name"></label>
```

zal er automatisch een label tekst worden gegeven met de naam van de property. Als we toch andere labels willen zien dan moeten we dit in het model aanduiden met `[Display(Name = "Andere label")]`

```csharp
  [Display(Name = "Productnaam")]
  [Required(ErrorMessage = "Naam is een verplicht veld")]
  public string Name { get; set; }
  
  [Display(Name = "Beschrijving")]
  [Required(ErrorMessage ="Beschrijving is een verplicht veld")]
  public string Description { get; set; }
  
  [Display(Name = "Prijs")]
  [Required(ErrorMessage = "Prijs is een verplicht veld")]
  [Range(0, 999.99, ErrorMessage = "Prijs moet tussen 0 en 1000 euro liggen")]
  public decimal Price { get; set; }
  
  [Display(Name = "Afbeelding")]
  [Required(ErrorMessage = "Afbeelding is een verplicht veld")]
  public string ImageURL { get; set; }
```

### Validation tag helper

Hiervoor hebben we de foutbootschappen gewoon bovenaan laten zien na het valideren van het model. Maar wat we echt willen is dat de foutboodschap bij het input veld in kwestie komt staan. Hiervoor hebben we de validation tag helper.

```markup
<span asp-validation-for="Name"></span>
```

Als er dan iemand het formulier doorstuurt en `FirstName` is niet ingevuld dan wordt dit vervangen met de volgende html code

```markup
<span class="field-validation-error" 
         data-valmsg-replace="true" 
         data-valmsg-for="Name"> 
   The Name field is required.</span>
```

Met wat boostrap magie kunnen we deze text ook er duidelijk uit laten zien als een error

```markup
@model Product
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Products - Create"; 
}
<form method="post" asp-controller="Product" asp-action="Create">
    <div class="form-group">
        <label asp-for="Name"></label>
        <input class="form-control" asp-for="Name">
        <div class="invalid-feedback" style="display:block;">
            <span asp-validation-for="Name"></span>
        </div>
    </div>
    <div class="form-group">
        <label asp-for="Description"></label>
        <input class="form-control" asp-for="Description">
        <div class="invalid-feedback" style="display:block;">
            <span asp-validation-for="Description"></span>
        </div>
    </div>
    <div class="form-group">
        <label asp-for="Price"></label>
        <input class="form-control" asp-for="Price">
        <div class="invalid-feedback" style="display:block;">
            <span asp-validation-for="Price"></span>
        </div>
    </div>
    <div class="form-group">
        <label asp-for="ImageURL"></label>
        <input class="form-control" asp-for="ImageURL">
        <div class="invalid-feedback" style="display:block;">
            <span asp-validation-for="ImageURL"></span>
        </div>
    </div>
        <button type="submit" class="btn btn-primary">Submit</button>
</form>
```

![](https://github.com/similonap/g-pro-webframeworks/tree/68ccc05b565fea247dd6aecc7d351478cdb145f5/.gitbook/assets/ExtraEmailFIeld1.png) ![](https://github.com/similonap/g-pro-webframeworks/tree/68ccc05b565fea247dd6aecc7d351478cdb145f5/.gitbook/assets/ExtraEmailFIeld2.png)

