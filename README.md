I built this Satellite Orbit Explorer as part of Space Career Week 2026 (SCW26), a free online event by GNSS Academy. Thank you @GNSS Academy!

# scw26-satellite-orbit-explorer
Python 3 satellite orbit explorer Gnss Academy challenges SATELLITE ORBIT EXPLORER. Real satellite data. Real orbital mechanics. Your own Python project. Build evidence of what you can actually do.

# Satellite Orbit Explorer

*Built during Space Career Week 2026 (SCW26), a free online event by [GNSS Academy](https://www.gnssacademy.com/).*

A Python notebook (Google Colab) that loads real CelesTrak GP orbital data, propagates four satellites over 24 hours with a two-body Kepler model, compares their orbits and exports the results for Google Earth.

**Notebook:** [Colab link]

## What I built

The notebook has 8 stages:

1. Imports and configuration (24 h window, 5 s step, UTC, km, km/s)
2. Load CelesTrak GP data (OMM JSON fields) from the workshop snapshot
3. Convert GP fields to Kepler elements (a from Kepler's third law)
4. Define one common UTC time grid (17,281 points) and flag stale records
5. Propagate: Kepler's equation (Newton-Raphson), GMST (IAU-1982), ECI to Earth-fixed, latitude, longitude, altitude, speed (vis-viva)
6. Orbit summary table (period, perigee, apogee, eccentricity, inclination, speed range)
7. Plots with Matplotlib: ground tracks and speed time series
8. Export: two KML files, a text report and one output ZIP

### Satellites and orbit comparison

Data: CelesTrak GP snapshot downloaded on 10 November 2026 (see `data/data_manifest.json`).

| Satellite | NORAD ID | Regime | Period [h] | Perigee [km] | Apogee [km] | e | i [°] |
|---|---|---|---|---|---|---|---|
| PULSAR-0 | 64541 | LEO | 1.58 | 500.0 | 505.6 | 0.000408 | 97.5 |
| GSAT0225 (GALILEO 29) | 59598 | MEO | 14.08 | 23217.4 | 23226.8 | 0.000159 | 55.2 |
| SES-5 | 38652 | GEO | 23.93 | 35778.1 | 35794.2 | 0.000191 | 0.1 |
| QZS-2 (MICHIBIKI-2) | 42738 | IGSO | 23.94 | 32649.6 | 38934.8 | 0.074521 | 39.2 |

What the plots show:
- **PULSAR-0:** many passes per day, near-polar and retrograde (sun-synchronous type).
- **Galileo 29:** a wide ground-track wave, about 14 h per revolution.
- **SES-5:** a single point near 5° E, because its period equals one sidereal day and its inclination is about 0.
- **QZS-2:** an asymmetric figure-8, because it has the same period but a 39° inclination and an eccentric orbit. It moves slowest near its apogee in the north.
- **Speed:** eccentric orbits speed up near perigee (QZS-2: about 2.85 to 3.31 km/s). Nearly circular orbits keep an almost constant speed.

## Outputs (`outputs/`)

| File | Content |
|---|---|
| `ground_tracks.png` | Ground tracks of the 4 satellites on a world map |
| `velocity_timeseries.png` | Orbital speed vs UTC time |
| `orbit_tracks_ecef.kml` | Earth-fixed 3D orbits (altitude in metres) |
| `orbit_animation.kml` | Ground-track animation (`gx:MultiTrack`, one point per 60 s) |
| `orbit_animation_web.kml` | Static fallback for Google Earth Web |
| `orbit_report.txt` | Summary tables, source, window, model caveat |

### Opening the KML files
- **Google Earth Pro (desktop):** File > Open, select the KML files. For the animation, drag the time slider handle to 10 Nov 2026 and press Play.
- **Google Earth Web:** it does not support `gx:` extensions or time animation (`gx:MultiTrack`, `gx:Track` and `TimeSpan` were rejected). Use `orbit_animation_web.kml`, which is static (ground tracks and a marker every 3 h).

## Validation and assumptions

- **Model:** two-body Kepler orbit, spherical Earth, simple Greenwich rotation. GP records contain SGP4 mean elements, so this is an educational approximation, **not** precision orbit prediction and not for spacecraft operations.
- **Not modelled:** J2 and higher geopotential terms, Sun and Moon perturbations, atmospheric drag. A fair comparison with SGP4 would need TEME to ITRF/ECEF conversion.
- **Checks in the notebook:** GMST at J2000 is about 280.46°; Kepler residual below 1e-10; SES-5 longitude stays between 4° and 6° E without any fitted offset; Galileo 29 period between 14.0 and 14.2 h.
- **Epoch age:** each record is propagated from its own epoch. The QZS-2 record dates from 17 October 2026, about 23 days before the start of the window, and is flagged as stale in Stage 4. Its result should be read as a model demonstration, not as an accurate position.
- **Time grid:** 24 h from 10 Nov 2026 00:00 UTC, 5 s step, 17,281 points per satellite.

## What I learned

- How the six Keplerian elements describe an orbit, and how GP/OMM records store them (mean motion, mean anomaly at epoch).
- Solving Kepler's equation numerically and converting from an inertial frame to an Earth-fixed frame with sidereal time.
- Why orbit period alone explains the ground-track shape (GEO point, IGSO figure-8, LEO multiple passes).
- Why data quality matters: epoch age limits the accuracy of any propagation.
- KML compatibility: Google Earth Web and Pro do not support the same elements, so I exported a Pro animation and a web fallback.

## How to rerun

1. Open the notebook in Google Colab (standard CPU runtime, no GPU needed).
2. Download the workshop input ZIP (`orbit_workshop_inputs.zip`) from the [challenge page](https://www.gnssacademy.com/scw26/challenge3/#build). It contains `orbital_elements.json`, `data_manifest.json` and `world_map.txt`. A copy of the JSON files is in `data/`.
3. Run all cells (Runtime > Run all). When asked at Stage 2, drag the ZIP into the Colab Files panel (`/content`). The notebook stops with an error if inputs are missing and never uses mock data.
4. Download `satellite_orbit_outputs.zip` generated in Stage 8. Colab runtime files are temporary.

## Repository structure

```
README.md
notebook/Satellite_Orbit_Explorer.ipynb
data/            orbital_elements.json, data_manifest.json
outputs/         2 PNG, 2 KML (+ web KML), orbit_report.txt
```

## Credits

Built during Space Career Week 2026 (SCW26), a free online event by GNSS Academy. Orbital data: [CelesTrak](https://celestrak.org/) GP elements (OMM JSON).
