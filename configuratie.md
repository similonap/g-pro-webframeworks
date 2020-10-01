# Configuratie

Het configureerbaar maken van je applicatie is een belangrijk aspect voor de flexibiliteit van je web applicatie te garanderen. Wil je een andere database connection string gebruiken? Wil je bepaalde constanten ergens definieren? Dit doe je allemaal in de Configuration van je applicatie. 

### Waar haalt ASP.NET Core je configuratie

Configuration providers lezen configuratie data van key value pairs van een aantal verschillende configuratie bronnen:

* Settings files zoals appsettings.json
* Environment variabelen
* Azure
* Command line argumenten
* ...

Hij zal dit ook in een bepaald volgorde doen. Bijvoorbeeld als een key in appsettings.json en op environment variabelen gezet is dan zal de configuratie setting van de environment variables genomen worden.



