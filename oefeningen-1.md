# Oefeningen

Maak een nieuw project "Pokedex" aan de hand van een Empty Web project \(zoals we de voorgaande lessen hebben gedaan\).

Hieronder staat een zip file met 151 images van pokemon. Download deze en zorg dat deze beschikbaar is via je **wwwroot** directory.

{% file src=".gitbook/assets/pokemon.zip" caption="Pokemon images" %}

Maak een `HomeController` die een navigatiebalk bevat en een welkomst pagina. In de navigatie balk dient er een tab te staan met **Home** en een met **Pokedex**

Maak vervolgens een PokedexController met bijbehorende index actie. In de view van deze actie zorg je ervoor dat de images van alle 151 pokemon getoond worden aan de gebruiker. Er dienen geen namen getoond te worden.

Vervolgens zorg je ervoor dat het aantal pokemon dat getoond worden configureerbaar zijn aan de hand van de `appsettings.json` file met een configuratie variabele `MaxPokemon`

Gebruik logging om telkens als de gebruiker naar de Pokedex pagina gaat een bericht te loggen op je console met "A user has used the pokedex".

### 

