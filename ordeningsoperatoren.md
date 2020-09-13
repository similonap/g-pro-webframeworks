# Ordering operators

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

![](.gitbook/assets/image%20%2824%29.png)

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

![](.gitbook/assets/image%20%2822%29.png)

### OrderByDescending

De `OrderByDescending` queryoperator werkt op dezelfde manier als de `OrderBy` operator, met dien verstande dat de resultaten worden geretourneerd in de omgekeerde volgorde,

### ThenByDescending

De `ThenByDescending` queryperator werkt op dezelfde manier als de `ThenBy` operator, maar retourneert de resultaten in de omgekeerde sorteervolgorde.

