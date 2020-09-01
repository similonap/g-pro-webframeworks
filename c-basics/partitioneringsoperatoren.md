# Partitioneringsoperatoren

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

![](../.gitbook/assets/image%20%2828%29.png)



