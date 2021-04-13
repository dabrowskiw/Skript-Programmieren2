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

Eine Alternative stellen HashMaps dar. Diese erlauben es, auf Daten ähnlich wie in einer ArrayList über einen Index zuzugreifen - dieser Index muss aber kein Integer sein, sondern es kann ein beliebiges Objekt sein. 
