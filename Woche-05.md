# Woche 05

In dieser Woche schauen wir uns einige Feinheiten der Vererbung an.

## Root-Klasse

Sehen wir uns zunächst folgenden Beispielcode an:

```java
public class TestClass {
    public TestClass() {
        System.out.println("Hallo!");
    }
    
    public static void main(String[] args) {
        TestClass test = new TestClass();
        System.out.println(test.toString());
    }
}
```

Das Ausführen des obigen Codes würde ein Ergebnis liefern wie: ```TestClass@5a42bbf4``` - das bedeutet, es ist möglich, das Objekt ```test``` als String auszugeben. Aber wo kommt die Methode ```toString()``` her?

Wenn eine Klasse kein ```extends``` angibt, dann bedeutet das implizit ```extends Object``` - die Basisklasse aller anderen Klassen ist die [Object-Klasse](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/lang/Object.html). Sie definiert einige Basis-Methoden, wie z.B. ```toString()```, die somit in jeder anderen Klasse auf jeden Fall vorhanden sind. 

## Vererbungshierarchie in JavaDoc

Diese Vererbungshierarchie, die immer mit ```Object``` beginnt, ist auch auf der Dokumentationsseite jeder Klasse in der [API-Dokumentation](https://docs.oracle.com/en/java/javase/) von JAVA (mit der es sehr empfehlenswert ist, sich grundsätzlich auseinanderzusetzen, sie ist ein äußerst wertvolles Nachschlagewerk für die exakte Funktionalität der Standard-Java-Klassen) zu sehen. Sieht man beispielsweise auf der Seite von [LinkedList](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/util/LinkedList.html) nach, sieht man ganz oben die folgende Beschreibung:

```text
Class LinkedList<E>

java.lang.Object
  java.util.AbstractCollection<E>
    java.util.AbstractList<E>
      java.util.AbstractSequentialList<E>
        java.util.LinkedList<E>
```

Wie dort zu sehen ist, ist die ```LinkedList``` direkt von ```AbstractSequentialList``` abgeleitet, und darüber wieder über mehrere Schritte von ```Object```. 

Wie in dieser Darstellung zu erkennen ist, ist dabei jede Klasse von exakt einer Superklasse abgeleitet. Dies ist eine Limitation in Java (nicht allgemein in der objektorientierten Programmierung, andere Sprachen handhaben das anders). Hintergrund ist, dass unterschiedliche Klassen die gleiche Methode implementieren könnten. Nehmen wir beispielsweise die folgenden Klassen:

```java
public class Baustein {
    private String name;
    
    public Baustein(String name) {
        this.name = name;    
    }
    
    public String toString() {
        return "Ein Baustein, der " + this.name + " heißt.";
    }
    
    public boolean kompatibelMit(Baustein andererBaustein) {
        // Nur Bausteine mit gleichem Namen sind miteinander kompatibel
        return andererBaustein.name.equals(this.name); 
    }
}

public class Spielzeug {
    private String owner;
    
    public Spielzeug(String owner) {
        this.owner = owner;
    }
    
    public String toString() {
        return "Ein Spielzeug von " + this.owner + ".";
    }
    
    public boolean darfSpielen(String name) {
        // Nur der Besitzer darf mit seinem Spielzeug spielen
        return name.equals(this.owner);
    }
}

public class Dachziegel extends Baustein, Spielzeug {
    public Dachziegel(String owner) {
        super(owner);
    }
}

public class Main {
    public void main(String[] args) {
        Dachziegel ziegel = new Dachziegel("Max");
        System.out.println(ziegel.toString());
    }
}
```

Hier leitet ```Dachziegel``` sowohl von ```Baustein``` als auch von ```Spielzeug``` ab - was gewissermaßen sinnvoll erscheint, da der Dachziegel sowohl ein Spezialfall von einem Baustein als auch von einem Spielzeug ist. Ein solches Verhältnis wird als Mehrfachvererbung bezeichnet. Es wirft allerdings in ```main()``` einige Fragen auf:
* Was passiert bei ```Dachziegel ziegel = new Dachziegel("Max"");``` aufgerufen? Das ```super(owner);``` im Constructor könnte sich sowohl auf den Cosntructor von ```Baustein``` als auch auf den von ```Spielzeug``` beziehen, aber diese tun intern unterschiedliche Dinge.
* Was passiert bei ```System.out.println(ziegel.toString());```? ```Dachziegel``` implementiert keine eigene ```toString()```-Methode, also muss eine Methode der Superklasse genutzt werden. Dabei geben die jeweiligen Implementationen in ```Baustein``` und ```Spielzeug``` unterschiedliche Werte zurück - welcher soll nun verwendet werden?

Um diese Probleme zu vermeiden, verbietet Java die Mehrfachvererbung. Allerdings ist es natürlich trotzdem sinnvoll, solche Beziehungen wie "Ein Dachziegel ist sowohl ein Spielzeug als auch ein Baustein" darzustellen. Zu diesem Zweck werden Interfaces verwendet. 

## Interfaces

Ein Interface ist ähnlich wie eine Klasse, allerdings implementiert es keine Logik - es deklariert nur Methoden. Eine Klasse, die von einem Interface erbt (hier verwendet man den Begriff ```implements``` und nicht ```extends```), verpflichtet sich, alle diese Methoden auch zu implementieren. Dadurch wird das Problem bei Mehrfachvererbung umgangen: Wenn eine Klasse von mehreren Interfaces erbt, dann ist sichergestellt, dass sie alle darin beschriebenen Methoden hat, aber es kann keine Konflikte geben, weil die Interfaces nicht vorschreiben, was in diesen Methoden passieren soll. Das obige Beispiel mit Interfaces umgeschrieben würde nun so aussehen:

```java
public interface Baustein {
    public String toString();
    
    public boolean kompatibelMit(Baustein andererBaustein);
    
    public String getName();
}

public interface Spielzeug {
    public String toString();
    
    public boolean darfSpielen(String name);
}

public class Dachziegel implements Baustein, Spielzeug {
    String name, owner;
    
    public Dachziegel(String owner) {
        this.owner = owner;
        this.name = "Ein Dachziegel";
    }

    public String toString() {
        return "Ein " + this.name + " von " + this.owner + ".";
    }

    public boolean darfSpielen(String name) {
        // Nur der Besitzer darf mit seinem Spielzeug spielen
        return name.equals(owner);
    }

    public boolean kompatibelMit(Baustein andererBaustein) {
        // Nur Bausteine mit gleichem Namen sind miteinander kompatibel
        return andererBaustein.getName().equals(this.name);
    }
}

public class Main {
    public void main(String[] args) {
        Dachziegel ziegel = new Dachziegel();
        System.out.println(ziegel.toString());
    }
}
```

In dieser Situation ist eindeutig geklärt, was bei den Aufrufen in ```main()``` geschieht. 

Zurückkehrend zu dem Beispiel von [LinkedList](https://docs.oracle.com/en/java/javase/16/docs/api/java.base/java/util/LinkedList.html) ist dort auf der Seite unter der Vererbungshierarchie zu lesen:

```text
All Implemented Interfaces:
    Serializable, Cloneable, Iterable<E>, Collection<E>, Deque<E>, List<E>, Queue<E> 
```

Das ist die Liste der Interfaces, die ```LinkedList``` implementiert - also die Liste der Funktionalität, die es garantiert, anzubieten. Dort findet sich auch die in der letzten Woche behandelte ```List``` wieder.

### Generic Types

