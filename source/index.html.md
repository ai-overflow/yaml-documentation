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


@app.route('/image/test/<seed>/', methods=['GET'])
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
    method: GET
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
        "labelValue": "{{cmd.json(root/rnd[0])}}"
      }
    }
  }
}
```

Zum starten müssen sie die Datei `config.dl.yaml` in ihrem Hauptverzeichnis anlegen. Ersetzen Sie den Namen ihres Projektes durch einen aussagekräftigen, menschenlesbaren Titel zur Beschreibung ihres Projektes an.
Anschließend müssen Sie die Verbindung, welche verwendet werden soll um ihrem Projekt Daten zu übergeben zu spezifizieren. Nähere Informationen hierzu finden Sie im Abschnitt [Verbindung](#verbindung).
Am Anfang können Sie den Port, Pfad, sowie die HTTP-Methode auswählen. Wenn Sie gerne einen Input referenzieren wollen, so verwenden Sie hierzu die Syntax `{{input.NAME}}`. Näheres hierzu finden Sie im Abschnitt [Variablen](#variablen).

In diesem Beispiel wird als Input ein Slider verwendet, welcher die Werte 0-1 mit der Schrittweite 0.01 akzeptiert. Das Label wird "Seed" sein.

Die Ausgabe wird der erste Eintrag des Rückgabewertes von "rnd" sein. Näheres hierzu finden Sie im Abschnitt [Ausgabe](#ausgabe).

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

```yaml
connection:
  main:
    ...
  imageConnection:
    ...
  pizzaConnection:
    ...
```

```json
{
  "connection": {
    "main": {},
    "imageConnection": {},
    "pizzaConnection": {}
  }
}
```

Der Eintrag "connection" beschreibt alle möglichen Verbindungen zu Ihrem Service. Da die Möglichkeit besteht mehrere Verbindungen zu besitzen
(Dies kann der Fall sein, wenn z.B. eine Verbindung für das Netzwerk existiert und eine weitere um Daten, wie bspw. Bilder, zu laden), müssen diese benannt werden. 

Diese Benennung kann in dem nebenstehenden Beispiel betrachtet werden.
Hierbei werden drei Verbindungen mit den Namen `main`, `imageConnection` und `pizzaConnection` erstellt. Jede dieser Verbindungen kann auch als Wert für `entryPoint` verwendet werden.


## Port, Path und Method

> GET http://hostname:8085/image/test/{{input.algo}}/

```yaml
connection:
  main:
    port: 8085
    path: "/image/test/{{input.algo}}/"
    method: "GET" # POST, PUT, DELETE...
```

```json
{
  "connection": {
    "main": {
      "port": 8085,
      "path": "/image/test/{{input.algo}}/",
      "method": "GET"
    }
  }
}
```

Port, Path und Method sind die minimalen Angaben für eine funktionsfähige Verbindung. Hierbei ist zu beachten, dass der Host nicht angegeben wird. Dieser wird implizit über Docker ermittelt.

<aside class="notice">Wenn Sie eine externe Verbindung zu einem anderen Service (z.B. externer API) benötigen, so können Sie dies innerhalb ihres Services implementieren und die Schnittstelle nach außen weiterreichen</aside>

- **Method** unterstützt folgende Werte: `GET`,`PUT`,`PATCH`,`POST`,`DELETE`,`HEAD`,`COPY`,`OPTIONS`,`LINK`,`UNLINK`,`PURGE`,`LOCK`,`UNLOCK`,`PROPFIND`,`VIEW`
- **Port** unterstützt Zahlenwerte von 1024-65535. Dieser Wert kann frei gewählt werden.
- **Path** kann ein beliebiger Pfad ohne Parameter (/abc/**?a=b**) sein. Dieser muss mit einem `/` beginnen und enden. Dieser Wert unterstützt Variablen

## Parameter

> http://hostname:8085/abc/**?a=1&b={{input.inputA}}/**

```yaml
connection:
  main:
    # ...
    params:
      a: "1"
      b: "{{input.inputA}}"
```

```json
{
  "connection": {
    "main": {
      "params": {
        "a": "1",
        "b": "{{input.inputA}}"
      }
    }
  }
}
```

Parameter, wie Beispielsweise http://hostname/abc?**a=1&b=2** werden im Feld `params` angegeben. Die Werte von Parametern unterstützen Variablen.

## Header

> Header für `Cookie` und `User-Agent`

```yaml
connection:
  main:
    # ...
    headers:
      Cookie: "SESSION-xyz"
      User-Agent: "Something Special"
```

```json
{
  "connection": {
    "main": {
      "headers": {
        "Cookie": "SESSION-xyz",
        "User-Agent": "Something Special"
      }
    }
  }
}
```

Header können analog zu Parametern angegeben werden mit dem Schlüssel `headers`. Die Werte von Headern unterstützen ebenfalls Variablen.

## Body

> Body vom Typ `binary`

```yaml
connection:
  main:
    # ...
    body:
      type: "binary"
      input: "{{input.abc}}"
```
```json
{
  "connection": {
    "main": {
      "body": {
        "type": "binary",
        "input": "{{input.abc}}"
      }
    }
  }
}
```

> Body vom Typ `raw`

```yaml
connection:
  main:
    # ...
    headers:
      Content-Type: "application/json"
    body:
      type: "raw"
      input: |
        {
          "a:": "{{input.a}}"
        }
```
```json
{
  "connection": {
    "main": {
      "headers": {
        "Content-Type": "application/json"
      },
      "body": {
        "type": "raw",
        "input": "{\n  \"a:\": \"{{input.a}}\"\n}\n"
      }
    }
  }
}
```

> Body vom Typ `form-data`

```yaml
connection:
  main:
    # ...
    body:
      type: "form-data"
      input:
        mainImage:
          type: "file"
          value: "{{input.image}}"
          contentType: "auto"
        testValue:
          type: "text"
          value: "{{input.checkboxA}}"
```
```json
{
  "connection": {
    "main": {
      "body": {
        "type": "form-data",
        "input": {
          "mainImage": {
            "type": "file",
            "value": "{{input.image}}",
            "contentType": "auto"
          },
          "testValue": {
            "type": "text",
            "value": "{{input.checkboxA}}"
          }
        }
      }
    }
  }
}
```

Um Daten im Body zu speichern existieren drei verschiedene Optionen: `form-data`, `binary`, `raw`.

- `binary` wird genutzt um Binärdaten, wie beispielsweise Bilder zu übertragen. Dieser Wert erlaubt nur Variablen als input. Diese müssen zusätzlich in Binärform vorliegen. So kann zum Beispiel `input.abc` im nebenstehenden Beispiel vom Typ `image` sein.
- `raw` wird verwendet um reine Textnachrichten zu übermitteln. Hierbei wird empfohlen über die `headers` Schlüssel noch den Wert `Content-Type` passend zum übertragenen Datentyp anzupassen
- `form-data` erlaubt die Angabe mehrerer Eingabeelemente, welche sowohl Text als auch Dateien beinhalten können. Jedem Wert muss hierbei einen Namen zugewiesen werden. Zusätzlich muss mittels `type` angegeben werden ob eine Datei als Input verwendet wird (`file`) oder es sich um reinen Text handelt (`text`). `file` unterstützt zusätzlich den Wert `contentType` um den Content-Type anzugeben. Die `contentType` Ermittlung kann mittels des Werts `auto` dem Browser überlassen werden.

# Eingabe

Im Abschnitt `input` werden alle Eingabeelemente angegeben, welche zum verwenden des Modells notwendig sind. 

Mögliche Werte für `type` sind:

| Bezeichnung   | Beschreibung                                                                                                                | Beispiel                               |
|---------------|-----------------------------------------------------------------------------------------------------------------------------|----------------------------------------|
| `image`       | Eingabeelement ist ein Bild, welches sowohl als Base64 als auch als Binärwert übergeben werden kann                         | ![image](images/input/images.png)      |
| `input`       | Eingabeelement ist ein Freitext-Input. Die Länge, sowie Formatierung kann eingeschränkt werden                              | ![image](images/input/input.png)       |
| `slider`      | Eingabeelement ist ein Slider, welcher in einem gewissen Bereich mit einer festgelegten Schrittweite Zahlenwerte ausgibt    | ![image](images/input/slider.png)      |
| `textarea`    | Eingabeelement ist eine Textarea, welche längeren Freitext erlaubt. Die Länge, sowie Formatierung kann eingeschränkt werden | ![image](images/input/textarea.png)    |
| `select`      | Eingabeelement ist ein Select, welches ein Element aus einer (Dropdown-)Auswahl an Elementen erlaubt                        | ![image](images/input/select.png)      |
| `multiselect` | Eingabeelement ist vom Typ Multiselect, welcher mehrere Element aus einer (Dropdown-)Auswahl an Elementen erlaubt           | ![image](images/input/multiselect.png) |
| `checkbox`    | Eingabeelement ist vom Typ Checkbox, welches mehrere Element aus einer Auflistung von Elementen erlaubt                     | ![image](images/input/checkbox.png)    |
| `radio`       | Eingabeelement ist vom Typ Radio, welches ein Element aus einer Auflistung von Elementen erlaubt                            | ![image](images/input/radio.png)       |

```yaml
input:
  imageA:
    label: "Image A"
    type: "image"
    values:
      # ...
```
Der Aufbau eines Eingabeelements folgt immer dem Schema der Nachfolgenden Tabelle:

| Schlüssel | Beschreibung                                    | Mögliche Werte                |
|-----------|-------------------------------------------------|-------------------------------|
| label     | Menschenlesbare Bezeichnung des Eingabeelements | Referenzbild, Bild A, Bumpmap |
| type      | Typ des Eingabeelements                         | siehe [Tabelle](#eingabe)     |
| values    | Parameter zur Konfiguration des Eingabeelements | divers                        |

## Image

> Beispiel für ein Bild-Eingabeelement

```yaml
input:
  imageA:
    label: "Image A"
    type: "image"
    values:
      accepts:
        - "image/jpg"
        - "image/png"
      output: "base64"
```

Um ein Eingabeelement, welches Bild akzeptiert zu erstellen muss als Typ `image` angegeben werden. Hierbei können verschiedene Einschränkungen angegeben werden, wie in der Nachfolgenden Tabelle gesehen werden kann:

| Schlüssel | Beschreibung                                                         | Mögliche Werte                |
|-----------|----------------------------------------------------------------------|-------------------------------|
| accepts   | Liste akzeptierter MIME-Types. Muss **immer** `image/jpg` beinhalten | `image/jpg`, `image/png`, ... |
| value     | Übergabewert der Datei                                               | `binary`, `base64`            |

## Input, Textarea



# Variablen

<!-- TODO: {{user.AGENT}}, {{input.inputA}}, {{cmd.json()}} ... -->
