# Razor Syntax

Razor is een markup syntax voor het toevoegen van server code in webpaginas. De syntax bestaat uit Razord Markup, C\# en HTML. Je hebt al gebruik gemaakt van Razor pages maar nu gaan we iets meer in detail in de syntax en de mogelijkheden. Razor pages hebben gewoonlijk het .cshtml bestandsformaat.

### Razor syntax

Razor ondersteunt C\# code en gebruikt het `@` symbool om over te schakelen tussen HTML naar C\#. Razor evalueert C\# expressies en zet ze om naar HTML output.

Om `@` te kunnen escapen gebruik je een 2de `@` symbool

```aspnet
<p>first.last@@ap.be</p>
```

de code die dan gegenereerd wordt is

```aspnet
<p>first.last@ap.be</p>
```

Als je rechstreeks de expressie wil laten zien in je html code dan moet je gewoon een @ typen gevolgd door de expressie. Bijvoorbeeld:

```aspnet
<p>@DateTime.Now.Year</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Je kan ook complexere expressies uitvoeren zoals een optelling en een aftrekking van waarden, die moet je dan tussen haakjes zetten. Dit noemen we een expliciete expressie:

```aspnet
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

###   <a id="rendering-html"></a>

