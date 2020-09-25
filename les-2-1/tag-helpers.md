# Tag Helpers

Tag Helpers maken het mogelijk om code te genereren voor bepaalde HTML elementen. Dankzij tag helpers wordt de code die in de razor views worden gebruikt overzichtelijker en beter onderhoudbaar.

We beginnen deze keer nogmaals met een **nieuw project**. We noemen dit project "**LlamaStore**". We gaan een winkel bouwen die llama's verkoopt. Volg hiervoor de instructies gelijkaardig aan Les1. We maken een HomeController met Index pagina. Oefening baart kunst, daarom doen we het hier nog een keer.

## Nieuwe action toevoegen

We gaan nu een nieuwe actie aan onze `HomeController` toevoegen. We willen onze contact informatie toevoegen zodat de gebruikers van onze webapplicatie kunnen zien wie we zijn en hoe ze ons kunnen contacteren. We voegen dus een Contact action toe aan onze home controller.

```csharp
public IActionResult Contact()
{
    return View();
}
```

We maken hiervoor ook een nieuwe view `Contact.cshtml` in `/Views/Home` met de inhoud:

```markup
<strong>The Llama store</strong>
<p>Lange Nieuwstraat 101</p>
<p>2000 Antwerpen</p>
```

In de `Index.cshtml` voegen we een link toe naar deze nieuwe actie in onze HomeController:

```markup
<h1>Hello Llama</h1>
<a href="/Home/Contact">Contact Us</a>
<img src="/images/llama.gif"/>
```

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

## Anchor taghelper

De anchor tag helper is gemaakt voor het gemakkelijk genereren van urls voor links in je web applicatie. Hiervoor maakten we een link op de volgende manier

```markup
<a href="/Home/Contact">
```

Zo zal je naar de `Contact` actie gaan van de `HomeController`. Het is natuurlijk moeilijk om het overzicht te houden en al die links doorheen de pagina zelf te beheren. Daarom gebruiken we de `asp-action` en `asp-controller` tag helper om dit eenvoudiger te maken.

```markup
<a asp-controller="Home" asp-action="Contact">
```

Nu kunnen we onze link naar de Contact pagina aanpassen op de `Index.cshtml` pagina. 

```csharp
<h1>Hello Llama</h1>
<a asp-controller="Home" asp-action="Contact">Contact Us</a>
<img src="/images/llama.gif"/>
```

We voegen ook nog een link terug naar onze `Index` action in de `Contact.cshtml` view:

```csharp
<div><a asp-controller="Home" asp-action="Index">Home</a></div>
<strong>The Llama store</strong>
<p>Lange Nieuwstraat 101</p>
<p>2000 Antwerpen</p>
```

## Begeleidend Videomateriaal

{% embed url="https://ap.cloud.panopto.eu/Panopto/Pages/Viewer.aspx?id=d612bd84-82f4-4544-9b26-ac4100b90d7b" %}



