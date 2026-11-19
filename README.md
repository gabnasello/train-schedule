# Treinschema — commute trains, with your location

A single-file webapp that shows your regular commute train times, pulled live
from [iRail](https://docs.irail.be/) (the open data API behind
belgiantrain.be). No backend, no build step — it's one `index.html` file,
which is what makes it possible to host it for free on GitHub Pages and open
it from any device.

## What it does

Every time you open it, the app:

1. asks your browser for your location (if allowed),
2. works out which of your two commute stations you are closest to,
3. shows the trains leaving from there towards the other station for the
   **next 2 hours**, with live delays, platform and train number.

A **⇄ Keer om** button right under the route lets you flip the direction
whenever you want to see the opposite way. The label on the button shows
the direction you would switch to.

Under the list, **Toon meer · +1 u** loads the trains after the ones shown.
Each press adds another hour, as many times as you like. The list refreshes
every 60 seconds and keeps the extra hours you opened.

Your position never leaves your device: the browser only compares it with
the station coordinates (looked up via iRail's `/stations/` endpoint);
iRail only receives station names and times. Location needs a secure page,
so it works on the GitHub Pages `https://` link (not always when opening
the file straight from disk).

### When location is not available

If location is denied, switched off or times out, the direction falls back
to the time of day:

| Time | Direction |
|---|---|
| 01:00 – 11:59 | Station A → Station B (e.g. Mechelen → Leuven) |
| 12:00 – 00:59 | Station B → Station A (e.g. Leuven → Mechelen) |

A line under the route says when this fallback is being used. Delayed trains
that have not actually left yet stay in the list (scheduled time + delay
decides what has gone).

## Choosing your stations (no coding)

Anyone can use the published page for their own commute — no fork needed:

1. Tap **⚙ Stations** at the bottom of the page.
2. Type the two stations (suggestions come from iRail's station list; a
   typo gets a "did you mean…" message). **A** is where you leave in the
   morning, **B** where you go.
3. Tap **Opslaan**. The page title, directions and nearest-station logic all
   follow. **Standaard** goes back to the defaults.
4. Then add the page to your home screen (see below) — your choice is stored
   in the link, so the app icon opens with *your* stations.

You can also share or bookmark a link directly:
`https://YOUR-USERNAME.github.io/train-schedule/?a=Gent-Sint-Pieters&b=Brussel-Zuid`

Which stations are used, in order of priority: the link (`?a=…&b=…`) → the
choice saved on this device → the defaults in `CONFIG`. Distances come from
the station coordinates iRail provides, so nothing else needs changing when
the stations change.

## Defaults and other settings

Open `index.html` in any text editor and look at the very top of the file —
the block that starts with `const CONFIG = {`:

```js
const CONFIG = {
  stationA: 'Mechelen',          // eerste station van je traject
  stationB: 'Leuven',            // tweede station van je traject

  nowMinutes: 120,               // zoveel minuten vooruit tonen (vanaf nu)
  moreMinutes: 60,               // elke klik op "Toon meer" voegt zoveel minuten toe

  // Alleen gebruikt als de locatie niet beschikbaar is:
  firstDirectionFrom: '01:00',   // vanaf dit uur: richting A → B
  secondDirectionFrom: '12:00'   // vanaf dit uur tot firstDirectionFrom: richting B → A
};
```

`stationA`/`stationB` are the defaults people see before choosing their own
(forks: change these two lines to make your copy open on your commute).
`nowMinutes`, `moreMinutes` and the fallback hours (24-hour `UU:MM`) are the
same for everyone on that copy of the page.

## Publishing it on GitHub Pages

You only need a (free) GitHub account. No command line required — everything
below can be done in the browser.

1. **Create a repository.**
   Go to [github.com/new](https://github.com/new). Give it a name (e.g.
   `train-schedule`), leave it **Public** (GitHub Pages on a free account
   requires public repos), and click **Create repository**.

2. **Upload the file.**
   On the new repo's page, click **Add file → Upload files**, drag in
   `index.html`, and click **Commit changes**.
   > The file must be named exactly `index.html` — that's the name GitHub
   > Pages looks for by default.

3. **Turn on GitHub Pages.**
   Go to the repo's **Settings → Pages** (in the left sidebar). Under
   **Build and deployment → Source**, choose **Deploy from a branch**. Under
   **Branch**, choose `main` and folder `/ (root)`, then click **Save**.

4. **Get your link.**
   Reload the **Settings → Pages** screen after a minute — GitHub will show
   a banner: *"Your site is live at `https://YOUR-USERNAME.github.io/REPO-NAME/`"*.
   That's your permanent link. It usually takes 1–2 minutes after the first
   publish to go live.

5. **Open it from anywhere.**
   Bookmark that link, or turn it into a home-screen/desktop icon (see
   below). Every time you open it, it fetches live data — you never need to
   re-upload anything unless you change the settings.

## Updating it later

Whenever you want to change a station or a setting (see Settings above), edit the
file locally, then on the repo page click into `index.html` → the pencil
(✎) **Edit** icon → paste your changes → **Commit changes**. The live page
updates automatically within a minute or two, no re-publishing step needed.

## Files in this repo

| File | Purpose |
|---|---|
| `index.html` | The app itself. Must keep this exact name for GitHub Pages. |
| `icon.svg` | Source icon (train pictogram, matches the app's amber/black theme). Used as the browser tab favicon. |
| `icon-192.png`, `icon-512.png` | Rasterized versions for the home-screen icon (referenced by `manifest.json`). |
| `apple-touch-icon.png` | 180×180 icon iOS uses for "Add to Home Screen". |
| `favicon-32.png` | Fallback favicon for browsers that don't support SVG favicons. |
| `manifest.json` | Fallback web app manifest. When the page runs, it generates its own manifest with your chosen stations in the start link, so "install app" on Android keeps them. |

Upload **all of these files** to the repo (same folder, no subfolders) — `index.html` references them by their plain filename.

## Swapping in a different icon later

If you'd rather use the Wikimedia train icon (`BSicon_BAHN.svg`, CC0 / public
domain — free to use without restriction) instead of the custom one:

1. Download it from
   [commons.wikimedia.org/wiki/File:BSicon_BAHN.svg](https://commons.wikimedia.org/wiki/File:BSicon_BAHN.svg)
   (click "Original file" to get the raw `.svg`).
2. On your repo page, upload it and rename it to `icon.svg`, overwriting the
   existing one (GitHub will ask to confirm the replacement).
3. You'll also want new PNG versions for the home-screen icon, since phones
   need raster sizes, not just the SVG. Easiest way: open the SVG in any
   image editor (or an online SVG-to-PNG converter) and export it at
   512×512, 192×192, and 180×180, saving over `icon-512.png`, `icon-192.png`,
   and `apple-touch-icon.png` respectively.
4. Commit — the live site picks it up automatically, no other changes
   needed.


## Adding a home-screen / desktop icon

Choose your stations first (⚙ Stations), then add the page. The link in the
address bar now contains your stations, and that is what gets saved:

**iPhone (Safari):** share button → **Add to Home Screen**. (iOS keeps a
separate storage for home-screen apps, which is why the stations travel in
the link rather than only on the device.)

**Android (Chrome):** menu → **Install app** / **Add to Home screen**.

**Computer (Chrome/Edge):** menu → **Cast, save and share → Install page as
app** (or **More tools → Create shortcut**, tick "Open as window").

If you later change stations, add the page to the home screen again (and
remove the old icon), so the new link is the one saved.

## Data source

Live train data comes from the [iRail API](https://docs.irail.be/), a free,
open community project providing Belgian railway data. No API key is
needed. Requests are capped at 3/second per IP — this app makes a handful
of calls once a minute at most, well within that limit.

## Notes

- If iRail's API is briefly down, the app shows an error message rather
  than stale data.
- This is a personal tool, not affiliated with NMBS/SNCB or iRail.
