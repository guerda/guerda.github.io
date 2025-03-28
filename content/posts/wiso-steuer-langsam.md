+++
title = 'Wiso Steuer langsam auf Windows 10'
date = 2024-04-25T08:54:00+01:00
tags = ['WiSo', 'Windows 10']
+++

Ich nutze auf Windows 10 die Software Wiso Steuer.
Obwohl das Programm recht simpel ist, ist es sehr langsam.
Jede Eingabe, jeder Wechsel von einem Eingabefeld ins nächste dauert mehrere Sekunden.
Dabei ist die CPU auch ohne Grund gut ausgelastet.

Zeit, der Ursache auf den Grund zu gehen.

Eine erste Recherche bringt [einen Forenpost](https://www.buhl.de/wiso-software/forum/index.php?thread/75888-wiso-steuer-sparbuch-extram-langsam-unter-windows-10/) zu Tage, bei dem eine Software namens _Sticky Password_ anscheinend schuld war.
Da ich diese nicht nutze, habe ich weitergesucht.
Im ProcessExplorer habe ich dann einen Dienst entdeckt, der konstant 25% CPU benötigt hat, vor allem, wenn ich in Wiso Eingaben vorgenommen habe.

Der Dienst ist der sogenannte _Dateiversionsverlauf-Dienst_, um persönliche Dateien zu versionieren.
Praktisch, aber warum der Wiso lahmlegt, ist mir nicht klar.
Die Wiso-Datei wird nicht konstant gespeichert, daher hat der Dienst eigentlich nichts zu tun.

![Screenshot der Diensteigenschaften: Dienstname: fhsvc, Anzeigename: Dateiversionsverlauf-Dienst, Beschreibung: Schützt Benutzerdateien vor einem versehentlichen Verlust durch Kopieren der Dateien an einen Sicherungsspeicherort. Pfad zur EXE-Datei: C:\WINDOWS\system32\svchost.exe -k LocalSystemNetworkRestricted -p. Dienststatus: beendet](../service-fhsvc.png)

**Details des Dienstes**
* Dienstname: `fhsvc`
* Anzeigename: Dateiversionsverlauf-Dienst
* Beschreibung: Schützt Benutzerdateien vor einem versehentlichen Verlust durch Kopieren der Dateien an einen Sicherungsspeicherort
* Pfad zur EXE-Datei: `C:\WINDOWS\system32\svchost.exe -k LocalSystemNetworkRestricted -p`

Ich habe den Dienst dann manuell beendet, um den Effekt auf Wiso zu beobachten.
Tatsächlich ist Wiso nun merklich schneller, wenn auch immer noch langsam.
