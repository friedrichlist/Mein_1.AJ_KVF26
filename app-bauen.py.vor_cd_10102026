#!/usr/bin/env python3
"""
Macht aus der reinen App-HTML die installierbare Fassung fuer GitHub Pages.

    python3 app-bauen.py "/Pfad/zu/Mein_1_AJ_KVF26.html"

Schreibt index.html daneben. Die Quelldatei bleibt unveraendert.
Bricht ab, sobald ein Ansatzpunkt fehlt oder mehrdeutig ist - lieber ein
klarer Fehler als eine halb gepatchte Datei.
"""
import re
import sys
import pathlib

KOPF = '''<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<meta name="description" content="Persönliches Jahresprotokoll KVF 26 – alle Daten bleiben auf dem Gerät.">
<meta name="theme-color" content="#F4F6F5" media="(prefers-color-scheme:light)">
<meta name="theme-color" content="#12181A" media="(prefers-color-scheme:dark)">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
<meta name="apple-mobile-web-app-title" content="Mein 1. AJ">
<link rel="manifest" href="manifest.webmanifest">
<link rel="icon" href="icon-192.png" sizes="192x192" type="image/png">
<link rel="apple-touch-icon" href="icon-192.png">'''

BOOTSTRAP = '''
<script>
/* PWA + Teams-Einbettung. Greift nicht in die App-Logik ein. */
(function () {
  if ("serviceWorker" in navigator) {
    addEventListener("load", function () {
      navigator.serviceWorker.register("service-worker.js").catch(function () {});
    });
  }

  // Teams-SDK nur laden, wenn die Seite tatsaechlich eingebettet laeuft.
  // Standalone bleibt die App damit vollstaendig offline-faehig.
  if (window.self === window.top) return;

  var s = document.createElement("script");
  s.src = "https://res.cdn.office.net/teams-js/2.32.0/js/microsoft.teams.min.js";
  s.onload = function () {
    if (!window.microsoftTeams || !microsoftTeams.app) return;
    microsoftTeams.app.initialize().then(function () {
      function anwenden(theme) {
        document.documentElement.setAttribute(
          "data-theme", theme === "dark" || theme === "contrast" ? "dark" : "light"
        );
      }
      microsoftTeams.app.getContext().then(function (ctx) {
        anwenden(ctx && ctx.app && ctx.app.theme);
      }).catch(function () {});
      microsoftTeams.app.registerOnThemeChangeHandler(anwenden);
    }).catch(function () {});
  };
  document.head.appendChild(s);
})();
</script>
</body>'''

DATENSCHUTZ_LINK = ('\n      <br><a href="datenschutz.html" class="ds-link">'
                    'Wo deine Eintragungen liegen – die lange Fassung</a>\n    </p>')

STIL_ZUSATZ = ('\n.ds-link{color:var(--muted);text-decoration:underline;'
               'text-underline-offset:2px}\n.ds-link:hover{color:var(--ink2)}')


def ersetze_einmal(text, alt, neu, was):
    n = text.count(alt)
    if n != 1:
        sys.exit(f"ABBRUCH: Ansatzpunkt '{was}' {n}x gefunden, erwartet genau 1x.")
    return text.replace(alt, neu, 1)


def main():
    if len(sys.argv) < 2:
        sys.exit(__doc__)
    quelle = pathlib.Path(sys.argv[1])
    if not quelle.is_file():
        sys.exit(f"ABBRUCH: {quelle} nicht gefunden.")

    s = quelle.read_text(encoding="utf-8")
    ausgangsgroesse = len(s)

    # 1) Dunkles Farbschema auch ueber data-theme erreichbar machen (fuer Teams)
    m = re.search(r'@media \(prefers-color-scheme:dark\)\{:root\{(.*?)\}\}', s, re.S)
    if not m:
        sys.exit("ABBRUCH: Block fuer das dunkle Farbschema nicht gefunden.")
    farben = m.group(1)
    s = s.replace(
        m.group(0),
        '@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){' + farben + '}}\n'
        ':root[data-theme="dark"]{' + farben + '}',
        1,
    )

    # 2) Kopfdaten fuer Installation auf dem Startbildschirm
    m = re.search(r'<meta name="viewport"[^>]*>', s)
    if not m:
        sys.exit("ABBRUCH: viewport-Angabe nicht gefunden.")
    s = s.replace(m.group(0), KOPF, 1)

    # 3) Breite: linksbuendig, mehr Platz auf grossen Bildschirmen
    s = ersetze_einmal(
        s,
        '.wrap{max-width:820px;margin:0 auto;padding:26px 18px 70px}',
        '.wrap{max-width:1080px;margin:0;padding:26px 18px 70px}\n'
        '/* Ab Tabletbreite etwas Luft zum linken Fensterrand */\n'
        '@media (min-width:760px){.wrap{padding-left:32px;padding-right:32px}}',
        'Breitenangabe .wrap',
    )

    # 4) Fliesstext im Hinweiskasten auf lesbare Zeilenlaenge begrenzen
    m = re.search(r'\.privat p\{[^}]*\}', s)
    if m:
        s = s.replace(m.group(0), m.group(0) + '\n.privat p{max-width:80ch}', 1)

    # 5) Link zur ausfuehrlichen Datenschutzseite in den Fussbereich
    m = re.search(r'(<p class="fuss">.*?)\n(\s*)</p>', s, re.S)
    if m:
        s = s.replace(m.group(0), m.group(1) + DATENSCHUTZ_LINK, 1)
        s = ersetze_einmal(s, '.fuss b{color:var(--ink2)}',
                           '.fuss b{color:var(--ink2)}' + STIL_ZUSATZ,
                           'Stilangabe .fuss b')
    else:
        print("  Hinweis: kein Fussbereich gefunden, Datenschutz-Link ausgelassen.")

    # 6) Registrierung von Offline-Speicher und Teams-Anbindung
    s = ersetze_einmal(s, '</body>', BOOTSTRAP, 'schliessendes body-Tag')

    ziel = quelle.parent / 'index.html'
    ziel.write_text(s, encoding='utf-8')
    print(f"  {quelle.name}  ({ausgangsgroesse:,} Bytes)")
    print(f"  -> {ziel.name}  ({len(s):,} Bytes, +{len(s)-ausgangsgroesse:,})")


if __name__ == '__main__':
    main()
