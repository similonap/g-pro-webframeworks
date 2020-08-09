# Tag Helpers

Tag Helpers maken het mogelijk om code te genereren voor bepaalde HTML elementen. Dankzij tag helpers wordt de code die in de razor views worden gebruikt overzichtelijker en beter onderhoudbaar. 

### Tag Helpers toevoegen

Voordat een tag helper kan gebruikt worden moet deze eerst worden geactiveerd. Dit doen we aan de hand van het `@addTagHelper` commando. Je kan je eigen tag helpers aanmaken maar wij gaan vooral in deze cursus de standaard tag helpers van ASP.NET gebruiken. Je kan dit doen door:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

bovenaan aan je razor pages toe te voegen. Maar omdat we zo weinig mogelijk code willen dupliceren is er ook een manier om deze toe te voegen aan elke pagina. 

We maken een bestand `_ViewImports.cshtml` aan in de map `Views`. Dit bestand wordt standaard door elke razor page geimporteerd en is dus overal beschikbaar. Voeg je hier een taghelper toe dan is deze beschikbaar voor elke pagina. Het `_ViewImports.cshtml' bestand bevat dan de volgende code:

```csharp
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```

### Anchor taghelper

De anchor tag helper is gemaakt voor het gemakkelijk genereren van urls voor links in je web applicatie. Normaal gezien maak je altijd een link in je pagina door in html het volgende te schrijven:

```html
<a href="/Home/">


