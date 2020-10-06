# Oefeningen

### Hoofdopdracht

Maak een nieuw project "Pokedex" aan de hand van een empty ASP.NET Core Web Application \(zoals we de voorgaande lessen hebben gedaan\).

Hieronder staat een zip file met 151 images van pokemon. Download deze en zorg dat deze beschikbaar is via je **wwwroot** directory.

{% file src=".gitbook/assets/pokemon.zip" caption="Pokemon images" %}

* Maak een `HomeController` die een navigatiebalk bevat en een welkomst pagina. In de navigatie balk dient er een tab te staan met **Home** en een met **Pokedex**
* Maak vervolgens een PokedexController met bijbehorende index actie. In de view van deze actie zorg je ervoor dat de images van alle 151 pokemon getoond worden aan de gebruiker. Er dienen geen namen getoond te worden.
* Vervolgens zorg je ervoor dat het aantal pokemon dat getoond worden configureerbaar zijn aan de hand van de `appsettings.json` file met een configuratie variabele `MaxPokemon`
* Gebruik logging om telkens als de gebruiker naar de Pokedex pagina gaat een bericht te loggen op je console met "A user has used the pokedex".
* Zorg ervoor dat er rechtsbovenaan de navigatiebalk een image komt te staan waarin willekeurig een andere pokemon wordt getoond elke keer de pagina wordt gerefreshed.

![](.gitbook/assets/image%20%2848%29.png)

![](.gitbook/assets/image%20%2850%29.png)

### Uitbreiding

* Zorg ook dat de kleur van de navigatiebalk volledig configureerbaar is \(in appsettings.json\). Kijk naar de documentatie van bootstrap om te zien hoe je die kleur verandert.
* Gebruik de functie `isDarkMode` om te bepalen of de navigatiebalk in dark mode moet komen of ik light mode

```csharp
bool isDarkMode(string color)
{
    System.Drawing.Color col = System.Drawing.ColorTranslator.FromHtml(color);
    if (col.R * 0.2126 + col.G * 0.7152 + col.B * 0.0722 > 255 / 2)
    {
        return false;
    }
    else
    {
        return true;
    }
}
```

Bij NavBarColor = "\#400000" zal het er zo uit zien:

![](.gitbook/assets/image%20%2849%29.png)

Bij NavBarColor = "\#FFBDBD" zal het er zo uit zien:

![](.gitbook/assets/image%20%2847%29.png)

