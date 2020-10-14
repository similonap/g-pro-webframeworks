# Oefeningen

**We gaan weer verder op de pokedex oefening van vorige les**

Maak een interface `IPokedex` met de volgende methoden en plaats deze in je Models directory

![](../.gitbook/assets/image%20%2858%29.png)

Maak nu ook een `JsonPokedex` klasse aan in je Models directory. Deze moet de interface `IPokedex` implementeren. Zorg ervoor dat

* `Get` de pokemon teruggeeft die overeentkomt met de id die meegegeven is aan de methode
* `GetAll` een lijst van alle pokemon teruggeeft

Gebruik hiervoor weer de `getPokemon` functie die je in vorige oefeningen sessie hebt gebruikt om de pokemon in te lezen vanuit het JSON bestand. Verplaats die uiteraard in de `JsonPokedex` klasse.

