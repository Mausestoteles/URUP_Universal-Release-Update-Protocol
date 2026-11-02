# URUP - Universal Release & Update Protocol
**Allgemeine Systemspezifikation - Version 1.0**

Anwendungsbereich: Softwareversionierung und Releaseverwaltung

---

## 1. Definition

URUP ist ein einheitlicher Standard zur Nummerierung, Identifikation und Sortierung von Software-Releases.

Der Standard ist für Entwickler, Softwareanwendungen, Server und KI-Agenten bestimmt.

URUP definiert ausschließlich Versionskennungen und deren Auswertung. Es ist kein eigenständiges Netzwerk-, Download- oder Installationsprotokoll.

## 2. Versionsformat

Die allgemeine Schreibweise lautet:

`MAJOR.MINOR.PATCH[-KANAL.NUMMER][-hf.NUMMER][+b.NUMMER]`

Beispiel:

`3.5.0-dev.1-hf.6+b.2`

| Bestandteil | Bedeutung |
|---|---|
| `3` | Major - Hauptversion |
| `5` | Minor - Funktionsversion |
| `0` | Patch - Korrekturversion |
| `dev.1` | Erster Development-Release |
| `hf.6` | Sechster Hotfix |
| `b.2` | Zweiter Build derselben Releasekennung |

Nicht benötigte Bestandteile werden weggelassen.

## 3. Releasekanäle - Channels

URUP definiert vier offizielle Releasekanäle.

| Kanal | Kürzel | Beschreibung |
|---|---|---|
| Development | `dev` | Frühe Entwicklungsversion. Kann instabil sein und unfertige Funktionen enthalten. |
| Testing | `test` | Version für interne oder externe Tests. Funktionen werden auf Fehler geprüft. |
| Release Candidate | `rc` | Nahezu fertige Version zur abschließenden Prüfung. |
| Full Release | Kein Zusatz | Offiziell freigegebene Version für den regulären Einsatz. |

### 3.1 Kanalregeln

- Jeder Kanal besitzt eine eigene fortlaufende Nummerierung, beginnend mit 1.
- Eine Kanalnummer erhöht sich bei einem neuen Release desselben Kanals und derselben Basisversion.
- Ein Kanalwechsel beginnt mit Kanalnummer 1.
- Hotfixes sind in sämtlichen Kanälen zulässig.
- Beim Full Release entfällt die Kanalbezeichnung.
- Ein direkter Wechsel von Development zu Full Release ist zulässig.

### 3.2 Beispiel eines Releaseverlaufs

```text
3.5.0-dev.1
3.5.0-dev.2
3.5.0-dev.2-hf.1

3.5.0-test.1
3.5.0-test.2

3.5.0-rc.1
3.5.0-rc.2

3.5.1
3.5.1-hf.1
```

Die Kanalpriorität bei identischer Basisversion lautet:

`dev < test < rc < full`

## 4. Nummerierungsregeln

### 4.1 Major, Minor und Patch

- **Major:** Kennzeichnet grundlegende Versionsänderungen.
- **Minor:** Kennzeichnet neue Funktionen oder größere Erweiterungen.
- **Patch:** Kennzeichnet Korrekturen oder kleinere Versionsänderungen.

Alle drei Bestandteile sind verpflichtend und bestehen aus nicht negativen Ganzzahlen.

### 4.2 Development-, Testing- und RC-Nummern

Jede neue Veröffentlichung eines Kanals erhöht dessen Kanalnummer.

Beispiel:

`3.5.0-dev.1` → `3.5.0-dev.2`

Hotfixes erhöhen die Kanalnummer nicht.

### 4.3 Hotfixes

Hotfixes kennzeichnen nachträgliche Fehlerkorrekturen eines bestimmten Releasestandes.

Die zulässigen Hotfixnummern reichen von 1 bis 100.

Beispiel:

```text
3.5.1
3.5.1-hf.1
3.5.1-hf.2
3.5.1-hf.6
```

`hf.6` bedeutet, dass der betreffende Releasestand sechs Hotfix-Veröffentlichungen erhalten hat.

Jede Hotfixnummer bezeichnet den kumulativen Korrekturstand einschließlich der vorhergehenden Hotfixes derselben Reihe.

Bei einem neuen Release oder einer neuen Kanalnummer beginnt eine neue Hotfixzählung.

Nach Hotfix 100 ist für weitere Korrekturen eine neue Releasekennung erforderlich.

### 4.4 Buildnummern

Buildnummern sind optional.

Sie unterscheiden mehrere Builds, obwohl sich die übrige Versionskennung nicht verändert hat.

Beispiel:

```text
3.5.1+b.1
3.5.1+b.2
3.5.1+b.3
```

Die Buildnummer steigt nur, wenn ein weiterer Build mit ansonsten identischer Versionskennung erstellt wird.

Sie beeinflusst die Versionsreihenfolge ausschließlich, wenn alle übrigen Versionsbestandteile identisch sind.

## 5. Versionsvergleich

URUP vergleicht Versionskennungen in folgender Reihenfolge:

1. Major
2. Minor
3. Patch
4. Releasekanal
5. Kanalnummer
6. Hotfixnummer
7. Buildnummer

Fehlende Hotfix- und Buildnummern werden beim Vergleich als 0 behandelt.

Ein neuer Kanal-Release ist höher als sämtliche Hotfixes des vorherigen Releases desselben Kanals.

### 5.1 Beispiel der Versionsreihenfolge

Aufsteigend sortiert:

```text
3.5.0-dev.1
3.5.0-dev.1-hf.6
3.5.0-dev.2
3.5.0-test.1
3.5.0-rc.1
3.5.0
3.5.0-hf.1
3.5.1
3.5.1-hf.2
3.5.1-hf.2+b.2
```

Eine höhere Versionsnummer hat Vorrang vor einer höheren Hotfix- oder Buildnummer einer niedrigeren Version.

## 6. Implementierung auf Servern

Ein Server muss lediglich Informationen über verfügbare URUP-Versionen bereitstellen.

Dies kann beispielsweise über eine JSON-Datei erfolgen.

**Beispiel - releases.json**

```json
{
  "urup": "1.0",
  "software": "example-app",
  "releases": [
    {
      "version": "3.5.1",
      "url": "/downloads/3.5.1.zip"
    },
    {
      "version": "3.5.1-hf.6",
      "url": "/downloads/3.5.1-hf.6.zip"
    }
  ]
}
```

Diese JSON-Struktur ist eine beispielhafte Implementierung und kein verpflichtendes URUP-Dateiformat.

URUP benötigt keine spezielle Serverarchitektur. Bestehende Webserver, APIs oder andere Verteilungssysteme können verwendet werden.

## 7. Implementierung in Software

Eine Anwendung mit URUP-Unterstützung benötigt folgende Funktionen:

1. Eigene URUP-Version speichern.
2. Versionskennungen zerlegen und auf Gültigkeit prüfen.
3. Verfügbare Releases abrufen.
4. Versionsnummern entsprechend der URUP-Reihenfolge vergleichen.
5. Zulässige Releasekanäle berücksichtigen.
6. Die höchste zulässige Versionskennung auswählen.
7. Den eigentlichen Updatevorgang über das vorhandene Updatesystem durchführen.

### 7.1 Beispiel eines automatischen Updates

Installierte Version:

`3.5.1-hf.2`

Verfügbare Versionen:

```text
3.5.0-dev.9
3.5.1-hf.3
3.5.1-hf.6
```

Ergebnis:

`3.5.1-hf.6`

Die höchste zulässige Version wird ausgewählt. Die Software kann direkt auf den neuesten Hotfixstand aktualisiert werden, sofern ein geeignetes Updatepaket verfügbar ist.

## 8. Verwendung durch KI-Agenten

Ein KI-Agent, der URUP verwendet, muss folgende Regeln einhalten:

- Versionskennungen im definierten URUP-Format erstellen.
- Major, Minor und Patch korrekt unterscheiden.
- Die offiziellen Releasekanäle verwenden.
- Kanalnummern bei neuen Releases erhöhen.
- Hotfixnummern auf 1 bis 100 begrenzen.
- Buildnummern nur bei Bedarf ergänzen.
- Versionen anhand sämtlicher definierter Bestandteile vergleichen.
- Nicht benötigte Bestandteile weglassen.
- Keine unbekannten Releaseinformationen erfinden.

Agenten können URUP zur automatisierten Releasebenennung, Versionsprüfung und Auswahl verfügbarer Softwareversionen verwenden.

## 9. Gültige und ungültige Kennungen

| Kennung | Ergebnis |
|---|---|
| `1.0.0` | Gültig |
| `1.0.0-dev.1` | Gültig |
| `1.0.0-test.2-hf.5` | Gültig |
| `1.0.0-rc.3` | Gültig |
| `1.0.0-hf.100` | Gültig |
| `1.0.0+b.3` | Gültig |
| `1.0.0-dev.2-hf.4+b.6` | Gültig |
| `1.0.0-hf.101` | Ungültig |
| `1.0.0-dev.0` | Ungültig |
| `1.0` | Ungültig |
| `1.0.0-unknown.1` | Ungültig |

Führende Nullen sind innerhalb numerischer Bestandteile nicht zulässig.

## 10. Abgrenzung und Verwendung

URUP schreibt nicht vor, wie Software heruntergeladen, installiert, signiert oder verteilt wird.

Diese Aufgaben werden durch die jeweilige Anwendung und deren Infrastruktur geregelt.

URUP kann unabhängig von Programmiersprache, Betriebssystem oder Servertechnologie eingesetzt werden.

Der Standard stellt sicher, dass sämtliche beteiligten Systeme Versionskennungen einheitlich interpretieren, nummerieren und vergleichen.
