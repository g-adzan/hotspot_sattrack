# SatTrack · Kalbar

A single-file, browser-based pass predictor for the polar-orbiting Earth-observation
satellites most relevant to fire and land monitoring over West Kalimantan
(Kalimantan Barat), Indonesia. It shows live ground tracks, sensor swaths, and the
next overpass for a fixed ground station.

No build step, no server, no dependencies to install. Everything runs in the browser
from one HTML file.

## Live demo

`[https://USERNAME.github.io/REPO-NAME/](https://g-adzan.github.io/hotspot_sattrack/)`

## Satellites tracked

| Satellite  | NORAD ID | Sensor | Swath  |
|------------|----------|--------|--------|
| NOAA-20    | 43013    | VIIRS  | 3060 km|
| Suomi NPP  | 37849    | VIIRS  | 3060 km|
| Aqua       | 27424    | MODIS  | 2330 km|
| Terra      | 25994    | MODIS  | 2330 km|

These four carry the VIIRS and MODIS instruments behind the NASA FIRMS active-fire
products, which makes the tool useful for anticipating when a fire-detection overpass
will occur for a given area.

## Features

- Real-time position, azimuth, elevation, and slant range for each satellite.
- Next-pass prediction: AOS, time of maximum elevation, LOS, duration, and closest
  approach. Horizon crossings are refined by bisection to sub-second precision.
- Equirectangular world map with coastlines, ground tracks, and per-sensor swath
  footprints.
- Dual UTC / WIB (UTC+7) clock.
- Editable ground-station location at runtime via the **SET** button. It accepts a
  decimal pair (`lat, lon`) or a Google Maps URL that already contains coordinates
  (`@lat,lon`, `?q=lat,lon`, or `!3d...!4d...`). The change is held in memory only and
  is never stored or transmitted.

## How it works

Orbits are propagated with the SGP4 model via [satellite.js](https://github.com/shashwatak/satellite-js).
Orbital elements (TLEs) are fetched at load time from
[CelesTrak](https://celestrak.org/) using the GP query API
(`/NORAD/elements/gp.php?CATNR=...`), then refreshed every two hours. Az/El and range
are computed relative to the configured ground station; pass detection scans forward in
10-second steps and then refines the horizon crossings.

## Caveats

- **Live TLE needs an external fetch.** The tracker calls CelesTrak from the browser.
  This works when the page is opened as a normal website (e.g. GitHub Pages) or locally,
  but may be blocked by sandbox security policies in some embedded preview contexts.
- **Stale fallback.** If the live fetch fails, the app falls back to TLEs embedded at
  build time (epoch mid-2025). SGP4 error grows roughly 1-3 km per day from epoch, so
  fallback passes are unreliable. A status line at the bottom of the sidebar indicates
  when fallback data is in use.
- **Short Maps links are not resolvable in the browser.** Links such as
  `maps.app.goo.gl/...` are redirects that cannot be expanded client-side. Open the link
  in Google Maps first, then paste the resulting coordinates.

## Configuration

The default ground station and the tracked-satellite list are defined near the top of
the inline `<script>` block in `index.html`:

```js
let OBS_LAT  = 0.817333;   // observer latitude  (deg, +N)
let OBS_LON  = 110.348833; // observer longitude (deg, +E)
let OBS_ALT  = 0.030;      // observer altitude  (km)

const SATELLITES = [ /* id, name, NORAD, colour, sensor, swathKm */ ];
```

Edit those values and re-upload to change the baked-in defaults.

## Deploying on GitHub Pages

1. Create a public repository.
2. Upload `index.html` (and this `README.md`).
3. Go to **Settings → Pages**, set the source to **Deploy from a branch**, branch
   `main`, folder `/ (root)`, then **Save**.
4. Wait a few minutes, then open the published URL.

## Credits

- Orbit propagation: satellite.js (MIT).
- Orbital data: CelesTrak GP / TLE service.
- Coastline geometry: Natural Earth (public domain), via a simplified 110 m dataset.

## License

You may add your own license (for example MIT). Note that the third-party data and
libraries above remain under their respective terms.
