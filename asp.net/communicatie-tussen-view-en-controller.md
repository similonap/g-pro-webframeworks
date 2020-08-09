# Communicatie tussen Views en Controler

Tot nu toe waren de Views en de Controllers volledig van elkaar gescheiden. Het enige wat de controller deed was aangeven dat een View moet getoond worden maar er werd geen gegevens tussen beiden doorgegeven. Views zijn normaal gezien niet statisch en zullen afhankelijk wat er aan de controller meegegeven wordt (aan de hand van query parameters in de url bv) een ander resultaat geven. In dit deel gaan we zien hoe we dit kunnen doen.

### ViewBag

`ViewData` is een object waar informatie kan doorgegeven worden tussen de controller en de view. Het object is een Dictionary van string sleutels met object waarden. Je kan er alle soorten types van objecten in opslagen. Het `ViewData` object is zonder extra code altijd beschikbaar in de View. 

Om te laten zien hoe we het `ViewData` object gebruiken gaan we een nieuwe action `BrowserInfo` aanmaken in de `HomeController`. Deze action gaat simpelweg informatie over jouw browser laten zien aan de gebruiker. Deze informatie wordt meegestuurd bij elke request die je met je browser doet en is beschikbaar in het `Headers` object. 

```csharp
public IActionResult BrowserInfo()
{
    string userAgent = Request.Headers["User-Agent"].ToString();

    ViewData["User-Agent"] = userAgent;

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

### Oefeningen

Pas de Time action die je in de vorige oefeningen hebt gemaakt aan zodat deze het uur en de minuten doorgeeft aan de view en deze laat zien aan de gebruiker. Gebruik hiervoor de `ViewBag`.

