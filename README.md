# Mein Jahr · KVF 26

Persönliches Jahresprotokoll für das erste Ausbildungsjahr — als Web-App, die sich auf dem Handy wie eine App verhält und auch als Tab in Microsoft Teams läuft.

**Live:** https://friedrichlist.github.io/Mein_1.AJ_KVF26/

Alle Einträge bleiben im Browser des jeweiligen Geräts. Es gibt kein Backend, keine Anmeldung, keine Datenübertragung.

---

## Was liegt hier

| Datei | Zweck |
|---|---|
| `index.html` | Die App. Inhaltlich unverändert gegenüber `Mein_Jahr_KVF26_V20.html`, ergänzt um PWA-Kopfdaten und ein Bootstrap-Skript am Ende. |
| `manifest.webmanifest` | Macht die Seite installierbar (Name, Icon, Startbildschirm). |
| `service-worker.js` | Offline-Cache. Lädt die App beim zweiten Besuch auch ohne Netz. |
| `datenschutz.html` | Datenschutzhinweis, wird vom Teams-Manifest verlinkt. |
| `icon-192.png`, `icon-512.png` | App-Icons. |
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

## Änderungen an der App

`index.html` direkt bearbeiten. Zwei Stellen stammen nicht aus der Originaldatei und sollten erhalten bleiben:

- im `<head>`: der Block mit `manifest.webmanifest`, `apple-touch-icon` und den `theme-color`-Angaben
- vor `</body>`: das Bootstrap-Skript (Service-Worker-Registrierung und Teams-Anbindung)

Die Dark-Mode-Regel wurde um `:root[data-theme="dark"]` ergänzt, damit Teams sein eigenes Design durchreichen kann. Beim Bearbeiten der Farbvariablen beide Blöcke gleich halten.
