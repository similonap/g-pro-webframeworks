# Communicatie tussen Views en Controler

Tot nu toe waren de Views en de Controllers volledig van elkaar gescheiden. Het enige wat de controller deed was aangeven dat een View moet getoond worden maar er werd geen gegevens tussen beiden doorgegeven. Views zijn normaal gezien niet statisch en zullen afhankelijk wat er aan de controller meegegeven wordt (aan de hand van query parameters in de url bv) een ander resultaat geven. In dit deel gaan we zien hoe we dit kunnen doen.

### ViewData

`ViewData` is een object waar informatie kan doorgegeven worden tussen de controller en de view. Het object is een Dictionary van string sleutels met object waarden. Je kan er alle soorten types van objecten in opslagen. Het `ViewData` object is zonder extra code altijd beschikbaar in de View. 


