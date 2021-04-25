# Woche 04

In dieser Woche kommen wir - endlich - zur Vererbung! Als erstes Beispiel schauen wir uns Exceptions im Detail an.

## Vererbung allgemein

Durch Vererbung können Hierarchien von Klassen erstellt werden, die ähnlich funktionieren wie in der echten Welt. Nehmen wir als Beispiel Rucksäcke. Es gibt viele unterschiedliche Rucksäcke, die für unterschiedliche Zwecke geeignet sind: Geht man mit einem Wanderrucksack zur Schule, wird man seltsam angeschaut, während Wandern mit einem Schulranzen wenig Spaß macht. Wenn man sich also für einen bestimmten Anlass vorbereitet und entsprechend einen konkreten Rucksack braucht, kann man nicht einfach in den Laden gehen und sagen "Ich hätte gerne einen Rucksack" - man muss schon genau formulieren, was für einer es denn sein soll. In anderen Situationen ist diese Unterscheidung aber nicht ganz so wichtig und es zählen einzig die allgemeinen Eingenschaften eines Rucksacks (man kann Dinge reintun und ihn auf dem Rücken tragen). Braucht man beispielsweise beim Umzug Hilfe, kann man die Bekannten bitten, Rucksäcke zum Packen und Tragen der Dinge mitzubringen.

Ähnlich ist die Rationale bei der Vererbung in der Programmierung: An manchen Stellen im Code ist es wichtig, genau zu wissen, was ein Objekt alles kann ("Ist dieser Rucksack bei voller Bepackung bequem genug für 20 Kilometer im Feld?"), an anderen Stellen ist nur wichtig, dass ein paar grundlegende Bedingungen erfüllt sind ("Kann ich Dinge reintun und es auf dem Rücken tragen?"). Ein Beispiel sind sie uns mittlerweile wohlbekannten Listen. Wir haben bisher mit ```LinkedList``` und mit ```ArrayList``` gearbeitet und gesehen, dass diese unterschiedliche Eigenschaften haben. Allerdings verrät auch der Name bereits, dass sie einige Dinge gemeinsam haben: Sie halten Listen von Objekten. Möchte man nun beispielsweise wie folgt alle Elemente einer Liste ausgeben:

```java
public static void printList(LinkedList<String> mystringlist) {}
    for(String x : mystringlist) {
        System.out.println(x);    
    }
}
```

ist es eigentlich nicht wirklich interessant, ob die Daten in ```mystringlist``` in einer ```LinkedList``` oder einer ```ArrayList``` abgelegt sind - dieser Code würde in beiden Fällen funktionieren. In der oben genannten Schreibweise müsste man aber für die Ausgabe von Elementen einer ```LinkedList``` und einer ```ArrayList``` separate Funktionen schreiben, die identisch wären, außer dass die eine als Argument eine ```LinkedList``` und die andere eine ```ArrayList``` nimmt.  