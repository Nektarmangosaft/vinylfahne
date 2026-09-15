# vinylfahne
Vinylfinder by NMSAFT
# Vinyl Finder (MVP)

Durchsucht den Discogs-Marketplace nach Vinyls, reichert Ergebnisse mit BPM/Key an
(via GetSongBPM), zeigt Preis + Anzahl Angebote, ermöglicht Vorhören per YouTube-Link
(sofern beim Release vorhanden) und schlägt anhand von BPM + Genre passende Platten
zum Auflegen vor.

## Setup

1. **Node.js 18+** installieren (für natives `fetch`).
2. Abhängigkeiten installieren:
   ```bash
   cd vinyl-finder
   npm install
   ```
3. `.env.example` zu `.env` kopieren und Keys eintragen:
   ```bash
   cp .env.example .env
   ```
   - **DISCOGS_TOKEN**: https://www.discogs.com/settings/developers → "Generate new token"
   - **GETSONGBPM_API_KEY**: https://getsongbpm.com/api (kostenlos, aber Pflicht-Backlink
     zu getsongbpm.com laut deren Nutzungsbedingungen)
4. Starten:
   ```bash
   npm start
   ```
5. Im Browser öffnen: http://localhost:3000

## Was das MVP kann

- Suche über die Discogs-Datenbank (gefiltert auf Format „Vinyl“)
- Anzeige von Genre/Style, Erscheinungsjahr, niedrigstem Marktplatz-Preis und Anzahl Angebote
- BPM + Tonart pro Release (erster Track der Tracklist, über GetSongBPM nachgeschlagen)
- Filter/Sortierung nach Genre, BPM-Bereich, Preis-Bereich
- „Passende Vorschläge zum Auflegen“: einfache Heuristik auf Basis von BPM-Nähe (±6 BPM)
  und Genre-Überschneidung innerhalb der aktuellen Suchergebnisse
- YouTube-Vorhören, sofern Discogs für das Release ein Video verlinkt hat

## Wichtige Einschränkungen (bewusst so gebaut, nicht vergessen!)

- **Versandkosten**: Die Discogs-API liefert nur den *niedrigsten Angebotspreis* pro
  Release, keine verkäuferspezifischen Versandkosten. Die sind erst im eigentlichen
  Checkout-Flow auf discogs.com sichtbar. Der „Auf Discogs“-Link führt direkt zur
  Release-Seite, wo die realen Versandkosten pro Angebot stehen.
- **BPM-Abdeckung**: GetSongBPM ist eine namensbasierte Songdatenbank – nicht jeder
  (v.a. obskure/instrumentale) Track wird gefunden. Ergebnisse ohne Treffer zeigen
  einfach kein BPM-Tag.
- **Decks.de / HHV & Co.**: Diese Shops haben (soweit bekannt) keine offizielle,
  öffentliche API. Eine Integration wäre nur per Scraping möglich – das verstößt
  potenziell gegen die jeweiligen AGB und ist technisch instabil (bricht bei jedem
  Layout-Update). Deshalb bewusst **nicht** in diesem MVP enthalten. Falls einer der
  Shops doch eine Partner-/Affiliate-API anbietet, lässt sich das als weiterer
  `lib/`-Client relativ einfach nachrüsten.
- **Rate Limits**: Discogs erlaubt ~60 authentifizierte Requests/Minute, GetSongBPM
  ~3000/Stunde. Bei 20 Ergebnissen pro Suche werden bis zu 40 Discogs-Calls ausgelöst
  (Stats + Release-Detail pro Treffer) – für Testzwecke ok, für viel Traffic müsste
  man zusätzlich cachen/drosseln.
- **Suggestion-Algorithmus**: Aktuell reine BPM-Nähe + Genre-Match, kein echtes
  harmonisches Mixing (Camelot Wheel). Sobald zuverlässige Tonart-Daten vorliegen,
  wäre das der nächste sinnvolle Ausbau in `lib/suggestions.js`.

## Nächste sinnvolle Schritte

- Camelot-Wheel-Logik für harmonisches Mixing statt reiner BPM-Nähe
- Persistenter Cache/DB (aktuell nur einfache JSON-Datei in `.cache/`) für mehr Requests
- Eigene Sammlung (statt nur Marketplace) über die Discogs-Collection-Endpoints einbinden
- Paginierung der Suchergebnisse

- CREDS/APIS:
-    BPM data provided by [GetSongBPM](https://getsongbpm.com)
