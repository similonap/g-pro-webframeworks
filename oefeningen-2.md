# Oefeningen

We gaan verder op de **Pokedex** oefening van **Les 3**

Maak de 3 volgende klassen van dit klasse diagram in de Models directory. Let goed op dat je exact dezelfde property names gebruiken \(en let op hoofdletters!\)

![](.gitbook/assets/image%20%2853%29.png)

**Let op:** het type van een pokemon is een array van strings omdat pokemon meerdere types kunnen hebben.

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

Maak een action `Details` in de `PokedexController` die een `id` meekrijgt. In deze action moet je code schrijven om over de array te itereren \(foreach\) en de juiste pokemon te zoeken met dezelfde Id. Vervolgens geef je deze mee aan de view.

Vervolgens zorg je ervoor dat als je op de `Index` pagina van de Pokedex op een image klikt dat je dan in de correcte Details pagina van de pokemon terecht komt.

De details pagina moet er als volgt uitzien:

![](.gitbook/assets/image%20%2855%29.png)

Zorg er nu voor dat in de `Index` action ook de huidige timestamp wordt meegegeven aan de View \(via ViewBag\) en dat er onderaan de pagina van de pokemon het tijdstip komt te staan wanneer de pagina gemaakt werd. **Opgelet: De DateTime en de omzetting naar een string moet in de controller gebeuren, niet in de view!**

![](.gitbook/assets/image%20%2856%29.png)



