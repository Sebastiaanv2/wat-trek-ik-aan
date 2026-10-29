# Wat trek ik aan?

Proof of concept: clothing advice (shorts, sweater, jacket, rain gear) based on the weather forecast.

- Single `index.html`, vanilla JS, no build step.
- Forecast: KNMI HARMONIE-AROME via [Open-Meteo](https://open-meteo.com/en/docs/knmi-api) (`models=knmi_seamless`), CC BY 4.0.
- Clothing thresholds live in `RULES` at the top of the engine script. Tune them there.

Run locally: open `index.html` in a browser, or `npx serve`.
