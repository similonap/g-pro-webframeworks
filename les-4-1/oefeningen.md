# Oefeningen

Maak een nieuw **Console project** aan genaamd `StudentAdmin`. We maken dus deze keer **geen** ASP.NET Core Web application aan!

Zorg voor een klasse `Student` met de volgende properties en een ToString methode

![](../.gitbook/assets/image%20%2868%29.png)

In je Program klasse initialiseer je een list met de volgende studenten

```csharp
List<Student> students = new List<Student>
{
    new Student { FirstName = "Quentin", LastName = "Vanhoutte", Grade = 77},
    new Student { FirstName = "Brian", LastName = "Leroy", Grade = 55},
    new Student { FirstName = "Chiara", LastName = "Vancluysen", Grade = 80},
    new Student { FirstName = "Bavo", LastName = "Van Daele", Grade = 30},
    new Student { FirstName = "Timothy", LastName = "Borremans", Grade = 60},
    new Student { FirstName = "Lisa", LastName = "Peeters", Grade = 75},
    new Student { FirstName = "Lenny", LastName = "Louage", Grade = 45},
    new Student { FirstName = "Ayoub", LastName = "Edris", Grade = 90},
    new Student { FirstName = "Jeroen", LastName = "Callens", Grade = 60},
    new Student { FirstName = "Anke", LastName = "De Smedt", Grade = 70},
    new Student { FirstName = "Hans", LastName = "Leroy", Grade = 55},
    new Student { FirstName = "Arnaud", LastName = "Blindeman", Grade = 88},
    new Student { FirstName = "Brian", LastName = "Dujardin", Grade = 45},
    new Student { FirstName = "Perrine", LastName = "Desmet", Grade = 50},
    new Student { FirstName = "Abdullah", LastName = "Taslim", Grade = 70},
    new Student { FirstName = "Zaira", LastName = "Akman", Grade = 55}
};
```

Gebruik de LINQ queries om de volgende dingen te laten zien op je console:

* Een overzicht van alle geslaagde studenten gesorteerd op hun `Grade` \(van klein naar groot\)
* Een overzicht van alle gebuisde studenten gesorteerd op hun `Grade` \(van klein naar groot\)
* De top 3 van studenten \(waar de beste bovenaan staat\). 
* De beste student
* Een overzicht van alle punten op 10 \(in een array van `double` waarden\) TIP: gebruik select

**Dit is de verwachte output:**

![](../.gitbook/assets/image%20%2869%29.png)

