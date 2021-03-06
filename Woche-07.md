# Woche 07 

In dieser Woche fügen wir dem GUI Interaktion hinzu, und schauen uns hierfür noch ein paar Feinheiten der objektorientierten Programmierung in Java an.

## Das observer pattern

Für häufig auftretende Herausforderungen in der Programmierung haben sich mit der Zeit generische Lösungsansätze herauskristallisiert. Diese werden als "Design Pattern" bezeichnet, und Sie werden mehrere übliche Beispiele im nächsten Semester behandeln. Hier bekommen Sie aber schon mal einen ersten Kontakt: Dass es bestimmte Bestandteile eines Programms gibt (wie z. B. GUI-Komponenten), die auf irgendeine Art mit anderen Programmteilen kommunizieren müssen, wenn bestimmte Dinge passieren (wie z. B. dass die Komponente angeklickt wurde), ist nämlich eine solche wiederkehrende Herausforderung. Außer bei der GUI-Entwicklung tritt diese beispielsweise bei der Arbeit mit Sensoren (bestimmte Dinge müssen passieren, wenn neue Messwerte aufgenommen/Schwellenwerte überschritten wurden) oder in der Netzwerkkommunikation (andere Programmkomponenten sollen benachrichtigt werden, wenn Pakete über ein Interface eingehen) auf.

Ein Lösungsansatz, welcher auch in swing-GUIs verfolgt wird, ist das observer pattern. Dabei ist das Element, mit welchem Dinge geschehen auf die das Programm reagieren soll (subject), dafür zuständig, interessierte Parteien (observers) über relevante Vorkommnisse zu informieren. Zu diesem Zweck müssen dem subject alle interessierten observer mitgeteilt werden. Die observer ihrerseits müssen ein interface implementieren, das entsprechende Methoden definiert, die aufgerufen werden sollen, sobald ein Ereignis auftritt. 

Ein konkretes Minimalbeispiel wäre eine Berechnungsklasse, die unterschiedliche Ausgabeobjekte informieren soll, sobald ihre Berechnung abgeschlossen ist:

```java
import java.util.LinkedList;

public interface CalculationObserver {
    public void calculationFinished(int result);
}

public class HashPrinter implements CalculationObserver {
    private String calculationName;
    
    public void setCalculationName(String name) {
        calculationName = name;
    }
    
    public void calculationFinished(int result) {
        System.out.println("### " + result + " (" + calculationName + ") ###");
    }
}

public class VerbatimPrinter implements CalculationObserver {
    private String calculationName;

    public void setCalculationName(String name) {
        calculationName = name;
    }

    public void calculationFinished(int result) {
        System.out.println("Result for " + calculationName + ": " + result);
    }
}

public class Calculator {
    protected LinkedList<CalculationObserver> observers;
    
    public void addObserver(CalculationObserver observer) {
        observers.add(observer);
    }
    
    public void performCalculation(int a, int b) {
        int result = a+b;
        for(CalculationObserver observer : observers) {
            observer.calculationFinished(result);
        }
    }
}

public class CalculatorMain {
    private String calculationName;
    
    public void runCalculations() {
        HashPrinter hashp = new HashPrinter();
        VerbatimPrinter verbatimp = new VerbatimPrinter();
        Calculator calc = new Calculator();
        calc.performCalculation(1, 2);
        calc.addObserver(hashp);
        calculationName = "second";
        hashp.setCalculationName(calculationName);
        calc.performCalculation(3, 4);
        calc.addObserver(verbatimp);
        calculationName = "third";
        hashp.setCalculationName(calculationName);
        verbatimp.setCalculationName(calculationName);
        calc.performCalculation(5, 6);
    }
    
    public static void main(String[] args) {
        CalculatorMain calc = new CalculatorMain();
        calc.runCalculations();
    }
}
```

In diesem Beispiel hält ```Calculator``` eine Liste von ```CalculationObserver```, die alle über die in dem Interface deklarierte Methode ```calculationFinished(int result)``` informiert werden, wenn die Berechnung abgeschlossen ist. In ```CalculatorMain``` wird zunächst ein neuer ```Calculator``` erstellt, danach werden drei Berechnungen durchgeführt. Die erste Berechnung führt zu keiner Ausgabe, da noch keine ```CalculationObserver``` in ```Calculator``` registriert sind. Nachdem ein ```HashPrinter``` hinzugefügt wurde und eine weitere Berechnung durchgeführt wurde, wird dieser informiert und gibt das Ergebnis aus. Zuletzt wird noch ein ```VerbatimPrinter``` registriert und eine dritte Berechnung wird durchgeführt, deren Ergebnis sowohl vom ```HashPrinter``` als auch vom ```VerbatimPrinter``` ausgegeben wird. Die Ausgabe sieht also wie folgt aus:

```text
### 7 (second) ###
### 11 (third) ###
Result for third: 11
```

## Interne Klassen

Das oben gezeigte Beispiel hat aber einen großen Nachteil: Die Information darüber, wie die Berechnung heißt, muss jedem ```CalculationObserver``` separat mitgeteilt werden (alternativ könnte der ```CalculationObserver``` eine Instanz von ```CalculatorMain``` bekommen, und wenn ```CalculatorMain``` einen getter für ```calculationName``` hätte, den Namen darüber bekommen – auch das wäre aber nicht sonderlich übersichtlich oder sauber).

Da die beiden Klassen ```HashPrinter``` und ```VerbatimPrinter``` aber nur innerhalb von ```CalculatorMain``` genutzt werden sollen, ist es nicht nötig, diese als allgemeinverfügbare, nachnutzbare Klassen zu implementieren. Stattdessen können sie direkt innerhalb von ```CalculatorMain``` als interne Klassen definiert werden. Vorteil ist, dass sie dadurch direkt Zugriff auf alle Attribute von ```CalculatorMain``` haben. Mit dieser Veränderung sähe der Code so aus:

```java
import java.util.LinkedList;

public interface CalculationObserver {
    public void calculationFinished(int result);
}

public class Calculator {
    protected LinkedList<CalculationObserver> observers;
    
    public void addObserver(CalculationObserver observer) {
        observers.add(observer);
    }
    
    public void performCalculation(int a, int b) {
        int result = a+b;
        for(CalculationObserver observer : observers) {
            observer.calculationFinished(result);
        }
    }
}

public class CalculatorMain {

    private class HashPrinter implements CalculationObserver {
        public void calculationFinished(int result) {
            System.out.println("### " + result + " (" + calculationName + ") ###");
        }
    }

    private class VerbatimPrinter implements CalculationObserver {
        public void calculationFinished(int result) {
            System.out.println("Result for " + calculationName + ": " + result);
        }
    }

    private String calculationName;
    
    public void runCalculations() {
        HashPrinter hashp = new HashPrinter();
        VerbatimPrinter verbatimp = new VerbatimPrinter();
        Calculator calc = new Calculator();
        calc.performCalculation(1, 2);
        calc.addObserver(hashp);
        calculationName = "second";
        calc.performCalculation(3, 4);
        calc.addObserver(verbatimp);
        calculationName = "third";
        calc.performCalculation(5, 6);
    }
    
    public static void main(String[] args) {
        CalculatorMain calc = new CalculatorMain();
        calc.runCalculations();
    }
}
```

Dadurch, dass ```HashPrinter``` und ```VerbatimPrinter``` interne Klassen von ```CalculatorMain``` sind, entfällt das Verwalten von ```calculationName``` innerhalb dieser Klassen sowie das entsprechende Setzen in ```runCalculations```. 

## Anonyme Klassen

Ein weiterer Vereinfachungsschritt ist die Verwendung von anonymen Klassen. Wird eine Klasse nur einer einzigen Stelle verwendet und braucht nirgedwo wiederverwendet werden, so braucht sie auch keinen Namen und kann direkt an der Stelle implementiert werden, an der sie zum einzigen Mal verwendet wird. Da sie keinen Namen hat, wird eine solche Klasse als anonyme Klasse bezeichnet. Anonyme Klassen sind automatisch auch interne Klassen der Klasse, in der sie eingesetzt werden, Eine Implementation des vorhergehenden Beispiels mit anonymen Klassen sieht wie folgt aus:

```java
import java.util.LinkedList;

public interface CalculationObserver {
    public void calculationFinished(int result);
}

public class Calculator {
    protected LinkedList<CalculationObserver> observers;

    public void addObserver(CalculationObserver observer) {
        observers.add(observer);
    }

    public void performCalculation(int a, int b) {
        int result = a + b;
        for (CalculationObserver observer : observers) {
            observer.calculationFinished(result);
        }
    }
}

public class CalculatorMain {
    private String calculationName;

    public void runCalculations() {
        HashPrinter hashp = new HashPrinter();
        VerbatimPrinter verbatimp = new VerbatimPrinter();
        Calculator calc = new Calculator();
        calc.performCalculation(1, 2);
        calc.addObserver(new CalculationObserver() {
            @Override
            public void calculationFinished(int result) {
                System.out.println("### " + result + " (" + calculationName + ") ###");
            }
        });
        calculationName = "second";
        calc.performCalculation(3, 4);
        calc.addObserver(new CalculationObserver() {
            @Override
            public void calculationFinished(int result) {
                System.out.println("Result for " + calculationName + ": " + result);
            }
        });
        calculationName = "third";
        calc.performCalculation(5, 6);
    }

    public static void main(String[] args) {
        CalculatorMain calc = new CalculatorMain();
        calc.runCalculations();
    }
}
```

Hier werden die ```CalculationObserver``` direkt in den Aufrufen der ```addObserver```-Methode in ```runCalculations``` implementiert und instanziiert. Da sie automatisch auch interne Methoden von ```CalculationMain``` sind, können sie weiterhin direkt auf ```calculationName``` zugreifen.

## Event handling mittels observer pattern in swing

In swing gibt es nicht nur eine Sorte Ereignis (event), welches mit einem GUI-Element auftreten kann, sondern sehr viele unterschiedliche: Vom Klick auf einen Button über die Mauszeigerbewegung bis hin zu Texteingaben. Jede ```JComponent``` ist in der Lage, interessierte ```Listener``` (so heißen observer in swing) über unterschiedliche solche events zu informieren. Dabei müssen die ```Listener``` je nach event-Typ, über den sie informiert werden wollen, unterschiedliche Interfaces implementieren, und entsprechend auch unterschiedlichen Listen der ```JComponent``` hinzugefügt werden. Soll beispielsweise auf das Aktivieren eines GUI-Elements (z. B. durch einen Mausklick, aber auch über "Tab->Enter", Sprachsteuerung etc.) reagiert werden, muss der observer das Interface ```ActionListener``` implementieren und der ```JComponent``` über die Methode ```addActionListener``` hinzugefügt werden.

Im folgenden Beispiel wird auf diese Art immer, wenn auf den Button geklickt wird, der Text "Hallo!" ausgegeben, wobei der verwendete ```ActionListener``` als anonyme interne Klasse implementiert ist:

```java
import javax.swing.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;

public class EventDemo extends JFrame {
    public EventDemo() {
        super("Event-Demo");
        JButton helloButton = new JButton("Say hi!");
        helloButton.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent actionEvent) {
                System.out.println("Hallo!");
            }
        });
        add(helloButton);
    }

    public static void main(String[] args) {
        EventDemo demo = new EventDemo();
        demo.setVisible(true);
    }
}
```

## Übliche Operationen: Benachrichtigungen und Dateiauswahl

Da die wenigsten bei der Verwendung von GUIs gleichzeitig die Kommandozeile im Auge behalten, ist es keine gute Idee, relevante Informationen wie z.B. Fehlermeldungen über ```System.out.println()``` auszugeben. Stattdessen sind popup-Fenster eine übliche Herangehensweise. Eine gleichzeitig einfache und flexible Methode, solche Fenster anzuzeigen, bietet die Klasse ```JOptionPane```, für deren Verwendung es auf der dazugehörigen [Dokumentationsseite von Oracle](https://docs.oracle.com/javase/tutorial/uiswing/components/dialog.html) eine schöne Zusammenfassung gibt. Zu beachten hierbei ist ein kleines Detail, auf welches bei der Dokumentation nicht eingegangen wird: Die ```JOptionPane``` ist ein sogenanntes modales Fenster, welches immer vor dem es generierenden Fenster bleibt. Dies kann aber nur garantiert werden, wenn die ```JOptionPane``` weiß, welches andere Fenster sie erstellt hat. Daher ist das erste Argument in der Methode ```showMessageDialog``` die erstellende ```JComponent```. Eine einfache Methode, das "Hauptfenster" einer Komponente zu finden, ist die in jeder ```JComponent``` verfügbare Methode ```getOwner()```.  

Eine weitere sehr übliche Art der Interaktion, die auf diese Art implementiert wird, ist die Auswahl von Dateien. Entsprechend gehört das Dateiauswahlfenster zu den Standard-GUI-Elementen, die in swing bereits implementiert sind: Der ```JFileChooser```, zu dem eine ausführliche Dokumentation auf der entsprechenden [Dokumentations-Seite von Oracle](https://docs.oracle.com/javase/tutorial/uiswing/components/filechooser.html) zu finden ist.