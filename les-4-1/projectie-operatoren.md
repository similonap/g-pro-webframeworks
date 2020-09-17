# Projection operators

Projectie query's nemen een inputreeks, transformeren elk element in die reeks, en produceren een outputreeksvan deze getransformeerde elementen. Op deze wijze wordt de invoerreeks geprojecteerd op een ander uitvoerreeks.

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

### SelectMany

Voegt collecties in een enkele collectie samen. Is vergelijkbaar met cross join van SQL.

Deze lambda-expressie kruist twee arrays, en produceert een Cartesiaans product.

```csharp
Person[] personen = {
    new Person("Jan", 60),
    new Person("An", 100),
    new Person("Peter", 15),
    new Person("Hans", 11),
    new Person("Marijke", 40),
    new Person("Judith", 20)
};
string[] fruit = { "appel", "peer", "banaan" };

IEnumerable<string> persoonEet = personen.SelectMany(persoon => fruit, (p, f) =>
{
    return $"{p.Name} eet {f}";
});

foreach (string watDiePersoonEet in persoonEet)
{
    Console.WriteLine(watDiePersoonEet);
}
```

Deze creëert een outputreeks die een string bevat voor elk stuk fruit in de fruit array dat de persoon eet.

![](../.gitbook/assets/image%20%2828%29.png)


