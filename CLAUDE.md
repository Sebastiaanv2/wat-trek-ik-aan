# Wat trek ik aan? (weather outfit PoC)

## What this is
Single-file HTML PoC that recommends clothing (shorts / long pants, t-shirt / sweater,
no / summer / winter jacket, rain gear, extras) from the weather forecast.
Hard constraints for this phase: plain HTML + vanilla JS, no frameworks, no build step, no mobile app yet.

## Data source
- Open-Meteo forecast API with `models=knmi_seamless` (KNMI HARMONIE-AROME, 2 km over NL,
  hourly, ~2.5 days, ECMWF after that). No API key. Free tier is non-commercial, CC BY 4.0,
  attribution is in the page footer.
- Geocoding: Open-Meteo geocoding API.
- Chosen over the KNMI Data Platform directly because KNMI's Open Data API is file-based
  (NetCDF/HDF5) and needs a key; KNMI EDR serves observations, not forecasts.
- All provider code sits in the `weatherSource` adapter object, so swapping providers is one place.

## Code layout (index.html)
1. `<script id="engine">`: `RULES`, `summarise()`, `recommend()`. Pure functions, no DOM, Node-testable
   (`module.exports` at the bottom).
2. `weatherSource` adapter + `getJson()` helper (turns "Failed to fetch" into a readable error).
3. UI script: location (geolocation or search), day + from/to hour window, "I run cold/warm" offset
   (stored in localStorage), outfit cards with reasons, hourly table.

## Clothing thresholds (feels-like / apparent temperature, °C)
From Dutch consumer guides; no official standard exists:
- shorts from ~20 avg (and coldest hour >= 16, own choice)
- 15-20: thin sweater, no jacket
- 10-15: summer / tussen jacket
- below 10: winter jacket
Marked `[own choice]` in `RULES` = untuned guesses: rain 40% / 0.5 mm, umbrella useless above 45 km/h gusts,
scarf < 5, gloves + hat < 2, "dress in layers" at an 8° swing.

## Status
- Engine tested with 6 synthetic scenarios, all sensible.
- Live API tested 2026-10-29 on GitHub Pages (Utrecht): geocoding + forecast work, adapter field mapping
  matches, outfit cards and hourly table render. All 7 hourly fields filled, 0 nulls in 48 h.
- Geolocation needs HTTPS or localhost.

## Known issues (code review 2026-10-29, not fixed yet)
1. RESOLVED: `precipitation_probability` is filled for `knmi_seamless` (values 0-6% in test run, 0 nulls).
2. Hourly table crashes if the API returns null precip: `h.precip.toFixed(1)` in `render()`.
   Null temp/gust silently become 0 in `Math.round`/`Math.max`. Not seen in live test (0 nulls), still unguarded.
3. Default "from" hour uses browser local time (`new Date().getHours()`), filter uses Amsterdam time.
   Only wrong outside the NL timezone.
4. From > To gives an empty window. Error shown, but the UI doesn't prevent it.
5. Page is English (`lang="en"`, "What To Wear"), repo/README use the Dutch name. Owner to decide.

## Next steps
1. DONE 2026-10-29: repo https://github.com/Sebastiaanv2/wat-trek-ik-aan (public),
   Pages (main / root) at https://sebastiaanv2.github.io/wat-trek-ik-aan/
2. DONE 2026-10-29: first real run, field mapping matches.
3. Calibrate thresholds against the owner's actual experience.

## Working style for this owner
Blunt, short answers, clear structure, no em dashes. Label anything unverified as [Unverified] / [Inference].
