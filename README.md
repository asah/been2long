# Been2long

A personal catch-up page in a single editable [`index.html`](index.html). The Alex/Jamie stories are fictional. The HTML contains the data, styles, scripts, and favicon; there is no application backend or build step.

## Run locally

From this directory:

```sh
python3 -m http.server 3000 --bind 127.0.0.1
```

Open <http://localhost:3000>. `npm run dev` and `npm start` are shortcuts for the same Python command. Stop any old development server with Ctrl+C before starting this one.

GitHub Pages serves this same file at <https://asah.github.io/been2long/>. Publish the updated root `index.html` to update that site.

## Edit the content

Edit the `catchup-data` JSON block near the top of `index.html`, then refresh. It contains the author, default reader and date, categories, interests, overview groups, thread definitions, stories, media credits, and Google Maps configuration. There is no database to override your edits.

Keep valid JSON: double quotes, no trailing commas, and `\u003c` for a literal `<` inside the data block. The data uses two-space indentation so content changes are easy to diff. Edit the inline style and script blocks directly to change the UI.

Each story has a human-readable `location` and numeric `coordinates`, for example:

```json
"coordinates": {"lat": 45.467, "lng": -122.65}
```

Photos can carry their own coordinates, placing one story at several locations. Repeated visits at the same coordinates group together. All 20 demo stories have plausible fictional locations; these are illustrative guesses, not recovered photo GPS.

## Reading and maps

The page includes topic/date/category filters, a photo gallery, timeline and map explorers, and reading preferences. Closing a story opened from an explorer returns to the same explorer and position. The map fits local or global collections and groups nearby places as you zoom out. Groups of 1–4 stories display photo callouts immediately, with titles and connector lines to their locations. White borders, dark outlines, and shadows separate the callouts from the map. Clicking a photo opens its story without changing the camera. The layout avoids overlaps across groups; crowded groups retain a count bubble until there is room. Groups of 5+ zoom in when clicked. If a group remains at exactly one location after zooming, or the map reaches its maximum zoom, selecting it shows up to three previews plus a link to the full story list. Callouts reposition after panning or zooming.

Photos, fonts, embedded videos, and Google Maps load from their original hosts. The Google Maps JavaScript API loads only when the map opens; its key and map ID are in `googleMaps` in the JSON. The configured browser key's allowed referrers must include whichever host you use, including localhost for local map testing. Story reading and the timeline do not need Google Maps.

You can also send someone the raw HTML file. Opening it directly keeps the stories, photos, filters, and timeline; the interactive Google map requires a hosted page allowed by the key. There is no Studio, email delivery, reader account system, or server-side storage.
