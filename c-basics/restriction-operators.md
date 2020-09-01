# Restriction operators

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



