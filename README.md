# Mein 1. AJ · KVF 26

Persönliches Jahresprotokoll für das erste Ausbildungsjahr — als Web-App, die sich auf dem Handy wie eine App verhält und auch als Tab in Microsoft Teams läuft.

**Live:** https://friedrichlist.github.io/Mein_1.AJ_KVF26/

Alle Einträge bleiben im Browser des jeweiligen Geräts. Es gibt kein Backend, keine Anmeldung, keine Datenübertragung.

## Gestaltung (seit 10.10.2026)

Die App trägt das Corporate Design der Listschule Halle (Saale): Farben aus dem Design Manual,
Schrift Montserrat (in die Seite eingebettet, SIL Open Font License 1.1 – es wird nichts von
Google oder anderen nachgeladen), Logo oben, Icon als App-Symbol. Ausnahme: die sieben
Fachfarben bleiben zur Orientierung wie im gedruckten Turnusplan (Entscheidung Christian
vom 10.10.2026). Das CD kommt aus `cd/cd.py` in der Tafeln-Bauumgebung
(`Turnusplan BGT/1. AJ`); `tafeln.js` setzt es in die Tafel und prüft es.

---

## Was liegt hier

| Datei | Zweck |
|---|---|
| `index.html` | Die App, erzeugt aus `Mein_1_AJ_KVF26.html` durch `app-bauen.py`. Nicht von Hand bearbeiten. |
| `app-bauen.py` | Baut aus der reinen App-HTML diese installierbare Fassung. |
| `manifest.webmanifest` | Macht die Seite installierbar (Name, Icon, Startbildschirm). |
| `service-worker.js` | Offline-Cache. Lädt die App beim zweiten Besuch auch ohne Netz. |
| `datenschutz.html` | Datenschutzhinweis, wird vom Teams-Manifest verlinkt. Erzeugt von `app-bauen.py` aus `datenschutz.tpl.html`. |
| `datenschutz.tpl.html` | Text des Datenschutzhinweises mit Marken für das CD. Hier wird der Text gepflegt. |
| `icon-192.png`, `icon-512.png` | App-Icons: Icon der Listschule, weiß auf Dunkelgrün (seit 10.10.2026). |
| `icon-color.png`, `icon-outline.png` | Icons für Teams (192 px farbig, 32 px weiße Linien auf transparent). |
| `teams/manifest.json` | Teams-App-Definition. |
| `mein-jahr-kvf26-teams.zip` | Fertiges Paket für das Teams Admin Center. |

## Installieren

**iPhone** — Safari öffnen (nicht Chrome), Link aufrufen, Teilen-Symbol → „Zum Home-Bildschirm".

**Android** — Chrome öffnen, Link aufrufen, Menü (⋮) → „App installieren" bzw. „Zum Startbildschirm hinzufügen".

**Teams** — muss einmalig von der IT freigegeben werden, siehe unten.

## Teams-App freigeben (IT-Administration)

1. Vor dem Hochladen in `teams/manifest.json` prüfen:
   - `developer.name` und `developer.websiteUrl` auf die Schule anpassen
   - `privacyUrl` / `termsOfUseUrl` zeigen derzeit auf `datenschutz.html` in diesem Repo
2. Paket `mein-jahr-kvf26-teams.zip` verwenden (oder nach Änderungen neu packen, siehe unten)
3. Teams Admin Center → **Teams-Apps → Apps verwalten → App hochladen**
4. Nach der Freigabe erscheint „Mein Jahr" für die Nutzer in der linken App-Leiste

Paket nach Änderungen neu bauen:

```bash
cd teams && cp ../icon-color.png ../icon-outline.png . && zip -j ../mein-jahr-kvf26-teams.zip manifest.json icon-color.png icon-outline.png && rm icon-color.png icon-outline.png
```

## Wichtig zu wissen

**Getrennte Speicher.** Die App auf dem Startbildschirm und die App im Teams-Tab benutzen jeweils einen eigenen Speicher. Einträge erscheinen nicht automatisch auf der anderen Seite. Am besten einen Weg festlegen und dabei bleiben — oder die Sicherungsdatei zum Übertragen nutzen.

**Sicherung.** Der Knopf „Eintragungen sichern" legt `meine-noten-kvf26.json` in den Downloads ab, „Sicherung einlesen" holt sie zurück. Das ist der einzige Weg, Einträge auf ein anderes Gerät zu bekommen oder vor dem Löschen des Browserspeichers zu retten.

**Updates.** Nach einem Push auf `main` dauert es ein bis zwei Minuten, bis GitHub Pages ausliefert. Der Service Worker holt bei jedem Seitenaufruf zuerst die Netz-Version, geänderte Inhalte sind also beim nächsten Öffnen da.

## Neue Version der App einspielen

Die eigentliche App wird an anderer Stelle gepflegt (im OneDrive unter `Turnusplan BGT/1. AJ/Mein_1_AJ_KVF26.html`). Aus dieser Quelldatei entsteht `index.html` durch ein Skript — von Hand nachpatchen ist nicht nötig:

```bash
python3 app-bauen.py "/Pfad/zu/Mein_1_AJ_KVF26.html"
```

Das Skript legt `index.html` und `datenschutz.html` neben sich selbst ab (seit 10.10.2026; vorher neben der Quelldatei) und lässt die Quelle unverändert. Es ergänzt:

- die Kopfdaten für die Installation auf dem Startbildschirm
- das dunkle Farbschema zusätzlich über `data-theme`, damit Teams sein Design durchreichen kann
- die Breitenangabe (linksbündig, bis 1080 px)
- den Link zur ausführlichen Datenschutzseite im Fußbereich
- die Registrierung von Offline-Speicher und Teams-Anbindung vor `</body>`
- `datenschutz.html` aus `datenschutz.tpl.html`, mit Schrift, Farbwerten, Logo und Favicon aus der Tafel

Findet das Skript einen Ansatzpunkt nicht oder mehrfach, bricht es ab und sagt welchen — dann hat sich die Quelldatei an dieser Stelle geändert und das Skript muss angepasst werden.

Anschließend die erzeugte `index.html` ins Repo hochladen (bei Änderungen am Text oder am CD auch `datenschutz.html`).

App-Icons neu erzeugen (nur wenn sich das CD ändert): `python3 "<Tafeln-Bauumgebung>/cd/cd.py" --app-icons .` (der Ordner `cd/` liegt in `Tafeln_Bauumgebung_V204-KVF26e_CD_10102026.zip`) – danach das Teams-Paket neu packen und in `teams/manifest.json` die `version` erhöhen.
