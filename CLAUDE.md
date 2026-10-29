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
- Live API never tested yet: the claude.ai preview sandbox blocks outside fetches ("Failed to fetch").
  Must run in a real browser or on GitHub Pages.
- Geolocation needs HTTPS or localhost.

## Next steps
1. Push to GitHub, enable Pages (main / root): `gh repo create wat-trek-ik-aan --public --source . --push`,
   then `gh api -X POST repos/{owner}/wat-trek-ik-aan/pages -f "source[branch]=main" -f "source[path]=/"`.
2. First real run: check the API response matches the adapter's field mapping.
3. Calibrate thresholds against the owner's actual experience.

## Working style for this owner
Blunt, short answers, clear structure, no em dashes. Label anything unverified as [Unverified] / [Inference].
