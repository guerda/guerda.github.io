+++
title = 'Lenovo 510 Webcam unter MacOS'
date = 2024-07-01T09:46:14+02:00
+++

Ich benutze eine Lenovo Webcam 510 unter Fedora und Windows.
Seit Neuestem auch unter MacOS, aber dort ist ein seltsamer Fehler.
Die Kamera wird identifiziert als `Lenovo 510 IR Kamera`.
Wenn man diese in Photo Booth, Teams oder Zoom auswählt, sieht man nur ein schwarz-weißes Bild:

![Screenshot von PhotoBooth mit einem seltsamen schwarz-weiß Bild. Die Farben werden nicht korrekt wiedergegeben. Man kann schemenhaft eine Person erkennen, die an einem Computer sitzt.](../lenovo-510-ir-mode.png)

So ist die Kamera nicht zu benutzen, aber ich habe eine Lösung gefunden.

Es stellt sich raus, dass MacOS diese Kamera nur als Kamera für den Infrarot-Kanal aktiviert und nicht das normaler Kamerabild.
Wofür braucht eine Kamera überhaupt einen Infrarot-Modus?
Für Authentifizierung via Windows Hello und Co. wird der Infrarot-Modus genutzt, um zu erkennen, ob eine echte Person vor der Kamera sitzt oder nur ein Foto vor die Linse gehalten wird.

Also was tun, um das Problem zu beheben?
Neustart, ab- und anstecken, anderer USB-Port, das hat alles nichts gebracht, immer meldete sich die Kamera als IR-Kamera.
Treiber wurden keine speziellen installiert, damit konnte man damit auch nichts machen.

Ich bin auf einen [Reddit-Beitrag](https://old.reddit.com/r/MacOS/comments/10msyr4/lenovo_510_fhd_webcam_on_osx/) gestoßen, der das gleiche Problem schildert.
Abhilfe sollte ein Programm namens [_CameraController_](https://github.com/Itaybre/CameraController) schaffen, das einige Einstellungen an der Kamera vornehmen können soll.
Auf GitHub die aktuelle Version heruntergeladen und gestestet: nichts.
Einstellungen wie Helligkeit, Kontrast, Belichtung etc. ließen sich korrigieren, aber das Gerät blieb eine IR-Kamera.

Dann stieß ich auf ein Firmwareupgrade der Kamera. Lenovo hält ein [Firmware-Upgrade für die Lenovo 510 Webcam](https://support.lenovo.com/de/de/accessories/acc500235-lenovo-510-fhd-webcam-overview-and-service-parts) bereit, das die Kamera aktualisiert.
Das läuft jedoch nur auf Windows, hat aber einwandfrei funktioniert.
Die Firmware von Version 2.9 auf Version 4.6.2 aktualisiert und nach einem erneuten Einstecken in das Macbook zeigt die Kamera das normale Bild.

