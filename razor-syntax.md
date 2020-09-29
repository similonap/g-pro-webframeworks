# Razor Syntax

Razor is een markup syntax voor het toevoegen van server code in webpaginas. De syntax bestaat uit Razord Markup, C\# en HTML. Je hebt al gebruik gemaakt van Razor pages maar nu gaan we iets meer in detail in de syntax en de mogelijkheden. Razor pages hebben gewoonlijk het .cshtml bestandsformaat.

### Razor syntax

Razor ondersteunt C\# code en gebruikt het `@` symbool om over te schakelen tussen HTML naar C\#. Razor evalueert C\# expressies en zet ze om naar HTML output.

Om `@` te kunnen escapen gebruik je een 2de `@` symbool

```text
<p>first.last@@ap.be</p>
```





###   <a id="rendering-html"></a>

