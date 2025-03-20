+++
title = 'Mit ebus Vaillant-Heizung auslesen und steuern'
date = 2025-03-20T11:03:10+01:00
+++

Ich habe eine Vaillant-Heizung verbaut und kann seit einem Jahr ziemlich alle Werte, die die Heizung hergibt, automatisch auslesen und einige Werte auch steuern.
Das coole ist: alles landet automatisch in Home Assistant und einer Zeitreihendatenbank zur Auswertung.
Zuerst gibt es ein wenig Hintergrund, dann erzähle ich etwas zum Adapter, zum Setup des _ebusd_-Dienstes und der Integration in Home Assistant via MQTT.

## Hintergrund

Vaillant hat eine proprietäre Schnittstelle auf einigen ihrer Geräte verbaut, die sog. _ebus_-Schnittstelle.
Das Bus-System unterstützt Gasbrennwertheizungen, Wärmepumpen, Steuergeräte etc. und kann mehrere Geräte in einem Bus verbinden.

Das Protokoll haben einige coole Menschen decodiert und einen Adapter dafür gebaut, den _ebus-Adapter_. Alle Infos zu dem Adapter findet ihr hier: [ebusd.de](https://ebusd.de/).
Zu erwähnen sind _john30_ und _Galileo_, aber noch ganz viele andere.

Über das Protokoll lassen sich Messwerte wie Temperaturen auslesen und Steuerwerte wie gewünschte Temperaturen, Zeitplan, Arbeitsmodus etc. können gesetzt werden.

# Adapter

Ich habe mir den Adapter für 17€ bestellt und er sieht sehr unscheinbar aus.
Ein kleiner ESP-Chip auf Board, eine Antenne und eine grüne Anschlussbuchse. 
Der Adapter kann entweder alleine über WLAN, huckepack auf einem Raspberry Pi oder direkt im LAN betrieben werden.

Ich betreibe den Adapter über Micro-USB.
John hat die Firmware für den Adapter bereits aufgespielt gehabt, sodass ich sofort loslegen konnte.
Mit Strom versorgt startet der Adapter und öffnet ein offenes WLAN.
Einmal beigetreten kann über die Weboberfläche alles konfiguriert werden. 
Was habe ich konfiguriert? 
Zunächst dass der Adapter als Client im WLAN sein soll. Dazu musste ich die WLAN SSID sowie das Passwort eintragen. Nach dem Reboot hatte sich der Adapter schon im WLAN gemeldet.
Noch einmal in die Weboberfläche eingewählt muss ich noch eine wichtige Information kopieren: den Device String.
Der steht ganz oben im weißen Kästchen und ist später für den _ebusd_-Dienst wichtig.
Alle weiteren Standards waren in meinem Fall ok.
Über die Weboberfläche lässt sich die Firmware des Adapters auch aktualisieren.

## ebusd-Dienst

Der ebusd (ebus Daemon) ist ein Stück Software, das die Werte vom Adapter ausliest, interpretiert und übersetzt. 
Der Daemon kann die Werte in JSON übersetzen und mit etwas Hilfe auch direkt für Home Assistant aufbereiten.

Ich lasse den Daemon als Docker Compose Service laufen.
Eine Beispieldatei mit sehr vielen Optionen findest du hier bei John:
[docker-compose.example.yaml](https://github.com/john30/ebusd/blob/master/contrib/docker/docker-compose.example.yaml)
Alternativen sind ein eigenständiger Dienst oder als Docker Container.

Die Konfiguration des Dienstes kann über Flags beim Start, über Umgebungsvariablen oder eine Datei erfolgen.
Über docker compose habe ich den Weg der Umgebungsvariablen genutzt.


## Anschluss an die Heizung

Das war der einfachste Schritt.
Der Anschluss an die Heizung erfolgt an den ebus-Port vom Steuergerät, der sich in der Bedienungsanleitung finden lässt. Dabei ist die Polarität egal.
Mit einem einfachen zweiadrigen Kabel habe ich Steuergerät und Adapter verbunden.
Falls bereits Kabel im ebus-Port des Steuergerätes stecken:
**Bitte Kabel stecken lassen und die Kabel vom Adapter hinzufügen.**

## Integration in Home Assistant 

Um den Adapter in Home Assistant zu integrieren, verwende ich
[Mosquitto](https://mosquitto.org/) als MQTT-Broker.

In unserem Docker compose file oder über Umgebungsvariablen können wir 
die Verbindung zu MQTT kontrollieren. Die KOnfigurationsdatei enthält
auskommentierte Beispielwert. So sieht es bei mir aus:

```
services:
  ebusd:
    ...
    environment:
        ...
        # Connect to MQTT broker on HOST
        EBUSD_MQTTHOST: "192.168.178.31"
        # Connect to MQTT broker on PORT (usually 1883), 0 to disable
        EBUSD_MQTTPORT: 1883
        # Set client ID for connection to MQTT broker
        EBUSD_MQTTCLIENTID: "ebusd"
        # Connect as USER to MQTT broker (no default)
        EBUSD_MQTTUSER: "ebus"
        # Use PASSWORD when connecting to MQTT broker (no default)
        EBUSD_MQTTPASS: "s3cr3t"
```

Ab sofort tauchen die Werte der Heizung in Home Assistant als eigene Geräte auf:

![Screenshot der Geräte-Seite in Home Assistant. Der Filter ist auf 'ebus' gesetzt, es werden sieben Geräte gezeigt, die alle vom Typ MQTT sind: ebusd, ebusd 700, ebusd bai, ebusd broadcast, ebusd Broadcast, ebusd eBUS device ebusd vr_70](../ebusd-in-home-assistant.png)

Je nach Setup eurer Heizung werden andere Geräte angezeigt.

Die Geräte `ebus bai`, `ebusd 700` und `ebusd VR 700` sind die für uns interessanten Geräte.

Die Integration bringt sehr viele Werte mit sich und macht viele Listen etwas unübersichtlich, aber gibt jeden Wert aus der Heizung aus, die es hat: Sensoren für den Warmwassertank, Heizkurven etc.

Ich lese zum Beispiel die aktuelle Heizkurve aus, ob die Pumpe aktuell läuft, sowie die Temperaturen der Sensoren.

# Zusammenfassung
Über das Gerät, den Dienst und MQTT lassen sich über 100 Werte aus meiner Heizung einfach ablesen und kontrollieren.
Ich lese die Werte nur aus und steuere nicht damit, das soll auch möglich werden.


