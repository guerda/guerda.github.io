+++
title = 'Ausgewählt-Markierungen aus Lightroom auslesen'
date = 2025-12-14T13:40:00+01:00
tags = ['Lightroom', 'XMP', 'Darktable']
+++

Weil Lightroom nicht mehr auf MacOS X funktioniert, bin ich umgestiegen auf Darktable.
Der Umstieg ist nicht leicht, vor allem weil bei allen Fotos die "Ausgewählt"-Markierung (Picked) fehlte.
Ich habe einen Weg gefunden, die Daten auszulesen, ohne Lightroom zu öffnen.
Das eröffnet den Weg, die Markierungen auch in Darktable wieder nutzbar zu machen

# Der Ausgangslage
Ich besitze eine alte Version von Lightroom, die noch lokal läuft.
Darin habe ich über Jahrzente Fotos gespeichert und ausgewählt, um sie auszudrucken oder zu teilen.
Leider funktioniert die Version nicht mehr mit dem aktuell MacOS und daher war ich gezwungen, eine Alternative zu finden.

Ich bin auf Darktable gestoßen und auf eine gute [Migrations-Anleitung von Mathias Hueber](https://mathiashueber.com/migrate-from-lightroom-to-open-source-alternative/).
Der Migrationsprozess hat funktioniert, leider wurde - wie erwartet - keinerlei Markierung, ob ein Foto ausgewählt wurde, mitgenommen.
Diese Info ist aber ein Hauptbestandteil meiner Arbeitsweise, wie ich mit Fotos umgehe.

# Der Bedarf
Jetzt stand wieder mal an, endlich die Fotobücher der letzten Urlaube zu erstellen.
Vorselektiert hatten wir die Bilder schon - in Lightroom.
Problem war jetzt, dass wir die Selektion nicht mehr hatten.
So schwer kann es doch nicht sein, die Selektion auszulesen, oder?

# Die Lösung
Ich habe mir die Backup-Dateien von dem Katalog angeschaut.
Bei mir lagen das neueste Katalog-Backup unter `~/Pictures/Lightroom/Backups/2024-xx-xx/2024 Lightroom 3 Catalog.lrcat`.
Ein schnelles `file *.lrcat` zeigte mir an, dass es eine SQLite-Datenbank ist:

```
% file *.lrcat
Lightroom 3 Catalog.lrcat: SQLite 3.x database, last written using SQLite version 0, file counter 4, database pages 0, cookie 0x1ac, schema 1, UTF-8, version-valid-for 0
```

Also schnell die Datenbanktabellen angeschaut und voilà, die Tabelle `adobe_images` sieht vielversprechend aus.

Nach ein wenig Versuchen (und Kampf mit dem Datumsformat in SQLite), konnte ich die selektierten Dateien auslesen und in einer CSV-Datei abspeichern:

```
.headers on
.mode csv
.output selektierte-fotos.csv
select stackparent_filename 
  from adobe_images where date(capturetime) > '20xx-xx-xx' and 
  date(capturetime) < '20xx-xx-xx' and 
  pick != 0.0;
.quit
```

Die erste Zeile weißt SQLite an, die Spaltennamen mit auszugeben.
Die zweite Zeile versetzt SQLite in den CSV-Modus, sodass die Daten als kommasepariertes Format ausgegeben werden.
Die dritte Zeile verändert das Ziel von Standardausgabe auf eine Datei, hier `selektierte-fotos.csv`.
Die vierte Zeile ist die Selektion: Der Dateiname eines jedes Bildes aus der Tabelle `adobe_images`, das zwischen den beiden Daten aufgenommen wurde und ausgewählt (`pick`) ist, wird ausgegeben.
Die letzte Zeile beendet SQLite, kann weggelassen werden, wenn man die Session noch nutzen will

# Ausblick
Für einen einmaligen Export ist diese Methode vollkommen ausreichend.
Ich konnte mit einem kleinen Python-Script die Dateien in einen extra Ordner kopieren, sodass ich von dort das Fotobuch endlich anfertigen konnte.
Am Besten wäre es, wenn ein Script die Daten gesamtheitlich ausliest und in den jeweiligen XMP-Dateien (_Sidecar file_) speichern würde.
Diese liest Darktable aus und verwendet sie im eigenen Katalog.
Vielleicht finde ich ja dafür noch Zeit und Muße.
