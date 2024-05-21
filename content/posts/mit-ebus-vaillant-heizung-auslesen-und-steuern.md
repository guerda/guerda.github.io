+++
title = 'Mit ebus Vaillant-Heizung auslesen und steuern'
date = 2024-03-22T20:15:10+01:00
+++

Ich habe eine Vaillant-Heizung verbaut und kann seit dieser Woche ziemlich alle Werte, die die Heizung hergibt, automatisch auslesen und einige Werte auch steuern.
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
Alternativen sind ein eigenständiger Dienst oder als Docker Container.

Die Konfiguration des Dienstes kann über Flags beim Start, über Umgebungsvariablen oder eine Datei erfolgen.
Über docker compose habe ich den Weg der Umgebungsvariablen genutzt.


## Anschluss an die Heizung

Das war der einfachste Schritt.
Der Anschluss an die Heizung erfolgt an den ebus-Port vom Steuergerät, der sich in der Bedienungsanleitung finden lässt. Dabei ist die Polarität egal.
Mit einem einfachen zweiadrigen Kabel habe ich Steuergerät und Adapter verbunden.
Falls bereits Kabel im ebus-Port des Steuergerätes stecken:
**Bitte drin lassen und die Kabel vom Adapter hinzufügen.**

## Integration in Home Assistant 