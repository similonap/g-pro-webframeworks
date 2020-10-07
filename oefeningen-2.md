# Oefeningen

Maak de 3 volgende klassen van dit klasse diagram in de Models directory. Let goed op dat je exact dezelfde property names gebruiken \(en let op hoofdletters!\)

![](.gitbook/assets/image%20%2853%29.png)

Download het onderstaande `pokedex.json`  bestand en plaats dit in je project. Je project zal er ongeveer zo uitzien:

![](.gitbook/assets/image%20%2852%29.png)



{% file src=".gitbook/assets/pokedex.json" caption="Pokedex.json" %}

Plaats de volgende functie `loadPokemonData()` in je PokedexController

```csharp
private List<Pokemon> getPokemon()
{
    var options = new JsonSerializerOptions
    {
        PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
        WriteIndented = true
    };
    var lines = System.IO.File.ReadAllText(@"pokedex.json");
    return JsonSerializer.Deserialize<List<Pokemon>>(lines, options);
}
```



