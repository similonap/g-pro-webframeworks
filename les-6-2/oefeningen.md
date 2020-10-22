# Oefeningen

**We gaan weer verder op de pokedex oefening van vorige les**

**OPGELET:** Er stond een fout in de pokedex.json waar speed met een hoofdletter stond en niet met een kleine letter. Pas de pokedex.json file aan zodat speed niet meer met hoofdletter is geschreven! Of download de juiste file hier.

{% file src="../.gitbook/assets/pokedex \(1\).json" %}

Maak een nieuwe methode in de interface IPokedex 

```csharp
void Save(Pokemon pokemon);
```

en zorg voor een implementatie in de JsonPokedex. Je hoeft uiteraard enkel het element in de lijst aan te passen en niet in de originele json file.

Zorg er nu voor dat de Base stats van de pokemon nu kunnen aangepast worden aan de hand van input elementen in een form. Deze form moet een **POST** doen naar de `Details` action in de `Pokedex` ****controller.

![](../.gitbook/assets/image%20%2866%29.png)

Als de pokemon is geupdated in de pokedex moet je een melding geven dat de pokemon is geupdated.

![](../.gitbook/assets/image%20%2865%29.png)

Zorg ervoor dat er validatie is op de waarden die worden meegegeven aan de base stats \(Hp, Speed, Attack, Defense, SpAttack en SpDefense\). Deze waarden moeten tussen 1 en 255 liggen.

![](../.gitbook/assets/image%20%2867%29.png)

