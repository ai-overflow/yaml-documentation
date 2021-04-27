---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - yaml
  - json

toc_footers:
  - <a href='https://github.com/ai-overflow/example-project'>Demo Repository</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true
---

# Einleitung

In dieser Dokumentation wird spezifiziert, wie Sie ihre Deep Learning Projekte mithilfe einer `config.dl.yaml` erfolgreich beschreiben.

# Aufbau des Projekts

> Ihr Projekt muss folgende Ordnerstruktur besitzen:

```
Name                  Typ     Beschreibung
----                  ---     ---
src                   Ordner  Ihre Projektdaten
.gitignore            Datei   [optional] .gitignore
config.dl.yaml        Datei   Beschreibungsdatei ihres Projekts
docker-compose.yml    Datei   docker-compose, welche ihr Projekt startet
Dockerfile            Datei   [optional] Dockerfile
LICENSE               Datei   [optional] Lizenz ihres Projekts
README.md             Datei   Projektbeschreibung
```

Wenn Sie ihr Projekt erstellen muss dieses der Vorgegebenen Ordnerstruktur entsprechen.

Optionale Elemente müssen bei der Abgabe nicht in ihrem Projekt vorhanden sein, sind aber "nice to have".
Weitere Elemente, welche nicht von "optional" abgedeckt sind, können ebenfalls in ihrem Hauptverzeichnis vorhanden sein.
Alle Elemente, welche im gezeigten Beispiel nicht mit "optional" gekennzeichnet sind, müssen vorhanden sein

# Einstieg

> /src/app/views.py

```python
from app import app
import json


@app.route('/image/test/<seed>/', methods=['POST'])
def algo_test(seed):
  random.seed(seed)
  text = "Seed: {seed:}\n".format(seed = seed)
  results = [random.random() for i in range(10)]
  return json.dumps({"text": text, "rnd": results})
```

> config:

```yaml
entryPoint: main
name: Example Project
connection:
  main:
    port: 8085
    path: /image/test/{{input.sliderA}}/
    method: POST
input:
  sliderA:
    type: slider
    label: Seed
    values:
      min: 0
      max: 1
      stepSize: 0.01
output:
  firstRandomNr:
    type: html
    label: "First Random Number"
    format:
      labelValue: "root/rnd[0]"
```

```json
{
  "entryPoint": "main",
  "name": "Example Project",
  "connection": {
    "main": {
      "port": 8085,
      "path": "/image/test/{{input.sliderA}}/",
      "method": "POST"
    }
  },
  "input": {
    "sliderA": {
      "type": "slider",
      "label": "Seed",
      "values": {
        "min": 0,
        "max": 1,
        "stepSize": 0.01
      }
    }
  },
  "output": {
    "firstRandomNr": {
      "type": "html",
      "label": "First Random Number",
      "format": {
        "labelValue": "root/rnd[0]"
      }
    }
  }
}
```

Zum starten müssen sie die Datei `config.dl.yaml` in ihrem Hauptverzeichnis anlegen. Ersetzen Sie den Namen ihres Projektes durch einen aussagekräftigen, menschenlesbaren Titel zur Beschreibung ihres Projektes an.
Anschließend müssen Sie die Verbindung, welche verwendet werden soll um ihrem Projekt Daten zu übergeben zu spezifizieren. Nähere Informationen hierzu finden Sie im Abschnitt [Verbindung](#Verbindung).
Am Anfang können Sie den Port, Pfad, sowie die HTTP-Methode auswählen. Wenn Sie gerne einen Input referenzieren wollen, so verwenden Sie hierzu die Syntax `{{input.NAME}}`. Näheres hierzu finden Sie im Abschnitt [Variablen](#Variablen).

In diesem Beispiel wird als Input ein Slider verwendet, welcher die Werte 0-1 mit der Schrittweite 0.01 akzeptiert. Das Label wird "Seed" sein.

Die Ausgabe wird der erste Eintrag des Rückgabewertes von "rnd" sein. Näheres hierzu finden Sie im Abschnitt [Ausgabe](#Ausgabe).

<aside class="notice">
Wenn Sie JSON zur Beschreibung ihres Projekts verwenden, müssen Sie die Datei <code>config.dl.json</code> nennen
</aside>

# Struktur

> Die oberste Ebene der Konfigurationsdatei besteht immer aus den folgenden Schlüsseln:

```yaml
entryPoint: ...
name: "Test"
description: "Lorem Ipsum"
authors:
  - "John Doe"
connection: ...
input: ...
output: ...
```

```json
{
  "entryPoint": "...",
  "name": "Test",
  "description": "Lorem Ipsum",
  "authors": [
    "John Doe"
  ],
  "connection": {},
  "input": {},
  "output": {}
}
```
### entryPoint

Der Entry Point beschreibt den ersten Request, welcher zum verschicken der Inputs verwendet werden soll. Dieser Request wird immer nach dem abschicken der Eingabe durchgeführt.

### Name

Der Name ist ein für Menschen lesbarer Titel des Projekts. Folgende Liste zeigt einige gute und schlechte Beispiele für einen Titel auf:

| Ok  | Name                                | Erklärung                                                                                                                      |
| --- | ----------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| ❌   | StudPro-2020WS-T01                  | Bitte nicht die Teambezeichnung als Name wählen                                                                                |
| ❌   | CNN/RNN Objekterkennung Zun et. al. | Bitte keine Erklärung zum zugrundeliegenden Algorithmus oder Autor verwenden. Verwenden Sie hierzu "author" oder "description" |
| ❌   | Obj                                 | Funktionsweise nicht eindeutig                                                                                                 |
| ✔️   | Objekterkennung                     |                                                                                                                                |

### Description

> Mehrzeilige Beschreibung

```yaml
description: |
  ### Heading

  * Bullet
  * Points
```

```json
{
  "description": "### Heading\n\n* Bullet\n* Points\n"
}
```


Die Beschreibung kann alle Informationen beinhalten, welche nicht direkt im Titel enthalten sind, so können unter anderem genaue Algorithmen, Funktionsweise, Zitate und ähnliches verwendet werden. Die Beschreibung unterstützt zudem Markdown zur Formatierung.

Sofern Sie bereits eine ausführliche README haben, muss die Description nicht sehr ausführlich sein, sondern es genügt ein Verweis auf die README.

<aside class="warning">Binden Sie bitte keine externen Bilder, große Tabellen oder ähnliche größere Elemente, welche die Formatierung zerstören können ein. Versuchen Sie, wenn möglich nur wichtige Inhalte hervorzuheben, sofern Sie Markdown verwenden.</aside>

### Authors

```yaml
authors:
  - "John Doe"
  - "Max Mustermann"
  - "Erika Mustermann, M. Sc."
```

```json
{
  "authors": [
    "John Doe",
    "Max Mustermann",
    "Erika Mustermann, M. Sc."
  ]
}
```

In "Authors" können die Autoren des Projekts genannt werden. Hierbei sollten nur die Namen der beteiligten Studenten verwendet werden. Für die Nennung von Teilnehmer Zugrundeliegender Paper verwenden Sie bitte die Beschreibung (`description`).

Die Autoren sind eine Liste von Strings, womit es möglich ist auch mehrere Teilnehmer inkl. Titel anzugeben.

# Verbindung


