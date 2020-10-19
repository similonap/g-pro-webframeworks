# Oefeningen

**We gaan weer verder op de pokedex oefening van vorige les**

Maak een interface `IPokedex` met de volgende methoden en plaats deze in je Models directory

![](../.gitbook/assets/image%20%2858%29.png)

Maak nu ook een `JsonPokedex` klasse aan in je Models directory. Deze moet de interface `IPokedex` implementeren. Zorg ervoor dat

* `Get` de pokemon teruggeeft die overeentkomt met de id die meegegeven is aan de methode
* `GetAll` een lijst van alle pokemon teruggeeft

Gebruik hiervoor weer de `getPokemon` functie die je in vorige oefeningen sessie hebt gebruikt om de pokemon in te lezen vanuit het JSON bestand. Verplaats die uiteraard in de `JsonPokedex` klasse. Zorg dat je een private field hebt met een lijst van pokemon en vul deze in de constructor.

Zorg er nu voor dat de `PokedexController` een private field `pokedex` heeft met als type `IPokedex` en zorg ervoor dat die wordt meegegeven als argument in de PokedexController constructor. Pas nu de `Index` en de `Details` action aan dat die de methoden van de interface gebruiken.

Pas de Startup klasse aan zodat je de `JSONPokedex` gebruikt als er een `IPokedex` service gebruikt wordt. We doen dit momenteel  met `AddSingleton` omdat we maar 1 keer de pokemon in het geheugen willen laden en dus niet bij elke request.

**Uitbreiding:**

* Zorg ervoor dat de interface `IPokedex` ook een `GeneratedAt` property heeft \(get en set\) met type DateTime
* Zorg ervoor dat de property `GeneratedAt` wordt gezet op het tijdstip wanneer de constructor aangeroepen wordt.
* Pas de `Index` action van de `PokedexController` aan zodat de `GeneratedAt` property van de pokedex gebruikt wordt in de `ViewBag.TimeStampGenerated`
* Als je nu naar de pokedex pagina gaat kijken ga je zien dat de tijdstip niet meer zal aanpassen na een refresh.
* Kijk eens wat er gebeurd als je in Startup ipv `AddSingleton` `AddScoped` gebruikt. Kan jij dit verklaren? Zoek in de leerstof over dependency injection waarom dit zo is.

