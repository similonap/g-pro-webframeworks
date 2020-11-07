# LINQ

## Introductie

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

![](../.gitbook/assets/image%20%2832%29.png)

**Opgelet:** Let op dat je `using System.Collections.Generic`gebruikt en niet `using using System.Collections` .

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

## Element operatoren

Elementoperatoren zijn operatoren die exact 1 waarde van de outputreeks terug geeft. 

### First

Geeft  het eerste element terug van de inputreeks. 

```csharp
List<double> getallen = new List<int> { 1, 2, 3, 4, 5 };
int whatsThis = doubles.First();
```

Je kan hier ook een lambda expressie aan meegeven om een conditie mee te geven.

```csharp
List<double> getallen = new List<int> { 1, 2, 3, 4, 5 };
int whatsThis = getallen.First(val => val > 2);
```

### Last

Uiteraard heb je ook een Last operator. Deze is gelijkaardig aan de First operator maar neemt het laatste element van een input reeks.

```csharp
List<double> getallen = new List<int> { 1, 2, 3, 4, 5 };
int whatsThis = doubles.Last();
```

### FirstOrDefault en LastOrDefault

Als het niet mogelijk is om een element terug te geven dan gooit`First` en `Last` een exception. Om dit te voorkomen kan je ook de OrDefault varianten gebruiken voor een element te selecteren. Hij zal dan de default waarde van je type teruggeven als er niets gevonden is. 

```csharp
List<double> getallen = new List<int> { 1, 2, 3, 4, 5 };
int whatsThis = getallen.FirstOrDefault(val => val > 5);
```

## Restrictie operatoren

Beperkingsopertoren nemen een inputreeks en geven een outputreeks terug met een beperkt aantal elementen \("gefilterd"\).

### Where

De elementen, die deel uitmaken van de outputreeks, zijn die elementen die overeenkomen met de opgegeven beperking. Individuele uitvoerelementen worden niet gewijzigd of getransformeerd.

```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Judith", 20)
};
IEnumerable<Person> vijftigPlussers = personen.Where(x => x.Age >= 50);
Console.WriteLine("Vijftigplussers:");
foreach (Person persoon in vijftigPlussers)
{
    Console.WriteLine(persoon.Name);
}
```

 De code toont het gebruik van de `Where` operator. Het predicaat `x => x.Leeftijd >= 50` geeft `true` terug voor elke persoon die 50 jaar is of meer. De code produceert de volgende output:

![](../.gitbook/assets/image%20%2834%29.png)

Een predicaat is een functie die een boolean true retourneert als aan een bepaalde voorwaarde is voldaan.

Je kan ook de positie gebruiken in je Where predicaat. Dit doe je door de overloaded functie van de Where functie te gebruiken die ook de index bevat.

```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Judith", 20)
};
IEnumerable<Person> vijftigPlussers = personen.Where((x, index) => x.Age >= 50 || index == 4);
Console.WriteLine("Vijftigplussers:");
foreach (Person persoon in vijftigPlussers)
{
    Console.WriteLine(persoon.Name);
}
```

![](../.gitbook/assets/image%20%2833%29.png)

## Ordering Operators

De ordeningsquery retourneert evenveel elementen als input-sequentie, maar dan in een \(mogelijk\) andere volgorde.

### OrderBy

De `OrderBy` queryoperator sorteert de ingevoerde reeks door de elementen in de invoerreeks met elkaar te vergelijken volgens een specifieke sleutel.

De volgende code toont het sorteren van een eenvoudige invoerreeks van strings. In dit voorbeeld is de sleutel een property die door de lambda expressie `x => x.Name of x => x.Leeftijd` gespecificeerd wordt.

De volgende code rangschikt de personen eerst op naam en daarna op leeftijd:

```csharp
Person[] personen = {
                new Person("Jan", 60),
                new Person("An", 100),
                new Person("Peter", 15),
                new Person("Hans", 11),
                new Person("Marijke", 40),
                new Person("Judith", 20)
            };
            
IEnumerable<Person> alfabetischOpNaam =
    personen.OrderBy(x => x.Name);

Console.WriteLine("Alfabetische lijst van personen");
foreach (Person persoon in alfabetischOpNaam)
{
    Console.WriteLine(persoon.Name);
}

IEnumerable<Person> alfabetischOpLeeftijd =
    personen.OrderBy(x => x.Age);
Console.WriteLine("Personen gerangschikt op leeftijd");
foreach (Person persoon in alfabetischOpLeeftijd)
{
    Console.WriteLine(persoon.Name);
}
```

![](../.gitbook/assets/image%20%2824%29.png)

### ThenBy

De `ThenBy` queryoperator kan één of meerdere keren na een eerste OrderyBy worden toegevoegd. De `ThenBy` operator voegt extra niveaus toe aan het sorteren. In de volgende code worden de Personen eerst per leeftijd en dan alfabetisch geordend.

```csharp
Person[] personen = {
                new Person("Jan", 60),
                new Person("An", 100),
                new Person("Peter", 15),
                new Person("Hans", 11),
                new Person("Marijke", 40),
                new Person("Andie", 40),
                new Person("Judith", 20)
};
IEnumerable<Person> alfabetischOpLeeftijdDanOpNaam =
    personen.OrderBy(x => x.Age).ThenBy(x => x.Name);

Console.WriteLine("Lijst op leeftijd en dan alfabetische op naam:");
foreach (Person person in alfabetischOpLeeftijdDanOpNaam)
{
    Console.WriteLine($"{person.Age}\t{person.Name}");
}
```

![](../.gitbook/assets/image%20%2822%29.png)

### OrderByDescending

De `OrderByDescending` queryoperator werkt op dezelfde manier als de `OrderBy` operator, met dien verstande dat de resultaten worden geretourneerd in de omgekeerde volgorde,

### ThenByDescending

De `ThenByDescending` queryperator werkt op dezelfde manier als de `ThenBy` operator, maar retourneert de resultaten in de omgekeerde sorteervolgorde.

## Partitioneringsoperatoren

Een partitioneringsquery's neemt een inputreeks en retourneren een "stuk" als outputreeks. Deze operatoren kan je gebruiken om grote reeksen in "pagina's" op te breken. Dat heet paging,

### Take

De `Take` query operator neemt een input reeks en geeft het opgegeven aantal elementen terug als uitvoerreeks.

De volgende code laat zien hoe je de `Take` query operator kan gebruiken om de eerste drie personen in de Personen reeks te retourneren.

```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Andie", 40),
    new Person("Judith", 20)
};
IEnumerable<Person> eersteDriePersonen = personen.Take(3);
Console.WriteLine("Namen van de eerste drie personen:");
foreach (Person persoon in eersteDriePersonen)
{
    Console.WriteLine(persoon.Name);
}
```

![](../.gitbook/assets/image%20%2827%29.png)

### TakeWhile

De `TakeWhile` queryoperator retourneert een niet vooraf bepaald aantal elementen, maar blijft daarentegen elementen retourneren zolang een gespecificeerd conditie waar is.

In de volgende code worden Personen gertourneerd zolang de leeftijd niet 11 is.  


```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Andie", 40),
    new Person("Judith", 20)
};
IEnumerable<Person> eersteDriePersonen = personen.TakeWhile(x => x.Age != 11);
Console.WriteLine("Namen tot er een persoon van 11 jaar wordt tegengekomen:");
foreach (Person persoon in eersteDriePersonen)
{
    Console.WriteLine(persoon.Name);
}
```

![](../.gitbook/assets/image%20%2829%29.png)

## Projectie operatoren

rojectie query's nemen een inputreeks, transformeren elk element in die reeks, en produceren een outputreeksvan deze getransformeerde elementen. Op deze wijze wordt de invoerreeks geprojecteerd op een ander uitvoerreeks.

### Select

De `Select` queryoperator transformeert elk element in de invoerreeks naar een element in de uitvoerreeks. In de uitvoerreeks zitten evenveel elementen als in de invoerreeks.

De volgende query projecteert de inputreeks van `Personen` elementen op een outputreeks van `string` elementen. De lambda-expressie is een beschrijving van de projectie: het nemen van elk element in `personen` invoerreeks en het terugsturen van een `string` element.

```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Judith", 20)
};
IEnumerable<string> persoonsNamen = personen.Select(x => x.Name);
Console.WriteLine("Namen van de personen:");
foreach (string name in persoonsNamen)
{
    Console.WriteLine(name);
}
```

![](../.gitbook/assets/image%20%2830%29.png)

## Aaneengeschakelde query operatoren

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

## Aggregatie operatoren

Als je met reeksen getallen werkt dan kan je een aantal ingebouwde aggregatie operatoren gebruiken zoals `Max`, `Min`, `Average` en `Sum`. Het gebruik hiervan is triviaal. 

```csharp
int[] getallen = { 1, 2, 3, 5, 3, 2, 10 };

Console.WriteLine(getallen.Max());
```

Wil je het maximum van een van de properties van een reeks objecten dan doe je dit aan de hand van een lambda:

```csharp
Person[] personen =
{
    new Person { Age = 60, Name = "Jan"},
    new Person { Age = 100, Name = "An"},
    new Person { Age = 15, Name = "Peter"},
    new Person { Age = 11, Name = "Hans"},
    new Person { Age = 15, Name = "Marijke"},
    new Person { Age = 20, Name = "Youssef"}
};


Console.WriteLine(personen.Max(p => p.Age));
```

De operatoren `Min`, `Average` en `Sum` werken op identiek dezelfde wijze.

Je kan ook een eigen aggregatie operator definiëren als deze bovenstaande niet exact doen wat jij wil. Zo kan je bijvoorbeeld de voorgedefinieerde aggregatie operatoren op de volgende manier zelf maken:

```csharp
//SUM
Console.WriteLine(getallen.Aggregate((prev, curr) => prev + curr)) ;

//MAX
Console.WriteLine(getallen.Aggregate((prev, curr) => curr > prev ? curr : prev));

//MIN
Console.WriteLine(getallen.Aggregate((prev, curr) => curr < prev ? curr : prev));

//AVERAGE
Console.WriteLine((double) getallen.Aggregate((prev, curr) => prev + curr) / getallen.Count());
```

Willen we nu bijvoorbeeld onze eigen aggregatie functie maken om de namen van onze personen objecten aan elkaar te concateneren, dan moeten we eerst Select gebruiken om de namen te selecteren en dan kunnen we de `Aggregate` operator gebruiken

```csharp
Console.WriteLine(personen.Select(p => p.Name).Aggregate((prev, curr) => prev + "," + curr));
```



