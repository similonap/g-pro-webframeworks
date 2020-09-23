# Tag Helpers

Tag Helpers maken het mogelijk om code te genereren voor bepaalde HTML elementen. Dankzij tag helpers wordt de code die in de razor views worden gebruikt overzichtelijker en beter onderhoudbaar.

## Tag Helpers toevoegen

Voordat een tag helper kan gebruikt worden moet deze eerst worden geactiveerd. Dit doen we aan de hand van het `@addTagHelper` commando. Je kan je eigen tag helpers aanmaken maar wij gaan vooral in deze cursus de standaard tag helpers van ASP.NET gebruiken. Je kan dit doen door:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

bovenaan aan je razor pages toe te voegen. Maar omdat we zo weinig mogelijk code willen dupliceren is er ook een manier om deze toe te voegen aan elke pagina.

We maken een bestand `_ViewImports.cshtml` aan in de map `Views`. Dit bestand wordt standaard door elke razor page geimporteerd en is dus overal beschikbaar. Voeg je hier een taghelper toe dan is deze beschikbaar voor elke pagina. Het \`\_ViewImports.cshtml' bestand bevat dan de volgende code:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

## Nieuwe actie

We gaan nu een nieuwe actie aan onze `HomeController` toevoegen. We willen onze contact informatie toevoegen zodat de gebruikers van onze webapplicatie kunnen zien wie we zijn en hoe ze ons kunnen contacteren. We voegen dus een Contact action toe aan onze home controller.

```csharp
public IActionResult Contact()
{
    return View();
}
```

We maken hiervoor ook een nieuwe view `Contact.cshtml` in `/Views/Home` met de inhoud

  

## Anchor taghelper

De anchor tag helper is gemaakt voor het gemakkelijk genereren van urls voor links in je web applicatie. Normaal gezien maak je altijd een link in je pagina door in html het volgende te schrijven:

```markup
<a href="/Home/BrowserInfo">
```

Zo zal je naar de `BrowserInfo` actie gaan van de `HomeController`. Het is natuurlijk moeilijk om het overzicht te houden en al die links doorheen de pagina zelf te beheren. Daarom gebruiken we de `asp-action` en `asp-controller` tag helper om dit eenvoudiger te maken.

```markup
<a asp-action="BrowserInfo" asp-controller="Home">
```

Nu kunnen we onze algemene navigatie balk aanpassen zodat we gebruik maken van tag helpers:

```markup
<!doctype html>
<html lang="en">
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    <title>@ViewBag.Title</title>
</head>
<body>
    <nav class="navbar navbar-expand-sm navbar-dark bg-dark">
        <a class="navbar-brand" href="#">Eerste Project</a>
        <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navBar" aria-controls="navBar" aria-expanded="false" aria-label="Toggle navigation">
            <span class="navbar-toggler-icon"></span>
        </button>

        <div class="collapse navbar-collapse" id="navBar">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item">
                    <a class="nav-link" asp-controller="Home" asp-action="Index">Home</a>
                </li>
            </ul>
        </div>
    </nav>
    <div class="container">
        @RenderBody()
    </div>

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```

Er zijn uiteraard nog heel veel andere tag helpers. We zullen doorheen deze cursus stap per stap andere tag helpers introduceren.



