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

Die HashMap heißt so, weil sie eigentlich nur ein normales array ist. Der Index, unter dem ein value dabei abgelegt wird, wird aus dem hash (falls Sie das noch nicht in einer anderen Vorlesung hatten: Eine hash-Funktion berechnet aus einem beliebig großen Objekt einen Zahlenwert, ein einfaches Beispiel wäre "Addiere die Werte aller bytes in dem Objekt zusammen und gib das Ergebnis modulo 1024 zurück" - diese Funktion würde für jedes beliebige Objekt einen Wert zwischen 0-1023 zurückgeben) des keys berechnet. Dabei muss auf ein paar Details geachtet werden: Bei zwei unterschiedlichen Objekten kann der gleiche hash-Wert rauskommen ("hash-Kollision"), der hash-Wert kann größer werden als die Länge des Arrays, das Array ist irgendwann voll und muss vergrößert werden etc. Mehr über solche Details werden Sie in dem Modul "Algorithmen und Datenstrukturen" lernen.

## Generics - erste kurze Erwähnung

In den Beispielen könnte Ihnen aufgefallen sein, dass die Listen und Maps nicht einfach als ```LinkedList mylist;``` sondern als z.B. ```LinkedList<String> mylistGeneric;``` deklariert sind. Dabei handelt es sich um Generics: Klassen, die Dinge mit anderen Klassen tun (insbesondere verwalten, wie Listen oder Maps), können generalisiert werden. Bei der Deklaration kann dann in dreieckigen Klammern angegeben werden, mit welcher anderen Klasse gearbeitet werden soll. Das hat den Vorteil, dass man sich dann darauf verlassen kann, dieses Objekt auch tatsächlich zurückzubekommen - vereinfacht gesagt könnte aus einer ```LinkedList mylist;``` bei ```mylist.get(0)``` ein String, ein Integer, eine CityCosts oder irgendein anderes Objekt rauskommen und man müsste eigentlcih immer überprüfen, was man denn gerade bekommen hat. Bei einer ```LinkedList<String> mylistGeneric;```führt der Versuch, irgendwas anderes als einen String über die ```àd```-Methode hinzuzufügen, direkt zu einem Fehler, und entsprechen kann man sich sicher sein, bei ```mylistGeneric.get(0)``` einen String zurückzubekommen. Auf die tieferen Details von generics gehen wir aber erst ein, wenn wir uns Vererbung im Detail anschauen. 

## Command line interface

Die einfachste - dadurch aber in keiner Art unwichtige - Form von Benutzerinterface ist das Kommandozeileninterface (CLI, CommandLine Interface). Diese erlauben es, Argumente an ein Programm beim Aufruf von der Kommandozeile aus zu übergeben. So ungewohnt dies wirken mag, falls man nur grafische Benutzeroberflächen (GUI, Graphical User Interface) gewohnt ist, hat das CLI gegenüber GUIs mehrere Vorteile:

 * Die Implementation ist deutlich einfacher, somit kann mit dem gleichen Aufwand ein viel höherer Funktionsumfang erreicht werden
 * Es ist eine einfache Automatisierung möglich: Basierend auf dem, was die Software tun soll, kann als String der Kommandozeilenaufruf generiert werden, während bei einem GUI umständliche Automatisierung von Mauseingaben nötig wäre
 * CLI-Software kann auch auf Servern eingesetzt werden, die keinen Bildschirm und kein Grafiksystem besitzen
 * Es ist eine effizientere Verwendung möglich: CLIs sind konzeptionell nah am natursprachlichen Modell (über den Programmnamen wird dem Computer mitgeteilt, was er tun soll, über die Argumente wird spezifiziert wie er es tun soll), während die Kommunikation über ein GUI die Interaktion auf stummes Zeigen reduziert

Das soll nicht bedeuten, GUIs hätten keine Daseinsberechtigung. Es sind aber einige Argumente, warum CLIs weiterhin relevant sind und auch in Zukunft auf absehbare Zeit viel Software mit CLI statt GUI entwickelt wird. 

### Aufbau einer Kommandozeile

Es gibt unterschiedliche Arten, Argumente an ein Programm mittels CLI zu übergeben. Ein Standard, der sich insbesondere in der Linux-Welt etabliert hat, basiert auf den GNU Coding Standards und den POSIX Conventions for Command Line Arguments. Kurz zusammengefasst:

 * Argumente können Namen haben 
 * Argumentnamen können entweder in einer Langform (ein Wort, z.B. ```name```) oder in einer Kurzform (ein Buchstabe, z.B. ```n```) oder
 * Um anzuzeigen, dass es sich um einen Argumentnamen handelt, werden vor der Langform zwei Bindestriche (z.B. ```--name```) und vor der Kurzform ein Bindestrich (z.B. ```-n```) geschrieben
 * Argumente können Werte haben (z.B. ```--name Max```). Benötigt ein Argument keinen Wert, wird es als flag bezeichnet (sozusagen ein boolean-Wert, der true ist falls das Argument angegeben wird, und sonst false)
 * Argumente können mehrere Werte haben (z.B. ```--names Max Miriam```)

Ein beispielhafter Programmaufruf für ein Programm, welches Kombinationen von Vor- (```--firstnames```) und Nachnamen (```--lastnamesnames```) ausgibt und bei dem man einstellen kann, wie viele Kombinationen ausgegeben werden (```-c```) und ob sie zeilenweise ausgegeben werden sollen (```-l```) könnte sein:

```
printnames --firstnames Max Miriam Lisa --lastnames Müller Schmidt -c 5 -l
```

Die ersten beiden Argumente sind Langformen mit jeweils drei und zwei Werten. Das dritte Argument ist eine Kurzform mit einem Wert. Das vierte Argument ist wieder eine Kurzform, allerdings ohne Wert, also ein flag.  

### Apache Commons-CLI

Wie Sie sehen können, kann durch die Verwendung von Argumenten auf der Kommandozeile eine hohe Flexibilität bei dem Einsatz von Software erreicht werden. All diese Regeln in jedem Programm manuell beim Auswerten von ```String[] args``` umzusetzen, wäre allerdings äußerst mühsam. Entsprechend gibt es externe Bibliotheken, die diese Arbeit übernehmen. In diesem Modul schauen wir uns [Apache Commons CLI](https://commons.apache.org/proper/commons-cli/) an.  