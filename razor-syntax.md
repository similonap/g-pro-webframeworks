# Razor Syntax

Razor is een markup syntax voor het toevoegen van server code in webpaginas. De syntax bestaat uit Razord Markup, C\# en HTML. Je hebt al gebruik gemaakt van Razor pages maar nu gaan we iets meer in detail in de syntax en de mogelijkheden. Razor pages hebben gewoonlijk het .cshtml bestandsformaat.

## Razor syntax

Razor ondersteunt C\# code en gebruikt het `@` symbool om over te schakelen tussen HTML naar C\#. Razor evalueert C\# expressies en zet ze om naar HTML output.

Om `@` te kunnen escapen gebruik je een 2de `@` symbool

```aspnet
<p>Meetup @@ AP</p>
```

de code die dan gegenereerd wordt is

```aspnet
<p>Meetup @ AP</p>
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

Als je in een code block een HTML tag wil printen kan je die gewoon in de block zelf plaatsen en dan wordt die uitgeprint.

```aspnet
@{
    int number = 14;
    number++;
    <p>@number</p>
}
```

### Loops

Loops zoals de for lus hierboven kunnen ook vereenvoudigd worden door het schrijven van de @ voor de loop. 

Stel dat je een array hebt van Person objecten:

```aspnet
@{
    var people = new SpelenMetRazor.Models.Person[]
    {
          new SpelenMetRazor.Models.Person { Name = "Andie", Age = 36 },
          new SpelenMetRazor.Models.Person  { Name = "Joske", Age = 41 },
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
    <p>Naam: @person.Name</p>
    <p>Leeftijd: @person.Age</p>
}
```

**foreach**

```aspnet
@foreach (var person in people)
{
    <p>Naam: @person.Name</p>
    <p>Leeftijd: @person.Age</p>
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

### Controle structuren

Ook controle structuren hebben een verkorte notatie in razor pages

#### if

```aspnet
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

bij de `else` en `elseif` hoef je dan geen @ symbool te plaatsen:

```aspnet
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

**switch**

```aspnet
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### Comments

Razor ondersteunt ook C\# en HTML comments

```text
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

Als je dan naar de bron code gaat kijken van je HTML pagina gaat enkel de commentaar in HTML zichtbaar zijn. Dus:

```text
<!-- HTML comment -->
```

### Directives

#### @functions

Wil je een functie toevoegen aan je razor page dan moet je deze plaatsen in een `@functions {}` block

```aspnet
@functions {
    public string GetHello()
    {
        return "Hello";
    }
}

<div>From method: @GetHello()</div> 
```

#### @using

Wil je een bepaalde namespace importeren zodat je de klassen in die namespace kan gebruiken kan je dat doen met @using

```aspnet
@using System.IO
@{
    var dir = Directory.GetCurrentDirectory();
}
<p>@dir</p>
```

