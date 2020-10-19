# Operators voor 1 waarde

Deze LINQ methoden kunnen gebruikt worden om een enkele waarde van een IEnumerable&lt;T&gt; reeks te halen. 

### First 

Deze operator geeft het eerste element terug in de reeks. Het datatype van de waarde die wordt teruggegeven hangt van van het type `T` in `IEnumerable<T>`waarop de operator uitgevoerd wordt. Als het een reeks is van integers dan zal First\(\) ook een integer terug geven.

```text
List<int> getallen = new List<double> { 1,2,3,4,5 };
int whatsThis = getallen.First();
```

### Last

De last operator is heel gelijkaardig aan de First operator maar geeft het laatste element terug.

```text
List<int> getallen = new List<double> { 1,2,3,4,5 };
int whatsThis = getallen.Last();
```



