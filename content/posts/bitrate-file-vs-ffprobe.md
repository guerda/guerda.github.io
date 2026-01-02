+++
title = 'Bitrate file vs ffprobe'
date = 2026-01-02T14:09:25+01:00
tags = ["ffmpeg", "ffprobe", "mp3", "Linux"]
draft = true
+++

# Bitrate mit file und ffprobe erkennen

Ich habe in letzter Zeit versucht, MP3-Dateien mit schlechter Audioqualität zu finden.
Hier ist meine Erfahrung, um euch vor Datenverlust zu bewahren.
Ich habe `file` genutzt, um Dateien mit Bitraten von unter 90 kBit/s zu identifizieren.
Ich habe mit einem schnellen Befehl einige Dateien gefunden und wollte sie schon löschen.

```
file *.mp3 
```

Leider zeigt `file` falsche Werte an, vor allem für Dateien mit variabler Bitrate (VBR).
Bei diesen Dateien ist die von `file` angezeigte Bitrate deutlich niedriger als die korrekte Bitrate.

Ein besserer Weg - wenn auch langsamer - ist dabei [`ffprobe` aus der FFMpeg-Suite](https://ffmpeg.org/ffprobe.html).

```
ffprobe -show_entries stream=bit_rate  01\ Money\ \(radio\ edit\).mp3  2>/dev/null|grep bit_rate
````

Hier noch eine hilfreiche StackOverflow-Frage zu dem Thema: [Fast test for audio file bitrate - StackOverflow](https://unix.stackexchange.com/questions/773526/fast-test-for-audio-file-bitrate)