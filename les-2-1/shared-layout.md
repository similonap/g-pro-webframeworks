# Gedeelde layout

Veel web applicaties gebruiken een gedeelde layout die op meerdere paginas zal worden gebruikt. We willen bijvoorbeeld dat op elke pagina een navigatie balk staat bovenaan, een footer om onze bedrijfsinformatie in te laten zien,...

![](../.gitbook/assets/image%20%2817%29.png)

Wat we zouden kunnen doen is op elke pagina opnieuw deze code copy pasten, maar dat is uiteraard niet de bedoeling volgens het DRY principe \(Don't repeat yourself!\). We gaan dus een stuk van onze html pagina apart plaatsen zodat we deze kunnen hergebruiken.

Per conventie noemen we de default layout voor een ASP.NET Core applicatie `_Layout.cshtml` en deze plaatsen we in de `Shared` map. Deze moeten we nog zelf aanmaken dus maak een map genaamd `Shared` in de `Views` map.

![](../.gitbook/assets/image%20%2816%29.png)

Omdat we toch stilaan van mooie UI elementen willen gaan gebruiken in onze applicatie gaan we Bootstrap gebruiken. Dit is een uitgebreide frontend library die het eenvoudig maakt om simpele web applicaties te stijlen. De inhoud van `_Layout.cshtml` wordt dan

```markup
<!doctype html>
<html lang="en">
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    <title>The llama store - @ViewBag.Title</title>
</head>
<body>
    @RenderBody()

    <!-- Optional JavaScript -->
    <!-- jQuery first, then Popper.js, then Bootstrap JS -->
    <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
    <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
</body>
</html>
```

Even tekst en uitleg:

* `@ViewBag.Title`  Omdat we elke pagina een eigen titel willen geven die in de browser zal te zien zijn geven moeten we deze informatie kunnen doorgeven. Het ViewBag object is normaal bedoeld om te kunnen communiceren tussen Controller en View, maar kan ook gebruikt worden om waarden door te geven tussen een individuele view en de gedeelde layout. We gaan er vanuit dat de individuele view een `Title` meegeeft.
* `@RenderBody()` Hier gaat de inhoud van de individuele pagina gerendered worden. Het is alsof de inhoud van de individuele view op deze plaats ingeplakt wordt voor elke pagina.

Bovenaan de individuele paginas \(Index en Contact\) zetten we dan

* `Layout = "_Layout"` Dit geeft aan welke gedeelde layout deze pagina zal gebruiken. In dit geval is dat de `_Layout.cshtml` pagina
* `ViewBag.Title = "Titel hier"` Hier geven we de titel mee die we in de gedeelde layout willen doorgeven.
* De rest is gewoon de body die zal worden getoond door de `renderBody()` functie.

De inhoud van het `Index.cshtml` bestand zal er dan zo uit zien:

```aspnet
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Home"; 
}
<h1>Hello llama</h1>
<div><a asp-controller="Home" asp-action="Contact">Contact Us</a></div>
<img src="/images/llama.gif" />
```

We doen hetzelfde ook voor de `Contact.cshtml` view:

```aspnet
@{ 
    Layout = "_Layout";
    ViewBag.Title = "Contact Us"; 
}
<div><a asp-controller="Home" asp-action="Index">Home</a></div>

<strong>The llama store</strong>
<p>Lange Nieuwstraat 101</p>
<p>2000 Antwerpen</p>
```

Als we nu terug de web applicatie starten dan zien we terug Hello World. De tekst zal er iets anders uitzien omdat we nu bootstrap gebruiken.

We gaan nu het bestand `_Layout.cshtml` aanpassen en een navigatiebalk toevoegen\(via bootstrap\). We zullen later meer in detail treden over een aantal features van bootstrap en hoe te gebruiken.

```aspnet
<!doctype html>
<html lang="en">
<head>
    <!-- Required meta tags -->
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Bootstrap CSS -->
    <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">

    <title>The llama store - @ViewBag.Title</title>
</head>
<body>
    <nav class="navbar navbar-expand-sm navbar-dark bg-dark">
        <a class="navbar-brand" href="#">The llama store</a>
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
        
        <div class="collapse navbar-collapse" id="navBar">
            <ul class="navbar-nav mr-auto">
                <li class="nav-item">
                    <a class="nav-link" asp-controller="Home" asp-action="Contact">Contact</a>
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

Als we nu terug de web applicatie starten dan krijgen we al een mooier resultaat te zien.

