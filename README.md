# Plan

Zusammenstellung von Informationen zur digitalen Bibliotheksarbeit.

Es werden nur öffentlich zugängliche Ressourcen verwendet.
Der Schwerpunkt liegt auf dem GBV und hier insbesondere bei der ULB Sachsen-Anhalt.
Die Inhalte sollten sich aber mehr oder weniger leicht auf andere Anwendungsfälle adaptieren lassen.

Sehr nützlich ist der [Kurs von Felix Lohmeier](https://felixlohmeier.gitbooks.io/kurs-bibliotheks-und-archivinformatik/content/kapitel-3/32-ubertragungsprotokolle-sru-und-oai-pmh.html). 
Vgl. auch die Tutorials der [DNB](https://www.dnb.de/DE/Professionell/Services/WissenschaftundForschung/DNBLab/dnblab_node.html)

# Aufbau

## Praktische Tipps zur effizienteren Gestaltung der eigenen Arbeit:

- Firefox als CBS 'light' mit Hotkeys-Funktion
- vorgefertigte, komplexe Suchanfragen im OPAC (Bsp. auf der [FID-Seite](https://www.menalib.de/vifa/systematik-des-fid-nahost-nordafrika-und-islamstudien/))

## Schnittstellen zu Datenbanken

- Übersicht der Datenbanken des GBV: <http://uri.gbv.de/database/>
- Bibliothekskataloge des GBV: <http://uri.gbv.de/database/opac>
- Halle:
  - Universitätsbibliographie: <https://uri.gbv.de/database/hb-halle>
  - Bibliothekskatalog: <http://uri.gbv.de/database/opac-de-3>

Weitere Schnittstellen:

- DAIA
- PAIA

Informationen hierzu: <https://verbundwiki.gbv.de/display/VZG/Schnittstellen>

## Zeitschriftendatenbank

<https://www.zeitschriftendatenbank.de/services/schnittstellen/sru/>

## SRU-Syntax

Einen Überblick über die erlaubten Such-Indizes bietet die `explain`-Funktion der SRU-Schnittstelle:

```
curl "http://sru.gbv.de/hb-halle?operation=explain" | xmllint --xpath "//*[local-name()='index']/*[local-name()='title']/text()" -
```

Erläuterungen zur SRU-Schnittstelle des K10Plus bietet das [K10Plus-Wiki](https://wiki.k10plus.de/display/K10PLUS/SRU)

Als Suchparameter für die SRU-Schnittstelle stehen zur Verfügung:

| Key                     | Value                                                                          |
| ---                     | ---                                                                            |
| `version`               | `1.1`/`1.2`                                                                    |
| `operation`             | `searchRetrieve`/`explain`                                                     |
| `query`                 | `pica.<SUCHINDEX> = <WERT>` (konkatenierbar mit `and`)                         |
| `maximumRecords`        | *integer* (maximale Zahl = 100)                                                                     |
| `recordSchema`          | `picaxml`, `marcxml`, `dc` [etc.](https://wiki.k10plus.de/display/K10PLUS/SRU) |
| `startRecord`           | *integer*                                                                      |
| `sortKeys` (SRU v. 1.1) [^1]| `year,,1` (aufsteigend), `year,,0` (absteigend)                                |


## Abfrage über SRU-Syntax

Abfrage erfolgt über HTTP-Request

```python

import requests

endpoint = "<DATENBANK-URL>"
params = { 'key' : <VALUE> ,
...
}
resp = requests.get(endpoint, params = params, timeout = 10)

```

Für iterative Abfragen bietet es sich an, ein `timeout` zu definieren, da `requests.get` standardmäßig beliebig lange auf eine Antwort wartet und das Programm damit einfriert.

[^1]: Bei SRU v. 1.2 erfolgt Sortierung durch CQL-Befehl, also durch Hinzüfügung von `sortby year =descending` zum Query-String.
