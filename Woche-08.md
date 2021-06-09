# Woche 08

In dieser Woche machen wir einen kleinen Exkurs in die Arbeit mit Bildern - gerade die Anzeige und Bearbeitung von Bildern sind häufig Anwendungsbereiche eines GUI, daher ist es sehr vorteilhaft, einige Details dazu zu kennen.

## BufferedImage

Die am häufigsten für die Arbeit mit Bildern in Java verwendete Klasse ist das ```BufferedImage```. Dies ist allerdings nur die interne Darstellung der Bilddaten in Java. Das Laden und Schreiben von Bilddateien wird von Hilfsmethoden in der Klasse ```javax.imageio.ImageIO``` übernommen. Eine wichtige Eigenschaft dieser Klasse ist, dass sie den ClassPath des Projekts (also die Liste an Verzeichnissen, in denen Java-Dateien mit weiteren im Projekt verwendbaren Bibliotheken vorhanden sein könnten) nach Plugins durchsuchen kann, die das Lesen bestimmter Bilddateiformate ermöglichen. Dabei können auch native Bibliotheken geladen werden.

### Lesen und Schreiben

Das Laden und Speichern eines Bildes mittels ImageIO ist recht einfach:

```java
BufferedImage img = ImageIO.read(new File("file.png"));
ImageIO.write(img, "png", new File("newfile.png"));
```

Neben dem Laden und Speichern von Bildern können diese mittels ```BufferedImage``` auch bearbeitet werden. Dies kann entweder auf Pixel-Ebene oder mit umfangreicheren Zeichenmethoden erfolgen.

### Bearbeiten: Pixelweise

Auf die Pixelwerte eines Bildes kann durch die Methoden ```int BufferedImage.getRGB(int x, int y)``` sowie ```BufferedImage.setRGB(int x, int y, int rgba)``` lesend und schreibend zugegriffen werden. Dabei ist der zurückgegebene/übergebene rgba-integer ein integer, der die drei Farbkomponenten Rot, Grün und Blau sowie die Transparenz repräsentiert. Dabei ist jeweils ein Byte in dem integer für die Darstellung jeder dieser Komponenten zuständig. Durch entsprechendes Maskieren und shiften des rgba-integers lassen sich die einzelnen Farbkomponenten extrahieren, was allerdings recht unkomfortabel ist. 

### Die Color-Klasse

Etwas weniger effizient aber deutlich komfortabler ist die Verwendung der Klasse ```java.awt.Color``` - ein ```Color```-Objekt kann wahlweise mit Werten für die einzelnen Farbkomponenten oder einem rgba-integer erstellt werden und bietet Methoden, um die einzelnen Farbwerte oder den rgba-integer zu berechnen:

```java
Color c = new Color(12, 155, 90, 255);
int red = c.getRed(); // =12
int green = c.getGreen(); // =155
int blue = c.getBlue(); // =90
int alpha = c.getAlpha(); // =255
int rgba = c.getRGB();
```

### Bearbeiten: Zeichenoperationen mit der Graphics-Klasse

Eine Alternative zum Setzen der einzelnen Pixel ist die Verwendung der ```Graphics```-Klasse, die Methoden für das Zeichnen von Formen und Texten anbietet:

```java
Graphics gr = image.getGraphics();
gr.setColor(Color.RED);
gr.drawString("This is a red text at x=20, y=40", 20, 40);
gr.setColor(new Color(125, 125, 0));
gr.drawRect(30, 80, 100, 250); // Viereck bei x=30, y=80, mit Breite=100 und Höhe=250
gr.dispose(); // Führt die Zeichenoperationen aus (erst das verändert das image)
```

Die Liste der von einem ```Graphics```-Objekt angebotenen Zeichenoperationen ist in der [offiziellen Java-Dokumentation von Oracle](https://docs.oracle.com/javase/7/docs/api/java/awt/Graphics.html) zu finden.

### Erstellen neuer BufferedImages

Selbstverständlich können Bilder nicht nur geladen werden - es können auch komplett neue ```BufferedImage```-Objekte mit definierter Größe erstellt werden, die mit den oben beschriebenen Methoden neu befüllt und gespeichert (oder in einem GUI angezeigt) werden können. Dabei ist allerdings zu beachten, dass zur Beschreibung eines Bildes nicht nur die Dimensionen, sondern auch das Farbmodell gehört - also die Information, in welcher Art die Farben gespeichert werden. Beispielsweise ist in dem folgenden Code das erste Bild ein RGBA-Bild, bei dem die Pixel-Werte wie oben beschrieben funktionieren. Das zweite Bild ist ein Grauwertbild, welches zwar über die gleichen Methoden bearbeitet werden kann, bei dem aber für den gesamten Pixelwert nur ein Byte zur Verfügung steht - entsprechend darf z. B. der Wert eines an ```setRGB``` übergebenen rgba-integers nicht größer als 255 (also die größte Zahl, die in einem Byte gespeichert werden kann) übersteigen:

```java
int width = 100;
int height = 200;
BufferedImage rgbaImage = new BufferedImage(width, height, BufferedImage.TYPE_INT_ARGB);
BufferedImage grayscaleImage = new BufferedImage(width, height, BufferedImage.TYPE_BYTE_GRAY);
```
