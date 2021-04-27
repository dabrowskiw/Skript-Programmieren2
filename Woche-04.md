# Woche 04

In dieser Woche kommen wir - endlich - zur Vererbung! Als erstes Beispiel schauen wir uns Exceptions im Detail an.

## Die Idee der Vererbung

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

Da allerdings sowohl ```LinkedList``` als auch ```ArrayList``` von ```List``` erben (also konkrete Arten von Listen sind, ähnlich die ein Schulranzen und ein Wanderrucksack konkrete Arten von Rucksäcken sind), kann die obere Funktion generischer auch wie folgt geschrieben werden:

```java
public static void printList(List<String> mystringlist) {}
    for(String x : mystringlist) {
        System.out.println(x);    
    }
}
```

Diese Funktion kann dann mit allen Arten von Listen aufgerufen werden - beide der folgenden Aufrufe sind korrekt:

```java
LinkedList<String> listOne = new LinkedList<>();
ArrayList<String> listTwo = new ArrayList<>();
listOne.add("LinkedList-Wert 1");
listOne.add("LinkedList-Wert 2");
listTwo.add("Arraylist-Wert 1")
listTwo.add("Arraylist-Wert 2")
printList(listOne);
printList(listTwo);
```

## Die konkrete Umsetzung in Java

### Vererbung von Eigenschaften

Konkret funktioniert die Vererbung in Java so, dass jede Klasse exakt ein "Elternteil" haben kann (Ausnahmen werden wir sehen, wenn wir uns mit Interfaces befassen), von dem sie abgeleitet ist. Eine abgeleitete Klasse übernimmt alle Eigenschaften (Methoden, Attribute) der Elternklasse, die nicht als ```private``` markiert sind. Somit "kann" eine abgeleitete Klasse alles, was die Elternklasse auch kann.

Konkret wird das Erben über den Begriff ```extends``` in der Klassendefinition erreicht. Sehen wir uns beispielsweise die folgenden Klasse ```Rucksack``` an:

```java
import java.util.LinkedList;

public class Rucksack {
    int maxInhalt = 0;
    LinkedList<String> inhalt = new LinkedList<>();

    /**
     * Erstellt einen Rucksack mit einer definierten Kapazität.
     * @param maxInhalt Wie viele Gegenstände reinpassen.
     */
    public Rucksack(int maxInhalt) {
        this.maxInhalt = maxInhalt;
    }

    /**
     * Einen Gegenstand in den Rucksack legen.
     * @param was Was reingelegt werden soll.
     * @return true, falls der Gegenstand reingelegt wurde. False, falls der Rucksack schon voll war und der Gegenstand nicht reingelegt werden konnte.
     */
    public boolean reinlegen(String was) {
        // Überprüfen, ob noch Platz im Rucksack ist
        if(inhalt.size() < maxInhalt) {
            inhalt.add(was);
            return true;
        }
        return false;
    }

    /**
     * Den obersten Gegenstand aus dem Rucksack rausnehmen.
     * @return Der oberste Gegenstand, oder null falls der Rucksack leer ist.
     */
    public String rausnehmen() {
        if(inhalt.size() > 0) {
            return inhalt.removeLast();
        }
        return null;
    }
}
```

Diese Klasse implementiert die Grundlogik eines Rucksacks: Es ist je nach Größe für eine bestimmte Anzahl an Gegenständen Platz (der Einfachheit halber ignorieren wir hier die Tatsache, dass Gegenstände unterschiedliche Größen und Gewichte haben), und es können Gegenstände reingetan und wieder rausgenommen werden.

Diese Eigenschaften sollte jeder Rucksack besitzen. Ein Schulranzen hat zwar noch Platz für Pausenbrote, allerdings können weiterhin wie im regulären Rucksack allgemeine Dinge reingesteckt und rausgenommen werden. Dies könnte wie folgt implementiert werden:

```java
import java.util.LinkedList;

public class Schulranzen extends Rucksack {
    int maxPausenbrote = 0;
    int pausenbrote = 0;

    /**
     * Einen Schulranzen erstellen.
     * @param maxInhalt Maximale Anzahl an Gegenständen, die reingetan werden können.
     * @param maxPausenbrote Maximale Anzahl an Pausenbroten, die reingetan werden können.
     */
    public Schulranzen(int maxInhalt, int maxPausenbrote) {
        super(maxInhalt);
        this.maxPausenbrote = maxPausenbrote;
    }

    /**
     * Pausenbrot in den Rucksack reintun.
     * @return true, falls fas Pausenbrot reingelegt werden konnte. False, falls kein Platz mehr für weitere Pausenbrote ist.
     */
    public boolean pausenbrotReinlegen() {
        if(pausenbrote < maxPausenbrote) {
            pausenbrote ++;
            return true;
        }
        return false;
    }

    /**
     * Pausenbrot rausnehmen.
     * @return true, falls noch Pausenbrote im Rucksack waren, sonst false.
     */
    public boolean pausenbrotRausnehmen() {
        if(pausenbrote > 0) {
            pausenbrote --;
            return true;
        }
        return false;
    }
}
```

Durch das ```extends Rucksack``` wird ausgesagt, dass der Schulranzen von Rucksack erbt und somit die in Rucksack definierten Methoden und Attribute ebenfalls besitzt. Zudem werden hier aber noch die Methoden für die Verwaltung von Pausenbroten hinzugefügt. Es kann also beides mit einem Schulranzen gemacht werden:

```java
Schulranzen ranzen = new Schulranzen(10, 2);
ranzen.reinlegen("Schulheft");
ranzen.pausenbrotReinlegen()
```

Wichtig ist dabei zu beachten, dass die Initialisierung des Objekts hier über den Constructor ```Schulranzen(int maxInhalt, int maxPausenbrote)``` erfolgt. Da die ganze Funktionalität für das Reinlegen und Rausnehmen von Gegenständen aber über den Constructor von ```Rucksack``` erfolgt, muss auch dieser Constructor der sogenannten Superklasse - der Klasse, von der geerbt wird - aufgerufen werden, um die entsprechende Initialisierung durchzuführen. Das erfolgt über den Aufruf ```super(maxInhalt)```: Hier wird zunächst der Constructor der Superklasse aufgerufen, bevor weitere für den Schulranzen spezifische Initialisierungsschritte durchgeführt werden.

### Überschreiben von Eigenschaften

Erbende Klassen müssen allerdings nicht einfach alles übernehmen, was die Superklasse vorgibt. Beispielsweise könnte ein ```FlaschenSchulranzen``` auch Trinkflaschen aufnehmen - allerdings nehmen die den gleichen Platz ein, wie Pausenbrote. In so einem Fall könnte zwar die Funktionalität von Schulranzen großteils übernommen und, ähnlich wie bei der Erweiterung des Rucksacks zum Schulranzen, um das Verwalten von Trinkflaschen erweitert werden. Allerdings müsste dann bei ```pausenbrotReinlegen``` die Funktionalität auch geändert werden: Es müsste bei der Überprüfung des Platzes auch getestet werden, wie viel Platz von Flaschen eingenommen wird:

```java
import java.util.LinkedList;

public class FlaschenSchulranzen extends Schulranzen {
    int maxFlaschenUndPausenbrote = 0;
    int flaschen = 0;

    /**
     * Einen FlaschenSchulranzen erstellen.
     * @param maxInhalt Maximale Anzahl an Gegenständen, die reingetan werden können.
     * @param maxFlaschenUndPausenbrote Maximale Anzahl an Pausenbroten und Flaschen, die reingetan werden können.
     */
    public FlaschenSchulranzen(int maxInhalt, int maxFlaschenUndPausenbrote) {
        super(maxInhalt, maxFlaschenUndPausenbrote);
        this.maxFlaschenUndPausenbrote = maxFlaschenUndPausenbrote;
    }

    /**
     * Pausenbrot in den Rucksack reintun.
     * @return true, falls fas Pausenbrot reingelegt werden konnte. False, falls kein Platz mehr für weitere Pausenbrote ist.
     */
    public boolean pausenbrotReinlegen() {
        // Anpassung im Vergleich zu Schulranzen: Es können
        // insgesamt nur maximal maxFlaschenUndPausenbrote
        // Flaschen und Pausenbrote reingetan werden
        if((pausenbrote + flaschen) < maxFlaschenUndPausenbrote) {
            pausenbrote ++;
            return true;
        }
        return false;
    }

    /**
     * Flasche in den Rucksack reintun.
     * @return true, falls fas Flasche reingelegt werden konnte. False, falls kein Platz mehr für weitere Flaschen ist.
     */
    public boolean flascheReinlegen() {
        if((pausenbrote + flaschen) < maxFlaschenUndPausenbrote) {
            flaschen ++;
            return true;
        }
        return false;
    }

    /**
     * Flasche rausnehmen.
     * @return true, falls noch Flaschen im Rucksack waren, sonst false.
     */
    public boolean FlascheRausnehmen() {
        if(flaschen > 0) {
            flaschen --;
            return true;
        }
        return false;
    }
}
```

Wird nun auf einem Objekt vom Typ FlaschenSchulranzen die Methode 
```pausenbrotReinlegen``` aufgerufen, wird nicht die in Schulranzen implementierte Methode, sondern die "überschriebene" Methode aus FlaschenSchulranzen ausgeführt. Intern funktioniert das so, dass Java bei einem Methodenaufruf zunächst in der Klasse des verwendeten Objekts (in diesem Beispiel FlaschenSchulranzen) überprüft, ob diese Methode definiert ist. Falls ja, wird diese verwendet. Falls nein, wird in der Superklasse das Gleiche getan: Überprüfen, ob die Methode vorhanden ist, falls ja aufrufen, falls nein in der Superklasse schauen. So wird sich weitergehangelt, bis eine Superklasse gefunden wird, die die Methode enthält.


## Konkrete Verwendung: Exceptions

Das Konzept haben Sie bereits implizit beim Umgang mit Exceptions verwendet. Nehmen wir beispielsweise diesen Code, um eine Datei zu öffnen und den Inhalt auszugeben:

```java
import java.io.BufferedReader;
import java.io.File;
import java.io.FileReader;

public class FileDumper {
    public static void dumpFile(String filename) {
        try {
            BufferedReader reader = new BufferedReader(new FileReader(new File(filename)));
            while(reader.ready()) {
                System.out.println(reader.readLine());
            }
        } catch(Exception e) {
            System.out.println("Fehler beim Ausgeben von " + filename);
        }
    }
}
```

In diesem Code können zwei Exceptions auftreten: Bei der Erstellung von ```BufferedReader``` eine ```FileNotFoundException``` (falls die Datei nicht vorhanden ist), und bei ```reader.readLine()``` eine ```IOException``` (bei diversen systembedingten Zugriffsfehlern). Beide diese Exception-Klassen leiten von der Exception-Oberklasse ```Exception``` auf, wodurch ein allgemeines Abfangen über ```catch(Exception e)``` möglich ist (tatsächlich leitet auch FileNotFoundException von IOException ab, entsprechend würde auch ```catch(IOException e)``` funktionieren).

Das ist nützlich, da man so auf unterschiedliche Typen von Exceptions, die in der Regel unterschiedliche Fehlerquellen beschreiben, unterschiedlich reagieren kann - oder auch (wie in diesem Beispiel) pauschal auf alle Exceptions gleich reagieren kann.

