# martian-interplanetary-survival-guide
# Ares Sozon 


**A survival-first map of Mars for the astronauts who will walk on it.**

Built for the NASA Space Apps Challenge: *Interplanetary Survival Guide, Martian Map*.

**Live demo:** https://767f5591.ares-sozon.pages.dev/

> Prototype for the hackathon. It is not mission advice. Values marked with `*` in the app are derived estimates, not measurements.

---

## What it does

Ares Sozon is an interactive Mars surface-planning tool. You rotate a 3D globe of Mars, pick any spot, and get an answer about how survivable it is. You can then drop to ground level, plan a route across the planet, or find the nearest reachable shelter if an astronaut is stranded.

| Action | Result |
|---|---|
| **Click** a spot on the globe | A survival scorecard: terrain slope, radiation shielding and sunlight, with any missing data stated |
| **Double-click** a spot | Zooms in and opens a ground-level view of that location, drawn from NASA MOLA elevation data |
| **Explorer** button | A flat world map with a directory of named places |
| **Route planner** | Pick a start and a goal anywhere on Mars. It finds the safest path, shows alternative routes with their risks, and warns about steep slopes and hazards |
| **Safety / Resources / Hazards / Imagery** | Four layer modes that recolour the whole planet |
| **Stranded** | Finds shelters within an oxygen limit. Walking slows on slopes, so the closest shelter is not always the one you can reach (default: 7.5 h of oxygen at 3 km/h) |
| **Missions** | Simulated astronaut briefings on research frontiers, explored sites and global systems |

### Controls (globe)

Drag to rotate, scroll to zoom, click for the scorecard, double-click for ground view, `R` to reset.

---

## Data: what is real and what is estimated

### Real NASA / USGS data

| Data | Used for | Source |
|---|---|---|
| Mars MGS MOLA DEM, 463 m | Elevation, slope, ground view | https://astrogeology.usgs.gov/search/map/mars_mgs_mola_dem_463m |
| Mars Viking Global Color Mosaic, 925 m | Surface colour and imagery | https://astrogeology.usgs.gov/search/map/mars_viking_global_color_mosaic_925m |
| NASA Mars Trek tiles | Imagery fallback in the Explorer | https://trek.nasa.gov/mars/ |
| Radiation raster (5° × 5° patch over Arcadia) | The only real radiation data in the app | Provided with the challenge resources |

### Derived estimates (not measurements)

| Layer | How it is estimated |
|---|---|
| Water ice | From latitude |
| Sunlight | From latitude |
| Dust-storm risk | Rough regional climatology |
| Radiation shielding (outside Arcadia) | From elevation |

The scorecard weights are our own assumptions: terrain 30%, radiation 30%, sunlight 20%, water 20%. Water is left out of the score when there is no data. The Safety layer uses ice 0.5, sunlight 0.3 and slope 0.2.

---

## Run it locally (offline)

The app loads its data with `fetch`, so it must be served over HTTP. Opening the `.html` file directly will not work.

You need Python 3.

1. Put the project folder in one place. It should contain `index.html` (the globe), `mars_explorer.html`, and the data folders `web/world/` (terrain tiles) and `layers/` (survival layers).
2. In that folder, start a local server:

   ```
   python -m http.server 8000
   ```

3. Open **http://localhost:8000/** in Chrome.

On Windows, `run.bat` does both steps: it opens the browser and starts the server.

The Explorer can also be opened on its own at a location, for example `http://localhost:8000/mars_explorer.html?lon=77&lat=18`.

---

## Build the data

The terrain tiles and layers are too large for this repository, so they are generated from the NASA / USGS source files with the Python scripts below.

```
pip install numpy pillow rasterio
```

| Script | What it makes |
|---|---|
| `build_layers.py` | The survival layers in `layers/` (elevation, slope, shield, insolation) |
| `build_routegrid.py` | `route_grid.bin`, the global grid the route planner searches |
| `build_texture.py` | A shaded colour texture for the globe from a colour image and the MOLA elevation |
| `make_viking_texture.py` | An 8192 × 4096 texture from the USGS Viking colour mosaic |
| `shrink_tiles.py` | A lighter copy of the terrain tiles for free hosting (for example `python shrink_tiles.py "deploy_lite\web\world" 4`) |

Run `shrink_tiles.py` only on a copy, because it rewrites the tiles in place.

---

## Built with

- **App:** plain HTML, JavaScript and canvas. No frameworks and no external libraries.
- **Data pipeline:** Python with NumPy, Pillow and rasterio.
- **Hosting:** Cloudflare Pages (deployed with Wrangler).
- **Developed on:** a Windows PC.
- **AI assistance:** the code was written with help from Claude, an AI assistant made by Anthropic.

---

## Limitations

- The hosted demo uses a lower-resolution terrain set (about 1.85 km per pixel) so it fits on free hosting. The full 463 m terrain runs offline.
- The route planner searches a coarse global grid (about 7 km cells), so short trips are approximate.
- Slopes come from 463 m data, so individual rocks and boulders are not visible.
- Ice, sunlight, dust risk and most of the radiation are estimates. The only real radiation data covers a small patch over Arcadia.
- The ground view is a simulated render from elevation data, not real rover photography.

---

## Credits

Elevation and imagery are from NASA and USGS (links above). Mars place names and landing-site locations are public NASA / IAU information.
