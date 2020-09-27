# Troubleshooting

### The following error occured when trying to configure IIS Express for project...

![](../.gitbook/assets/image%20%2839%29.png)

**Oplossing:** 

* Sluit eerst visual studio af.
* Ga naar %userprofile%\Documents\IISExpress\config in je verkenner.
* Daar zullen 3 bestanden instaan waaronder een bestand `redirection.config`
* Verwijder die 3 bestanden en start vervolgens visual studio terug op.

### De View methode wordt niet gevonden

![](../.gitbook/assets/image%20%2837%29.png)

**Mogelijke oplossingen:**

* Kijk na dat je niet van `ControllerBase` overerft maar van `Controller`

### 'Controller' is a namespace but is used like a type

![](../.gitbook/assets/image%20%2838%29.png)

**Oplossing:**

* Je hebt je namespace en directory niet `Controllers` genoemd maar `Controller`
* Hier moet dus `namespace LlamaStore.Controllers` staan en de directory moet je renamen naar Controllers.

