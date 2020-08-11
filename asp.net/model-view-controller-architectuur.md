# Model-View-Controller Architectuur

### MVC architectuur: Separation of concerns

De Model-View-Controller patroon is een architectonisch patroon dat een **strikte scheiding** afdwingt tussen de afzonderlijke delen van een applicatie. Deze scheiding noemt men SEC of Seperation of Concerns, of, in meer algemene zin, "losse koppeling." Losse koppeling tussen de verschillende onderdelen van een applicatie heeft een aantal voordelen op lange termijn:

1. Ontwikkeling \(development\) individuele componenten zijn niet rechtstreeks afhankelijk van andere componenten, waardoor ze gemakkelijker apart ontwikkeld kunnen worden. Componenten kunnen ook gemakkelijk worden vervangen omdat de 'losse koppeling' of 'scheiding' ervoor zorgt dat het weghalen en vervangen van één component geen of weinig invloed op de andere componenten.
2. Testbaarheid \(testability\) Losse koppeling van componenten laat toe dat test-implementaties de plaats kunnen innemen van "productie" componenten. De database kan tijdens het testproces vervangen worden door static data in een DAL klasse. De DAL klasse kan m.a.w. op zichzelf getest worden zonder rekening te moeten houden met de complexiteit van databasetoegang. Dat componenten eenvoudig vervangen kunnen worden door 'mock' voorstellingen vergemakkelijkt het testproces en verhooft drastisch de betrouwbaarheid van het systeem.
3. Onderhoud \(maintenance\) Logisch op zichzelf staande componenten zorgen ervoor dat veranderingen meestal beperkt blijven tot een klein aantal componenten, meestal slechts één. Daardoor vermindert het risico dat er na de verandering iets misloopt.

En dat zijn nu eenmaal zaken die hoog op de agenda van de bedrijven staan.

![Visuele voorstelling MVC](https://www.modernways.be/myap/it/image/programming/microsoft.net/asp.net/MVC%20diagram%202.png)

De MVC patroon splitst een applicatie op in drie lagen: het model, de view, en de controller \(zie Figuur MVC diagram\). Elk van deze lagen heeft een zeer specifieke taak waarvoor hij verantwoordelijk is en wat het meest belangrijke is, de ene laag houdt zich niet bezig met hoe de andere lagen hun werk doen.

#### Model

Een model is een softwarevoorstelling van dingen, processen en regels zoals ze in de werkelijkheid bestaan. Dingen, processen en regels vormen het onderwerp of domein van een applicatie. Het is de centrale bewaarder van gegevens en domeinlogica. Al de rest \(controllers en views\) is vormen de loodgieterij die nodig is om het domein via het web toegankelijk te maken.

Het model is de voorstelling van de core business logica en data. Modellen kapselen de eigenschappen en het gedrag van een domein entiteit in en geven toegang tot eigenschappen die de entiteit te beschrijven. Bijvoorbeeld, de klasse **Boek** vertegenwoordigt het begrip "boek" in de applicatie en kan eigenschappen zoals Titel en Auteur toegankelijk maken, evenals gedrag in de vorm van methoden zoals **Koop\(\)**.

Modellen stellen de dingen voor die de gebruikers browsen of editeren. Soms wordt er gewerkt met eenvoudige view modellen die de gegevens bevatten die overeen gaan tussen controllers en views. Soms worden er meer gesofisticeerde domein modellen gecreëerd die informatie, handelingen en regels omvatten die betekenis vol zijn voor het onderwerp \(business domein\) van de applicatie. Domein modellen beschrijven de toestand van de applicatiewereld op dit moment, maar ze staan volledig los van de UI.

#### View

De view is verantwoordelijk voor het omzetten van een model of modellen in een visuele representatie. In webapplicaties, betekent dit meestal het genereren van HTML die in de browser van de gebruiker kan worden weergegeven.Maar hetzelfde model kan ook gevisualiseerd worden in PDF, XML, of een spreadsheet. De view houdt zich alleen bezig met de weergave van gegevens en bevat dus geen business logica. De business logica blijft in het model, dat de view voorziet van alles wat het nodig heeft.

#### Controller

De controller, zoals de naam al zegt, controleert de applicatie-logica en treedt op als coördinator tussen de view en het model. Controllers ontvangen input van gebruikers via de view, en werken dan met het model om specifieke acties uit te voeren. Tenslotte sturen ze de resultaten terug naar de view.

