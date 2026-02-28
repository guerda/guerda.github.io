+++
title = 'ebusd mit Uptime Kuma überwachen'
date = 2026-02-28T14:04:02+01:00
tags = ["ebus", "ebusd", "MQTT", "Uptime Kuma"]
+++

Ein kleiner Tipp für alle, die mit ebus und ebusd eine Heizung oder andere Geräte auslesen:
In diesem Beitrag zeige ich euch, wie man einfach überwachen kann, ob der ebus-Adapter noch läuft und Signale empfängt.

Die Kombination aus ebusd und dem ebus-Adapter ermöglicht, Heizungen, Wärmepumpen etc. auszulesen und sogar zu steuern.
In einem früheren Beitrag habe ich gezeigt, wie man eine [Vaillant-Heizung mit ebusd in Home Assistant integriert](/posts/mit-ebus-vaillant-heizung-auslesen-und-steuern).
Um nun zu überwachen, ob ebusd korrekt läuft und auch Signale von den Geräten erhält, nutze ich [Uptime Kuma](https://uptime.kuma.pet/).
Bei mir überprüft Uptime Kuma diverse Dienste und meldet mir, wenn einer nicht mehr verfügbar ist.
Das ist hilfreich, wenn man diverse Services laufen hat und schauen möchte, dass alle untereinander sich verlassen können.

Uptime Kuma hat auch die Möglichkeit, auf MQTT-Themen zu lauschen und zu überwachen.
ebusd [veröffentlicht alle Nachrichten von den Geräten, aber auch generelle Infos über MQTT](https://github.com/john30/ebusd/wiki/3.3.-MQTT-client).

Die folgenden Informationen werden im Thema `<präfix>/global/` veröffentlicht:

* `ebusd/global/version` Versionsinfo von ebusd
* `ebusd/global/running` Ob ebusd läuft
* `ebusd/global/uptime` Laufzeit von ebusd
* `ebusd/global/signal` Ob ebusd ein Signal vom Adapter erhält
* `ebusd/global/scan` Status des Scans
* `ebusd/global/updatecheck` Ergebnis des Updatechecks

Die beiden Eigenschaften `running` und `signal` sind für unseren Zweck besonders interessant.
Wir möchten überprüfen, ob die beiden Eigenschaften `true` enthalten, um zu erkennen, ob der Daemon läuft und ein Signal vom Adapter bekommt. 

In Uptime Kuma fügen wir also einen neuen Monitor hinzu und wählen dabei den Monitortyp "MQTT" aus.
Nachdem wir Adresse, Port und eventuell Authentifizierungsdaten eingetragen haben, können wir das Thema auswählen.

![Screenshot von Uptime Kuma, auf dem vier Eingabefelder zu erkennen sind: MQTT Thema ist mit "ebusd/global/running" gefüllt, MQTT-WebSocket-Pfad" ist leer, "MQTT Prüfungstyp" ist auf "Schlüsselwort" gestellt und "MQTT Erfolgs-Schlüsselwort" enthält "true"](/posts/uptime-kuma-mqtt-ebusd.png)

Als Thema tragen wir `ebusd/global/running` ein, als zu erwartendes Schlüsselwort `true`.
Uptime Kuma hört also nach eingestelltem Intervall auf das Thema und benachrichtigt, sobald das Thema nicht mehr `true` enthält.

In gleicher Weise gehen wir mit `ebusd/globa/signal` vor.
Ich empfehle, einen anderen Namen für den Monitor zu wählen, da dieser in der Benachrichtigung verwendet wird.

Sobald also der Daemon nicht mehr läuft oder kein Signal vom Adapter bekommt, schickt Uptime Kuma eine Nachricht.
