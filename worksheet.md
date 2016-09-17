# Erste Schritte mit der PiCamera

Das Kameramodul ist eine tolle Hardware-Komponente für den Raspberry Pi, die es dem Nutzer erlaubt, sowohl Standbilder als auch Full HD Videos aufzunehmen.

## Das Kameramodul anschließen

Zu aller erst musst du die Kamera natürlich an den Raspberry Pi anschließen. Dafür muss der Pi ausgeschaltet sein. Wenn du die Kamera per Kabel verbunden hast, schaltest du den Pi ein und musst dann noch sicherstellen, dass die benötigte Software auch aktiviert ist. Aber hier auch nochmal eine Schritt-für-Schritt Anleitung:

1. Finde den Kamera-Port und verbinde die Kamera

    ![Schließe die Kamera an](images/connect-camera.jpg)
    
2. Starte deinen Raspberry Pi

3. Öffne das **Raspberry Pi Configuration Tool** im Hauptmenü

    ![Raspberry Pi Konfigurationstool](images/raspi-config-menu.png)
    
4. Überprüfe, ob die Kamera-Software aktiviert (engl. enabled) ist

    ![Kamera-Software aktiviert](images/raspi-config.png)

    Wenn dies nicht der Fall ist, aktiviere die Kamera und starte deinen Pi neu.

## Kamera Vorschau

Super! Deine Kamera ist jetzt verbunden und auch die Software ist aktiviert. Also worauf wartest du noch? Probiere doch einfach mal die Kamera Vorschau aus. Das ist auch gar nicht so schwer. Wir zeigen dir, wie es geht

1. Öffne **Python 3** im Hauptmenü:

    ![Öffne Python 3](images/python3-app-menu.png)
    
2. Erstelle eine neue Datei und speichere sie mit dem Namnen `camera.py`. Aber aufgepasst! Es ist wichtig, dass du die Datei **nicht** unter `picamera.py`speicherst!

3. Als nächstes musst du den folgenden Code eingeben:

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.start_preview()
    sleep(10)
    camera.stop_preview()
    ```
4. Jetzt speicherst du alles mit **Strg + S** und startest die Vorschau mit **F5**. Die Kamera Vorschau sollte dir jetzt für 10 Sekunden angezeigt werden und sich dann wieder schließen. Bewege die Kamera nun ein bisschen herum, damit du sehen kannst, was die Kamera sieht.

    Die live Vorschau sollte auf dem Bildschirm in etwa so aussehen:

    ![Image preview](images/preview.jpg)
    
    **Bitte beachte, dass die Kamera Vorschau nur dann funktioniert, wenn du auch einen Bildschirm an den Pi angeschlossen hast. Über Fernzugriff (wie etwa SSH und VNC) kannst du die Vorschau nicht nutzen**
    
5. Deine Vorschau war auf den Kopf gestellt? Kein Problem! Du kannst das Bild mit folgendem Code drehen:

    ```python
    camera.rotation = 180
    camera.start_preview()
    sleep(10)
    camera.stop_preview()
    ```

    Das Bild kannst du um `90`, `180`, oder `270` Grad drehen und mit `0`setzt du es auf die ursprüngliche Ansicht zurück.

6. Die Transparenz der Kamera kannst du ändern, indem du einen Alphakanal erstellst:

    ```python
    from picamera import PiCamera
    from time import sleep

    camera = PiCamera()

    camera.start_preview(alpha=200)
    sleep(10)
    camera.stop_preview()
    ```

    Für `alpha` kannst du jeden beliebigen Wert zwischen `0`und `255`auswählen.
    
    Transparenz? Alphakanal? Du hast noch nie davon gehört? Keine Sorge, klingt alles viel schlimmer als es ist.
    Das Wort Transparenz ist dir bestimmt ein Begriff. Es heißt nichts anderes als Durchsichtigkeit. Und das bedeutet, dass du in einem Bild ein Element (zum Beispiel eine Blume) sehen kannst, obwohl es eigentlich durch ein anderes Element (zum Beispiel einen Schmetterling) verdeckt ist. Da der Schmetterling aber transparent, also durchsichtig, ist, kannst du die darunterliegende Blume trotzdem sehen. 
    Die Informationen zu dieser Transparenz werden im sogenannten Alphakanal gespeichert. Wenn ein Bild keinen Alphakanal hat, dann können auch keine Bereiche oder Elemente transparent dargestellt werden.

## Standbilder

Die meistgenutzte Funktion der Kamera ist die Aufnahme von (Stand-)Bildern. Wie du das machst, zeigen wir dir jetzt:

1. Um ein Foto zu machen, musst du deinen Code von vorhin nur ein kleines bisschen verändern. Zum einen solltest du den Wert für `sleep`verringern, sodass die Vorschau nur noch 5 Sekunden beträgt. Zum anderen fügst du in der Zeile nach der `sleep`-Funktion  `camera.capture()` hinzu und gibst dort auch den Speicherort an:

    ```python
    camera.start_preview()
    sleep(5)
    camera.capture('/home/pi/Desktop/image.jpg')
    camera.stop_preview()
    ```

    Es ist wichtig, dass du das Programm für mindestens 2 Sekunden pausierst, bevor das Bild aufgenommen wird, damit der Sensor ausreichend Zeit hat, um das Licht einzustellen.

2. Starte nun das Programm (**F5**) und du wirst sehen, dass sich die Kamera Vorschau für 5 Sekunden öffnet, bevor das Foto aufgenommen wird. In dem Moment, in dem das Foto gemacht wird, ändert sich die Auflösung der Vorschau.

3. Jetzt solltest du die Bilddatei auf deinem Desktop sehen. Du kannst es ganz einfach mit einem Doppelklick öffnen:

    ![Foto auf dem Desktop](images/desktop.png)

4. Das hat doch schonmal ganz gut geklappt. Versuchen wir nun, eine Schleife in das Programm einzubauen, sodass gleich 5 Bilder hintereinander aufgenommen werden:

    ```python
    camera.start_preview()
    for i in range(5):
        sleep(5)
        camera.capture('/home/pi/Desktop/image%s.jpg' % i)
    camera.stop_preview()
    ```

    In Zeile 3 siehst du ein i. Das i ist eine Variable, die Informationen zur Nummer des Bildes enthält. Sie sagt also aus, um das wievielte Foto es sich aus dieser Reihe handelt, von 0 bis 4. Das heißt, das erste Foto wird als `image0.jpg` gespeichert, das zweite als `image1.jpg` und so weiter.
    
5. Führe das Programm nun erneut aus und halte die Kamera in Position. Wenn jetzt nichts schief geht, wird alle 5 Sekunden ein Foto gemacht.

6. Sobald das letzte Foto gemacht wurde, schließt sich die Vorschau wieder. Die aufgenommenen Bilder kannst du dir jetzt wieder auf deinem Desktop anschauen.

## Videos aufnehmen

Das mit den Fotos ist ja schonmal eine coole Sache, aber deine PiCamera kann noch mehr. Neben Standbildern ist sie nämlich auch in der Lage, Full HD Videos aufzunehmen

1. Um Videos aufzunehmen, musst du wieder nicht viel an deinem Code verändern. Praktisch, oder? Du ersetzt einfach `capture()` mit `start_recording()` und `stop_recording()`. Das Ganze sollte dann so aussehen:

    ```python
    camera.start_preview()
    camera.start_recording('/home/pi/video.h264')
    sleep(10)
    camera.stop_recording()
    camera.stop_preview()
    ```

2. Und schon kann es los gehen. Starte dein Programm und es wird ein 10 sekündiges Video aufgenommen. Sind die 10 Sekunden um, schließst sich die Vorschau wieder.

3. Als nächstes willst du dir die Aufnahme bestimmt ansehen. Nichts leichter als das! Zu erst musst du das Terminalfenster auf deinem Pi öffnen. Dazu klickst du einfach auf den schwarzen Bildschirm auf der Taskleiste.

    ![Öffne das Terminal](images/open-terminal.png)

4. Gebe nun den folgenden Befehl und drücke dann **Enter**, um das Video zu starten:

    ```bash
    omxplayer video.h264
    ```

    ![omxplayer](images/omxplayer.png)

    Das Video sollte nun beginnen. (Es kann sein, dass das Video schneller abgespielt wird, als es aufgenommen wurde. Das liegt an der schnellen Framrate des `omxplayer`. Eine hohe Framrate bedeutet, dass der Computer nur wenig Zeit braucht, um ein neues Bild auf dem Bildschirm anzuzeigen.)

## Effekte

Am Anfang hast du mit `camera = PiCamera()` ein sogenanntes `camera`-Objekt erstellt. Dieses `camera`-Objekt kannst du nun beeinflussen, in dem du seine Einstellungen veränderst. Wie das geht, lernst du jetzt. Die Kamera-Software stellt eine Anzahl an Effekten und anderen Einstellungsmöglichkeiten zur Verfügung, die du nutzen kannst. Einige beeinflussen nur die Vorschau, andere nur das aufgenommen Foto. Aber viele wirken sich auf beide auf.

1. Die Auflösung der Aufnahme kann verändert werden. Als Voreinstellung ist die Aufnahme an die Auflösung deines Bildschirms angepasst, aber die höchste Auflösung für Standbilder beträgt 2592 x 1944 Pixel, für Videoaufnahmen 1920 x 1080. Probiere doch einfach mal folgendes Beispiel aus und gucke, was passiert. Außerdem solltest du beachten, dass du die Framerate auf `15`setzen musst, um die maximale Auflösung zu ermöglichen:

    ```python
    camera.resolution = (2592, 1944)
    camera.framerate = 15
    camera.start_preview()
    sleep(5)
    camera.capture('/home/pi/Desktop/max.jpg')
    camera.stop_preview()
    ```

2. Du hast gesehen, was passiert ist? Dann teste jetzt doch nochmal die geringste Auflösung. Diese beträgt 64 x 64 Pixel.

3. Gut, das mit der Auflösung hätten wir geklärt. Aber dir bieten sich noch viel mehr Möglichkeiten. Zum Beispiel kannst du ganz einfach Text zu deinen Bildern hinzufügen, und zwar mit `annotate_text`. Probier's mal:

    ```python
    camera.start_preview()
    camera.annotate_text = "Hello world!"
    sleep(5)
    camera.capture('/home/pi/Desktop/text.jpg')
    camera.stop_preview()
    ```

4. Dein Bild ist viel zu dunkel? Natürlich kannst du auch die Helligkeit verändern, kein Problem. Automatisch ist der Wert auf `50`eingestellt, aber du kannst jeden Wert zwischen `0` und `100` auswählen:

    ```python
    camera.start_preview()
    camera.brightness = 70
    sleep(5)
    camera.capture('/home/pi/Desktop/bright.jpg')
    camera.stop_preview()
    ```

5. Was du auch versuchen kannst, ist, die Helligkeit in einer Schleife anzupassen und den aktuellen Helligkeitswert auf dem Bildschirm anzuzeigen:

    ```python
    camera.start_preview()
    for i in range(100):
        camera.annotate_text = "Brightness: %s" % i
        camera.brightness = i
        sleep(0.1)
    camera.stop_preview()
    ```

6. Genauso kannst du das natürlich auch für den Kontrast machen:

    ```python
    camera.start_preview()
    for i in range(100):
        camera.annotate_text = "Contrast: %s" % i
        camera.contrast = i
        sleep(0.1)
    camera.stop_preview()
    ```

7. Die Größe des Textes kannst du folgendermaßen ändern:

    ```python
    camera.annotate_text_size = 50
    ```

    Auswählen kannst du die Schriftgröße von `6`zu `160`, wobei `32` die Standardgröße ist.

8. Desweiteren hast du die Möglichkeit, die Schriftfarbe nach deinen Wünschen zu gestalten, aber zuerst einmal müssen wir sicherstellen, dass `Color`auch importiert ist. Dazu verändern wir die `import`-Zeile ganz oben:

    ```python
    from picamera import PiCamera, Color
    ```

    Den Rest deines Codes solltest du wie folgt verändern:

    ```python
    camera.start_preview()
    camera.annotate_background = Color('blue')
    camera.annotate_foreground = Color('yellow')
    camera.annotate_text = " Hello world "
    sleep(5)
    camera.stop_preview()
    ```

9. Um zwischen einer Auswahl an Effekten zu wählen, nutzt du einfach `camera.image_effect`. Dadurch stehen dir viele verschiedene Optionen zur Verfügung: `none`, `negative`, `solarize`, `sketch`, `denoise`, `emboss`, `oilpaint`, `hatch`, `gpen`, `pastel`, `watercolor`, `film`, `blur`, `saturation`, `colorswap`, `washedout`, `posterise`, `colorpoint`, `colorbalance`, `cartoon`, `deinterlace1` und `deinterlace2`. Standardmäßig ist natürlich `none`, also kein Effekt, eingestellt. Nun bist du dran! Such dir einen Effekt aus und dann los:

    ```python
    camera.start_preview()
    camera.image_effect = 'colorswap'
    sleep(5)
    camera.capture('/home/pi/Desktop/colorswap.jpg')
    camera.stop_preview()
    ```

10. Versuche doch als nächstes einmal, mit Hilfe einer Schleife während der Vorschau durch die verschiedenen Effekte zu wechseln:

    ```python
    camera.start_preview()
    for effect in camera.IMAGE_EFFECTS:
        camera.image_effect = effect
        camera.annotate_text = "Effect: %s" % effect
        sleep(5)
    camera.stop_preview()
    ```

    ![Effekte](images/effects.jpg)

11. Über die Bildeffekte hinaus kannst du `camera.awb_mode` nutzen, um den Autoweißabgleich voreinzustellen, damit du bestimmte Effekte anwenden kannst. Dabei stehen dir diese Optionen zur Auswahl: `off`, `auto`, `sunlight`, `cloudy`, `shade`, `tungsten`, `fluorescent`, `incandescent`, `flash`, und `horizon`. Der Standard ist `auto`. Also, entscheide dich für eine Option und teste es selbst:

    ```python
    camera.start_preview()
    camera.awb_mode = 'sunlight'
    sleep(5)
    camera.capture('/home/pi/Desktop/sunlight.jpg')
    camera.stop_preview()
    ```

    Du kannst durch die zur Verfügung stehenden Modi des Autoweißabgleich mit `camera.AWB_MODES` wechseln.

12. `camera.exposure_mode` ist dazu da, um den Belichtungsmodus für bestimmte Effekte anzuwenden. Dabei kannst du zwischen `off`, `auto`, `night`, `nightpreview`, `backlight`, `spotlight`, `sports`, `snow`, `beach`, `verylong`, `fixedfps`, `antishake`, und `fireworks` wählen. Standardmäßig ist mal wieder `auto` eingestellt. Nun bist du weider an der Reihe! Auswählen und loslegen:

    ```python
    camera.start_preview()
    camera.exposure_mode = 'beach'
    sleep(5)
    camera.capture('/home/pi/Desktop/beach.jpg')
    camera.stop_preview()
    ```

    Du kannst durch die zur Verfügung stehenden Modi der Belichtung mit `camera.EXPOSURE_MODES` wechseln.

## Was kommt als nächstes?

Jetzt, da du die ersten Schritte mit der PiCamera hinter dich gebracht hast, bleibt die Frage: was kannst du noch machen? Du könntest beispielsweise mal probieren, GPIO Steuerungen hinzuzufügen [GPIO Zero](https://gpiozero.readthedocs.org/), Minecraft Pi mit einzubeziehen oder du postest deine Fotos auf Twitter! Teste einfach noch ein paar Möglichkeiten, die sich dir durch die Kamera bieten:

- [Push button stop-motion](https://www.raspberrypi.org/learning/push-button-stop-motion/)
- [Minecraft Fotobox](https://www.raspberrypi.org/learning/minecraft-photobooth/)
- [Tweeting Babbage](https://www.raspberrypi.org/learning/tweeting-babbage/)
- [Eltern Detektor](https://www.raspberrypi.org/learning/parent-detector/)

Wusstest du schon, dass es auch eine Infrarot-Version der Kamera gibt? Sie heißt Pi NoIR und bietet dir alles, was die normale Kamera auch kann, mit einem Unterschied: Die Pi NoIR hat keinen Infrarotfilter. Dadurch hast du die Möglichkeit, im mit Infrarotlicht im Dunkeln zu sehen. Mit dem [Infrarot Vogelhäuschen](https://www.raspberrypi.org/learning/infrared-bird-box/) lernst du alles Wichtige, was du über die Pi NoIR Kamera wissen musst.

Außerdem findest du viele weitere Informationen in der umfangreichen [PiCamera Dokumentation](https://picamera.readthedocs.org/).
