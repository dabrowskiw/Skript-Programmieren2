# Woche 09

In dieser Woche schauen wir uns ein paar letzte Sachen zum Aufbau von GUIs mit Swing an - einige Details zur Bildverarbeitung und zum Layouting sowie das Überschreiben der ```paintComponent```-Methode zum Implementieren eigener GUI-Komponenten.

## Skalieren von Bildern

Häufig werden Bilder geladen, die im Original nicht der Größe entsprechen, welche für das GUI benötigt wird. Für diesen Fall bietet ```Image``` (die Elternklasse von ```BufferedImage```) die Methode ```getScaledInstance``` an:

```java
public BufferedImage scaleImage(BufferedImage input, int newWidth, int newHeight) {
    BufferedImage result = new BufferedImage(newWidth, newHeight, BufferedImage.TYPE_4BYTE_ABGR);
    Image scaledImage = input.getScaledInstance(newWidth, newHeight, Image.SCALE_SMOOTH);
    result.getGraphics().drawImage(scaledImage, 0, 0, null);
    return result;
}
```

## Layout hints

Wie in Woche 6 besprochen werden GUI-Komponenten mittels eines ```LayoutManager``` angeordnet. Dafür können wie z. B. beim ```GridBagLayout``` oder beim ```BorderLayout``` zusätzliche Informationen an die ```add```-Methode übergeben werden, die definieren, wo die Komponente sich befinden soll. Der ```LayoutManager``` benötigt aber auch weitere Informationen bezüglich der Komponente, um genau entscheiden zu können, wo und in welcher Größe sie auf dem Bildschirm auftauchen soll. Diese Informationen werden als Layout hints (Hinweise zum Layout) bezeichnet und von der Komponente selber bereitgestellt. Insbesondere wichtig sind die Informationen zur Größe der Komponente und zu Ihrer Ausrichtung in dem für sie zur Verfügung stehenden Bereich.

### Komponentengröße

Die Komponentengröße kann über vier Methoden definiert werden:

* ```setSize()```: Setzt die initiale Größe der Komponente. Beim Layout kann von dieser aber auch abgewichen werden, insbesondere wenn die ```pack()```-Methode aufgerufen werden soll.
* ```setPreferredSize()```: Setzt die präferierte Größe der Komponente. Der ```LayoutManager``` wird versuchen, die Komponente in dieser Größe darzustellen. Sollte es aber Konflikte mit den anderen Layout-Parametern oder Größen anderer Komponenten geben, wird aber von dieser präferierten Größe abgewichen.
* ```setMinimumSize()``` und ```setMaximumSize()```: Setzt die Minimal- und Maximalgröße der Komponente. Das sind Grenzwerte, die beim Layout nicht über- oder unterschritten werden können. Das bedeutet aber nicht, dass die gesamte Komponente immer in dieser Größe sichtbar bleibt - ragen Komponenten aufgrund ihrer Größenbeschränkungen über den Rand des Fensters hinaus, sind die entsprechenden Bereiche nicht sichtbar. In der nächsten Woche schauen wir uns die ```JScrollPane``` an, die solche überdimensionierten Komponenten mit Scrollbars nutzbar macht.

### Komponentenausrichtung

Zudem können Komponenten Präferenzen haben, wo sie in dem für sie verfügbaren Platz angeordnet werden sollen. Beispielsweise könnte durch zwei nebeneinander liegende Komponenten, die unterschiedliche Größen haben, für die kleinere Komponente im Fenster mehr Platz geben, als sie benötigt (also mehr, als ihre ```maximumSize``` ist). Dann muss der ```LayoutManager``` entscheiden, wo in diesem verfügbaren Platz die Komponente angeordnet werden soll. Dies kann über die zwei Methoden ```setAlignmentX()``` und ```setAlignmentY()``` definiert werden. Beide nehmen als Argument jeweils einen Wert zwischen 0 und 1, wobei 0 jeweils "ganz links" bzw. "ganz oben" und 1 jeweils "ganz rechts" bzw. "ganz unten" bedeutet. Eine schöne Visualisierung der Auswirkung dieser Parameter ist unter "Box Layout Features" in der [Oracle-Dokumentation zum BoxLayout](https://docs.oracle.com/javase/tutorial/uiswing/layout/box.html) zu finden.

## Eigene Komponenten

Häufig - insbesondere wenn es um die Visualisierung von Daten, wie beispielsweise das Darstellen von Plots oder die Bildanalyse geht - reichen die vordefinierten Komponenten nicht aus, um die Anforderungen zu erfüllen. In solchen Fällen ist es notwendig, eigene Komponenten mit einer spezialisierten Darstellung zu implementieren. 

### Überladen von paint

Das ist im Grunde ganz einfach: Es muss eine Klasse implementiert werden, die von einer anderen Komponente ableitet und, neben eventuell für die Datenverwaltung notwendiger Logik, die ```paintComponent```-Methode überschreibt. Diese Methode wird aufgerufen, wenn die Komponente aufgefordert wird, ihren Inhalt in den dafür vorgesehenen Platz in dem Programmfenster zu zeichnen. Als einziges Argument bekommt diese Methode ein ```Graphics```-Objekt, welches ein Zeichnen in diesem Bereich des Fensters zulässt - genau so, wie Sie es schon in der letzten Woche aus dem Zeichnen in ein ```BufferedImage``` mit dem über ```getGraphics()``` erhaltenen ```Graphics```-Objekt kennen.

Dabei kann im einfachsten Fall von ```JPanel``` geerbt werden, welches in seiner ```paintComponent```-Methode nichts spannendes macht, und die komplette Darstellung selber implementieren. Alternativ kann auch von einer Klasse abgeleitet werden, die schon einen Teil der gewünschten Darstellung mitbringt. Dann kann über ```super.paintComponent()``` diese Zeichenlogik der Elternkomponente verwendet werden und nur die notwendige Erweiterung selber implenmentiert werden.

In dem folgenden Beispiel sind beide Herangehensweisen zu sehen: In ```CarPanel``` wird von ```JPanel``` geerbt und in ```paintComponent``` ein Auto gezeichnet. In ```FunkyButton``` wird von ```JButton``` geerbt und auf die normale Darstellung von ```JButton``` wird nur eine Linie gezeichnet, die ihre Farbe ändert wenn die Maus über dem Button ist (durch den ```MouseListener```). 

```java
package org.htw.prog2.woche9;

import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseEvent;
import java.awt.event.MouseListener;

public class MainFrame extends JFrame {
  /**
   * Klasse, um ein Auto zu zeichnen
   */
  private class CarPanel extends JPanel {
    // Farbe des Autos
    private Color color;

    /**
     * Constructor
     * @param color Farbe, in der das Auto gezeichnet werden soll
     */
    public CarPanel(Color color) {
      super();
      this.color = color;
      Dimension size = new Dimension(100, 100);
      setPreferredSize(size);
      setMaximumSize(size);
      setMinimumSize(size);
    }

    /**
     * Zeichnet das Auto mit zwei Rechtecken und zwei Kreisen
     * @param g Graphics-Kontext
     */
    public void paintComponent(Graphics g) {
      g.setColor(color);
      g.drawRect(40, 35, 40, 20);
      g.drawRect(5, 55, 90, 20);
      g.drawOval(10, 70, 20, 20);
      g.drawOval(70, 70, 20, 20);
    }
  }

  /**
   * Klasse, um einen lustigen Button zu zeichnen
   */
  public class FunkyButton extends JButton {
    // Farbe der Linie im Normalfall
    private Color color;
    // Farbe der Linie, falls die Maus über dem Button ist
    private Color overColor;
    // Zeigt an, ob die Maus über dem Button ist
    private boolean isRolledOver = false;

    /**
     * Constructor
     * @param text Text des Buttons
     * @param color Farbe der Linie im Normalfall
     * @param overColor Farbe der Linie, falls die Maus über dem Button ist
     */
    public FunkyButton(String text, Color color, Color overColor) {
      super(text);
      this.color = color;
      this.overColor = overColor;
      // Mittels MouseListener isRolledOver anpassen, wenn die Maus den Bereich über dem Button
      // betritt oder verlässt
      addMouseListener(new MouseListener() {
        @Override
        public void mouseClicked(MouseEvent mouseEvent) { }
        @Override
        public void mousePressed(MouseEvent mouseEvent) { }
        @Override
        public void mouseReleased(MouseEvent mouseEvent) { }
        @Override
        public void mouseEntered(MouseEvent mouseEvent) {
          isRolledOver = true;
        }
        @Override
        public void mouseExited(MouseEvent mouseEvent) {
          isRolledOver = false;
        }
      });
    }

    /**
     * Button zeichnen
     * @param g Graphics-Kontext
     */
    public void paintComponent(Graphics g) {
      // Den Button selber über die paintComponent-Methode von JButton zeichnen lassen
      super.paintComponent(g);
      // Farbe wählen
      if(isRolledOver) {
        g.setColor(overColor);
      }
      else {
        g.setColor(color);
      }
      // Linie auf den über super.paintComponent() gezeichneten Button malen
      g.drawPolygon(new int[]{0, 5, 10, 29, 172},  new int[]{2, 12, 19, 8, 12}, 5);
    }
  }

  /**
   * Constructor von MainFrame
   */
  public MainFrame() {
      super("paintComponent demo");
      setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
      BoxLayout layout = new BoxLayout(this.getContentPane(), BoxLayout.Y_AXIS);
      setLayout(layout);
      setSize(100, 500);
      CarPanel panel = new CarPanel(Color.blue);
      panel.setAlignmentX(0);
      add(panel);
      FunkyButton button = new FunkyButton("More cars!", Color.magenta, Color.yellow);
      button.setAlignmentX(0);
      add(button);
  }

  /**
   * Entry point
   * @param args Kommandozeilenargumente
   */
  public static void main(String[] args) {
    MainFrame mf = new MainFrame();
    mf.setVisible(true);
  }
}
```

### Neuzeichnen des Fensters

In den Inhalten einer selber implementierten Grafikkomponente können auch Veränderungen auftreten. Allerdings werden diese dann nicht automatisch in dem GUI-Fenster angezeigt - es wäre sehr ineffizient, dauernd alle Elemente neu zu layouten und neu zu zeichnen, obwohl sich in der Regel nichts an der Darstellung ändert. Stattdessen können Komponenten über die ```repaint()```-Methode explizit neu gezeichnet werden, und Änderungen im Layout können dem Fenster über ```revalidate``` mitgeteilt werden.

Erweitern wir beispielsweise die Funktionalität des ```FunkyButton``` im obigen Beispiel durch einen ```ActionListener``` der, wenn auf den Button geklickt wird, um das Hinzufügen eines neuen ```CarPanel```:

```java
//...
    public MainFrame() {
        super("paintComponent demo");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        BoxLayout layout = new BoxLayout(this.getContentPane(), BoxLayout.Y_AXIS);
        setLayout(layout);
        setSize(100, 500);
        CarPanel panel = new CarPanel(Color.blue);
        panel.setAlignmentX(0);
        add(panel);
        FunkyButton button = new FunkyButton("More cars!", Color.magenta, Color.yellow);
        button.setAlignmentX(0);
        // Neuer ActionListener
        button.addActionListener(new ActionListener() {
            @Override
            public void actionPerformed(ActionEvent actionEvent) {
                // Neues CarPanel hinzufügen
                CarPanel panel = new CarPanel(Color.gray);
                panel.setAlignmentX(0);
                add(panel);            
            }
        });
        add(button);
    }
    //...
```

Führen Sie diesen Code aus und klicken Sie auf den Button, passiert scheinbar nichts. Erst, wenn Sie die Größe des Fensters verändern, werden Sie die neuen Autos sehen. Das liegt daran, dass zwar dem Layout neue ```CarPanel``` hinzugefügt wurden, aber die Elemente nicht neu angeordnet wurden und das Fenster nicht neu gezeichnet wurde. Das passiert erst, wenn Sie dies durch das Verändern der Größe mit der Maus forcieren. Programmatisch können Sie das erzwingen, indem Sie hinter ```add(panel)``` einen Aufruf von ```revalidate()``` hinzufügen:

```java
//...
        button.addActionListener(new ActionListener() {
        @Override
        public void actionPerformed(ActionEvent actionEvent) {
                // Neues CarPanel hinzufügen
                CarPanel panel = new CarPanel(Color.gray);
                panel.setAlignmentX(0);
                add(panel);
                // Re-Layouten und Neuzeichnen erzwingen
                revalidate();
            }
        });
//...
```

### Beispiele für Custom-Komponenten

Ein Beispiel für auf diese Art implementierte Custom-Komponenten kennen Sie schon aus dem letzten Semester: Die Chart-Elemente der [XChart-Bibliothek](https://knowm.org/open-source/xchart/), die Sie für die letzten Aufgaben verwendet haben, sind genau auf diese Art implementiert. Ein anderes populäres Beispiel sind Bedienelemente, die das Ribbon-UI von Windows-Programmen nachstellen, wie beispielsweise die [Flamingo-Komponente des Radiance-Projekts](https://github.com/kirill-grouchnikov/radiance). Die Möglichkeiten sind nur durch Ihre Phantasie begrenzt!
