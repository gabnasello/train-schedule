# Treinschema — Mechelen ⇄ Leuven

A single-file webapp that shows your regular commute train times, pulled live
from [iRail](https://docs.irail.be/) (the open data API behind
belgiantrain.be). No backend, no build step — it's one `index.html` file,
which is what makes it possible to host it for free on GitHub Pages and open
it from any device.

## What it does

- Shows departures for two time windows you configure: a morning route and
  an afternoon route.
- Automatically picks the right window depending on when you open it
  (before/after noon), with a toggle to switch manually.
- A **Nu** tab uses your location to show the next trains from the nearest
  commute station (see below).
- Refreshes every 60 seconds and shows live delays, platform, and train
  number.
- Everything you'd ever want to change lives in one clearly marked block at
  the top of `index.html`.

## "Nu" tab: nearest station, next trains

A third tab, **Nu**, shows the trains leaving in the next 2 hours from
whichever of your commute stations you are closest to:

1. Tap **Nu** — the browser asks for permission to use your location.
2. The app looks up the coordinates of your configured stations (via iRail's
   `/stations/` endpoint), picks the closest one, and shows the route that
   departs from it (e.g. near Mechelen → Mechelen → Leuven).
3. If location is denied or unavailable, it falls back to the morning/afternoon
   route by time of day. The **⇄ keer om** button flips the direction manually.

Your position never leaves your device: the browser only compares it with the
station coordinates locally; iRail only receives station names and times.
Location needs a secure page, so it works on the GitHub Pages `https://` link
(not always when opening the file straight from disk).

Trains that are delayed but have not actually left yet stay "upcoming"
(scheduled time + delay is used to decide what is past).

### Which tab opens first

With the default `startTab: 'auto'` the app opens on the most useful tab for
the time of day:

| When you open it | Tab shown |
|---|---|
| From midnight until the end of the morning window (e.g. 09:00) | Ochtend |
| After the morning window, before 12:00 | **Nu** |
| From 12:00 until the end of the afternoon window (e.g. 17:30) | Namiddag |
| After the afternoon window, until midnight | **Nu** |

In **Nu**, if location is denied or switched off, the direction falls back to
the likely one: inbound (morning route) before 12:00, outbound (afternoon
route) from 12:00 on. **⇄ keer om** always lets you flip it.

Settings in the `CONFIG` block: `nowMinutes` (default `120`),
`switchTime` (default `'12:00'`, the morning/afternoon switch) and `startTab`
(`'auto'`, `'now'`, `'morning'` or `'afternoon'` to always open on one tab).

## "Toon meer" button

Under the list on every tab, **Toon meer · +1 u** loads the trains after
the ones shown. Each press extends the window by another hour, as many times
as you like; the route line above the list shows the new end time. Switching
tab (or flipping direction in **Nu**) goes back to the normal window. The step
is the `moreMinutes` setting in `CONFIG` (default `60`).

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
   re-upload anything unless you change the config.

## Updating it later

Whenever you want to change a station or time window (see below), edit the
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
| `manifest.json` | Web app manifest — tells Android/Chrome what icon and name to use when added to the home screen. |

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



Open `index.html` in any text editor and look at the very top of the file —
you'll see a block that starts with `const CONFIG = {`. It looks like this:

```js
const CONFIG = {
  morning: {
    from:  'Mechelen',   // vertrekstation 's ochtends
    to:    'Leuven',     // aankomststation 's ochtends
    start: '07:20',      // begin van het tijdvenster
    end:   '09:00'       // einde van het tijdvenster
  },
  afternoon: {
    from:  'Leuven',     // vertrekstation 's namiddags
    to:    'Mechelen',   // aankomststation 's namiddags
    start: '15:00',      // begin van het tijdvenster
    end:   '17:30'       // einde van het tijdvenster
  }
};
```

Change the station names or the `start`/`end` times (24-hour `UU:MM`
format) and save — everything else in the app (titles, button labels, the
data it fetches) updates automatically from these six values. Nothing else
in the file needs to change.

## Adding a home-screen / desktop icon

Once the GitHub Pages link works in your browser:

**Phone:** open the link, then use your browser's share/menu button →
**Add to Home Screen**.

**Computer (Chrome/Edge):** open the link, then use the browser menu →
**More tools / Apps → Create shortcut / Install this site as an app**, and
check "Open as window" for an icon that opens without browser chrome.

## Data source

Live train data comes from the [iRail API](https://docs.irail.be/), a free,
open community project providing Belgian railway data. No API key is
needed. Requests are capped at 3/second per IP — this app makes a handful
of calls once a minute at most, well within that limit.

## Notes

- If iRail's API is briefly down, the app shows an error message rather
  than stale data.
- This is a personal tool, not affiliated with NMBS/SNCB or iRail.
