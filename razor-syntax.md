# Razor Syntax

Razor is een markup syntax voor het toevoegen van server code in webpaginas. De syntax bestaat uit Razord Markup, C\# en HTML. Je hebt al gebruik gemaakt van Razor pages maar nu gaan we iets meer in detail in de syntax en de mogelijkheden. Razor pages hebben gewoonlijk het .cshtml bestandsformaat.

## Razor syntax

Razor ondersteunt C\# code en gebruikt het `@` symbool om over te schakelen tussen HTML naar C\#. Razor evalueert C\# expressies en zet ze om naar HTML output.

Om `@` te kunnen escapen gebruik je een 2de `@` symbool

```aspnet
<p>first.last@@ap.be</p>
```

de code die dan gegenereerd wordt is

```aspnet
<p>first.last@ap.be</p>
```

Als je rechtstreeks de expressie wil laten zien in je html code dan moet je gewoon een @ typen gevolgd door de expressie. Bijvoorbeeld:

```aspnet
<p>@DateTime.Now.Year</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

Je kan ook complexere expressies uitvoeren zoals een optelling en een aftrekking van waarden, die moet je dan tussen haakjes zetten. Dit noemen we een expliciete expressie:

```aspnet
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

Als je gewoon code wil laten uitvoeren zoals een for lus of een if conditie kan je gebruik maken van de `@{ }` syntax. Zo kunnen we bijvoorbeeld itereren over een array:

```aspnet
@{
    for (int i=0;i<10;i++) {
        <div>i</div>
    }
}
```

Dit zal 10 div tags genereren die elk een getal van 0 tot 10 bevatten. Wil je hier gewoon de waarde van i laten zien zonder de &lt;div&gt; tag moet je ervoor zorgen dat deze voorgegaan wordt met een @ symbool

```aspnet
@{
    for (int i=0;i<10;i++) {
        @i
    }
}
```

### Loops

Loops zoals de for lus hierboven kunnen ook vereenvoudigd worden door het schrijven van de @ voor de loop. 

Stel dat je een array hebt van Person objecten:

```aspnet
@{
    var people = new Person[]
    {
          new Person("Andie", 36),
          new Person("Joske", 41),
          ...
    };
}
```

dan kan je hier in razor pages over itereren op de volgende manieren:

#### for loop

```aspnet
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

**foreach**

```aspnet
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

**while**

```aspnet
@{ int i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```



