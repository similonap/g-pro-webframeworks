# Introductie

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

![](../.gitbook/assets/image%20%2831%29.png)

### Uitgestelde uitvoering

De meeste queries worden niet onmiddellijk uitgevoerd. De uitvoering ervan wordt uitgesteld tot een later tijdstip in de uitvoering van het programma. Dit betekent dat de query niet wordt uitgevoerd wanneer ze wordt gemaakt, maar wanneer ze wordt gebruikt, bijvoorbeeld wanneer er over geïtereerd wordt met foreach.

Uitgestelde uitvoering betekent dat de ingevoerde sequentie kan worden gewijzigd nadat de query is geconstrueerd, maar dit moet je wel doen voordat de query wordt uitgevoerd. In de volgende code wordt de ingevoerde sequentie gewijzigd nadat de query is geconstrueerd.

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
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

 De query wordt niet uitgevoerd totdat de `foreach` wordt uitgevoerd. De resultaten van het uitvoeren van deze code zijn:

![](../.gitbook/assets/image%20%2823%29.png)

Merk op dat de waarde 100 is opgenomen in de resultaten, niettegenstaande het derde element op het moment dat de query werd gemaakt, nog steeds de waarde van 11 had.

De queries die een een scalaire waarde retourneren \(of een enkel element van de ingevoerde sequentie\) zoals `Count`, `Min` en `Last` werken niet op deze uitgestelde executie manier. Dus bij het gebruik van operatoren zoals `Count` zal de queryonmiddellijk worden uitgevoerd, en niet uitgesteld.

Een aantal van de conversie operatoren worden ook onmiddellijk uitgevoerd, zoals `ToList`, `ToArray`, `ToLookup` en `ToDictionary`.

In de volgende code wordt de leeftijden matrix worden gewijzigd net zoals in het voorgaande voorbeeld. Maar in dit voorbeeld werd de query op het moment dat de wijziging plaatsvindt reeds uitgevoerd vanwege de extra `ToArray()` operator. De wijziging zal niet worden opgenomen in de `foreach`.

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
// scalar return value
// construct the query
IEnumerable<int> ouderDan11 = leeftijden.Where(x => x > 10).ToArray();

// Change the third element
leeftijden[2] = 100;
// At this point the query has been created and 
// executed because of the ToArray() operator
Console.WriteLine("Ouder dan 11: ");
foreach (int leeftijd in ouderDan11)
{
    Console.WriteLine(leeftijd);
}
```

De waarde 100 is hier niet meer aanwezig, omdat de inputreeks werd gewijzigd nadat de query is uitgevoerd door de `ToArray()` operator.

![](../.gitbook/assets/image%20%2825%29.png)

### Lambda expressies in query operatoren

Sommige query's laten accepteren anonieme functies als argument waarmee je aangepaste logica aan de query kan doorgeven. Deze aangepaste logica kan je aan de query doorgeven door middel van een lambda-expressie.

De `leeftijden.Where (x => x > 11)` code in het voorgaande codevoorbeeld is een voorbeeld van een query operator waaraan een anonieme functie wordt doorgegeven. De lambda-expressie `x => x > 11` zorgt ervoor dat alleen de elementen \(integers in dit geval\) die groter zijn dan 11 gertourneerd worden.

Wanneer een queryoperator een lambda-expressie als argument meekrijgt, wordt de logica van de lambda-expressie op elk element van de invoer-sequentie toegepast.

Het type lambda expressie, die wordt ingevoerd in een queryoperator, is afhankelijk van de taak die de queryoperator uitvoert. In de volgende figuur zien we de handtekening van de Where-query operator. Het inputelement int wordt voorzien en een `bool` moet worden teruggestuurd die bepaalt of het element wordt opgenomen in de uitvoer-sequentie.

![Visual Studio toont de signatuur van de Where operator](https://modernways.be/myap/it/image/programming/microsoft.net/linq/Visual%20Studio%20toont%20de%20signatuur%20van%20de%20Where%20operator.png)

### Geïnterpreteerde \(interpreted\) queries

Tot nu toe hebben we ons gericht op lokale queries. Lokale queries manipuleren `IEnumerable<T>` sequenties. Lokale queries zijn het resultaat van de uitgevoerde query's, die zijn gedefinieerd in de `System.Linq.Enumerable` klasse. Op die manier resulteert het uitvoeren van een lokale query in code die wordt gedefinieerd tijdens het compileren.

Geïnterpreteerde queries, aan de andere kant, beschrijven slechts de "vorm" van de query die wordt geïnterpreteerd tijdens runtime - vandaar de naam "geïnterpreteerd." Geïnterpreteerde queries manipuleren `IQueryable<T>` sequenties, en de LINQ operatoren worden omgezet naar methoden die gedefinieerd zijn in de `System.Linq.Queryable` klasse in plaats van de `System.Linq.Enumerable` klasse.

De lokale \(IEnumerable&lt;T&gt;\) queries bevatten de werkelijke implementatie van de query code die wordt uitgevoerd, terwijl de geïnterpreteerde queries \(IQueryable &lt;T&gt;\) niet. Bij geïnterpreteerde queries, is de werkelijke query code, die wordt uitgevoerd, gedefinieerd in een query provider. De query provider ontvangt de beschrijving van de query die moet worden uitgevoerd en voert de query vervolgens uit \(bijvoorbeeld het uitvoeren van SQL op een database\) en returned tenslotte het resultaat.

### Fluent style en Query style

Er zijn twee stijlen voor het schrijven van LINQ queries: de vloeiende stijl \(of vloeiende syntax\) en de query-expressie stijl \(of query syntaxis\). De vloeiende stijl gebruikt query-operator extensie methoden om queries te maken, terwijl de query-expressie stijl gebruikt maakt van een andere syntaxis die door de compiler vertaald wordt in een vloeiende syntaxis.

De codevoorbeelden tot nu toe maken allemaal gebruik van de vloeiende syntax. Hier zie je ook nog eens het voorbeeld van de query expressie stijl:

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
// construct the query in query style
IEnumerable<int> ouderDan11 = from leeftijd 
                              in leeftijden 
                              where leeftijd > 0 
                              select leeftijd;

Console.WriteLine("Ouder dan 11: ");
foreach (int leeftijd in ouderDan11)
{
    Console.WriteLine(leeftijd);
}
```

We gaan vooral in deze cursus gebruikmaken van de fluent style.

### Aaneengeschakelde queryoperatoren

We kunnen ook query operatoren aan elkaar aaneenschakelen zodat we complexere queries kunnen samen stellen. 

```csharp
int[] leeftijden = { 50, 1, 11, 2, 28, 2, 13, 25, 50 };
// construct the query
IEnumerable<int> ouderDan11 = leeftijden
            .Where(x => x > 10)
            .OrderBy(x => x)
            .Select(x => x * 2);

Console.WriteLine("Ouder dan 11: ");
foreach (int leeftijd in ouderDan11)
{
    Console.WriteLine(leeftijd);
}
```

We maken hier dus een ketting van de `Where` operator die alleen de elementen selecteert die groter zijn dan 10, vervolgens sorteren we de reeks en dan gaan we het dubbele van elk element nemen. 

![](../.gitbook/assets/image%20%2821%29.png)



