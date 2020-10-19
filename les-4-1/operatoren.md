# Operatoren

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



