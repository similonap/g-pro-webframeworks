# LINQ

LINQ maakt het mogelijk om op arrays en meer algemeen reeksen van gegevens queries uit te voeren die heel gelijkaardig is met SQL queries. Dit gebeurt allemaal rechstreeks in de code. We zullen eerst een aantal basis concepten van LINQ behandelen.

Een **reeks** kan worden gezien als een lijst van items, elk item in de lijst kan je zien als een **element**. Een reeks is een instantie van een klasse die de `IEnumerable<T>` interface implementeert. 

Bijvoorbeeld in de array

```aspnet
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 }
```

stelt de variabele `leeftijden` de reeks voor en de individuele integers in de array een individueel element.

Een reeks hoeft niet een array te zijn of een lijst van objecten in het geheugen \(LINQ-to-objects\). Het kan ook een externe bron zijn zoals een tabel in een SQL database. Je zal dit ook later zien in combinatie met **Entity Framework.** De externe reeksen worden door een `IQueryable<T>` interface voorgesteld.  

Queries, of meer specifiek **query operatoren**, zijn van toepassing op een reeks en produceren 1 of meerdere output waarden. Deze output waarde kan een getransformeerde versie van de input sequentie zijn. Het kan een nieuwe reeks zijn of een enkele waarde.

Een belangrijk ding om op te merken bij het gebruik van query's is dat ze de input reeks niet veranderen. In plaats van de input reeks te veranderen stuurt de query operator een nieuwe reeks \(of een enkele waarde\) terug.

### Scalaire return waarden en outputreeksen

Een query operator returned een ofwel een nieuwe reeks ofwel 1 enkele waarde \(een scalaire waarde\). 

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
// scalar return value
int numberOfElements = leeftijden.Count();
Console.WriteLine("Aantal leeftijden {0}", numberOfElements);

// Output sequence return value
IEnumerable < int > verschillendeLeeftijden = leeftijden.Distinct();

Console.WriteLine("Verschillende leeftijden in de outputreeks: ");
foreach (int leeftijd in leeftijden)
{
    Console.WriteLine(leeftijd);
}
```

In de bovenstaande code wordt er van de reeks getallen de `Count` query operator gebruikt. Deze geeft maar 1 waarde terug omdat het gewoon het aantal elementen van de reeks terug geeft. Dit is dus een scalaire return waarde.  
Er wordt hier ook gebruik gemaakt van de `Distinct` query operator. Die geeft een nieuwe reeks terug die hetzelfde is als de vorige reeks maar waar de dubbele elementen zijn uitgehaald.

### Uitgestelde uitvoering

De meeste queries worden niet onmiddellijk uitgevoerd. De uitvoering ervan wordt uitgesteld tot een later tijdstip in de uitvoering van het programma. Dit betekent dat de query niet wordt uitgevoerd wanneer ze wordt gemaakt, maar wanneer ze wordt gebruikt, bijvoorbeeld wanneer er over geïtereerd wordt met foreach.

Uitgestelde uitvoering betekent dat de ingevoerde sequentie kan worden gewijzigd nadat de query is geconstrueerd, maar dit moet je wel doen voordat de query wordt uitgevoerd. In de volgende code wordt de ingevoerde sequentie gewijzigd nadat de query is geconstrueerd.

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
// scalar return value
// construct the query
IEnumerable<int> ouderDan11 = leeftijden.Where(x => x > 10);

// Change the third element
leeftijden[2] = 100;
// At this point the query has been created but not executed
Console.WriteLine("Ouder dan 11: ");
foreach (int leeftijd in ouderDan11)
{
    Console.WriteLine(leeftijd);
}
```

