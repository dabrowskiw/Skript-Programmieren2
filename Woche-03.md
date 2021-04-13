# Woche 03

In dieser Woche befassen wir uns mit der einfachsten Form von Benutzerintefraces: Dem Kommandozeileninterface. Doch zunächst holen wir noch eine wichtige Datenstruktur nach: Die HashMap.

## HashMap

Sie kennen bereits die LinkedList und die ArrayList als Möglichkeiten, Daten bequemer als mit reinen Arrays zu Organisieren. Beide eignen sich gut dafür, entweder Listen von Objekten, über die immer wieder iteriert werden muss (LinkedList) oder auf die häufig effizient über den Index zugegriffen werden muss (ArrayList) zu verwalten. Was aber tun, wenn die Entscheidung, auf welches Objekt Sie zugreifen wollen, komplexer ist? Nehmen wir als Beispiel eine Software, die bei der Planung von Budgets für Urlaubsreisen helfen soll und dafür für jede Stadt durchschnittliche Kosten für unterschiedliche Tätigkeiten (z.B. Übernachtung, Kneipenbesuch) in einer Tabelle gespeichert hat. Diese Tabelle könnte in solche Objekte eingelesen werden:

```java
public class CityCosts {
  public String cityname;
  public double hotelcost, beercost;

  public CityCosts(String cityname, double hotelcost, double beercost) {
    this.cityname = cityname;
    this.hotelcost = hotelcost;
    this.beercost = beercost;
  }
}
```

Wurde nun ein Stadtname und eine Verweildauer eingegeben, möchte man die geschätzten Kosten ausgeben. Sind die Städte allerdings in z.B. einer LinkedList gespeichert, wird das ziemlich ineffizient:

```java
  public double getStayCosts(String cityname, int days, int parties, LinkedList<CityCosts> allcosts) {
    for(CityCosts c : allcosts) {
      if(c.cityname.equals(cityname)) {
        return days*c.hotelcost + parties*c.beercost;
      }
    }
    return 0;
  }
```

Um das richtige Objekt zu finden, muss durch die gesamte Liste iteriert werden. Insbesondere, wenn es sich um Berechnungen handelt, die immer wieder durchgeführt werden müssen, schlägt sich das in der Laufzeit des Programmes nieder: Die Berechnungsdauer steigt linear mit der Anzahl an Datensätzen (in diesem Fall also: Verdoppelt sich die Anzahl der bekannten Städte, dann verdoppelt sich im Schnitt auch die Anzahl der Durchläufe der Schleife in dem oberen Beispiel, bis die richtige Stadt gefunden wurde). 

Eine Alternative stellen maps (in anderen Sprachen häufig auch dictionaries oder associative arrays genannt) dar. Diese erlauben es, auf Daten ähnlich wie in einer list über einen Index zuzugreifen - dieser Index muss aber kein Integer sein, sondern es kann ein beliebiges Objekt sein. Dabei wird der Wert, der in der map gespeichert wird, als _value_, und der "index" als _key_ bezeichnet. Die map erlaubt es also, wenn man den key kennt, darüber auf einen value zuzugreifen. Sehen wir uns mal an, wie das obere Beispiel unter Verwendung einer HashMap (einer konkreten Implementation der map - was das genau bedeutet, schauen wir uns später bei Vererbung an) aussehen würde:

```java
  public double getStayCosts(String cityname, int days, int parties, HashMap<String, CityCosts> allcosts) {
    if(allcosts.containsKey(cityname)) {
      return days*allcosts.get(cityname).hotelcost + parties*allcosts.get(cityname).beercost;
    }
    return 0;
  }
```

Nicht nur ist der Code kürzer und verständlicher, er ist auch deutlich effizienter: Die Zeit, die der Zugriff auf ein Element in einer HashMap dauert, ist nicht von der Anzahl der Elemente in der HashMap abhängig (abgesehen von Effekten, die darauf zurückzuführen sind, dass kleine Datenmengen z.T. komplett im CPU-cache vorgehalten werden könnten, während größere Datenmengen in RAM oder sogar swap ausgelagert werden müssen). 

Diese heißt so, weil sie im Hintergrund eigentlich ein ganz normales Array ist, bei dem aber 


