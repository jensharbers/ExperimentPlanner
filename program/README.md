# Experiment Designer

An offline-capable mobile app (Python + [Flet](https://flet.dev)) that **creates randomised experimental designs, lays them out on the field and archives them reproducibly**. It is a remake of the design functions of the R packages [agricolae](https://CRAN.R-project.org/package=agricolae) and [FielDHub](https://CRAN.R-project.org/package=FielDHub) that runs on an Android phone, without a network connection.

**New here? Read the [vignette](docs/vignette.md)**: one worked example from design to a georeferenced, reproducible layout.

![Field sketch](docs/vignette_sketch.png)

<!-- TODO: add screenshots, e.g. docs/screenshots/design.png, sketch.png, layout.png -->
<!-- ![Design](docs/screenshots/design.png) ![Field sketch](docs/screenshots/sketch.png) ![Field layout](docs/screenshots/layout.png) -->

## Features

- **21 design types**, generated on the device with a fixed seed (see table below).
- **Field sketch** of the plan, coloured by treatment, as SVG, PNG or JPEG (colour-blind-friendly viridis family scales).
- **Custom symbols** for experiments without a field (e.g. in medicine): give every treatment an emoji (any emoji of your keyboard) or upload your own icon. The symbols appear in the sketch, the legend and the SVG/PNG/JPEG exports.
- **Field layout on a map** (optional step): the plots of the plan are placed on a row-column grid with plot size, gaps, paths and protective strips, anchored to a GPS position and a compass heading on bundled OpenStreetMap tiles. Rotate and move the grid, save it in the plan, export GeoJSON and KML. Experiments without a field (e.g. in medicine) can skip this step.
- **Reproducibility:** a small parameter file or QR code regenerates the identical plan from its seed. A canonical compact JSON with SHA-256 hash supports time-stamped archiving. The significance level α can be fixed, with a timestamp, before data collection (guard against p-hacking).
- **Exports:** plan as JSON or CSV, parameters as JSON or QR code, compact JSON with hash, field sketch as image, field layout as GeoJSON/KML.
- **Optional location and heading** of the experiment (GPS, compass or manual input), stored with the plan.
- **Starter analysis scripts** (Python and R) generated from the plan. *Experimental, not part of the core claims, please check them before use.*
- **Accessibility** (WCAG 2.2 AA): high-contrast themes, text scaling, a black-and-yellow glove mode with large buttons, screen-reader labels.
- **English and German** interface. English is the default, German is used when the device language is German; switch in the settings.

## Designs

| Group | Designs |
|---|---|
| Basic | Completely randomized (CRD), randomized complete block (RCBD), Latin square (LSD), Graeco-Latin square, Youden square |
| Factorial | Factorial (A×B…), split-plot, split-split-plot, strip-plot |
| Incomplete block | BIB, alpha design, lattice, rectangular lattice, cyclic design, resolvable IBD, row-column design |
| Augmented / early generation | Augmented block, diagonal arrangement, optimized check arrangement, partially replicated (p-rep), sparse testing |

The designs follow the design types of agricolae and FielDHub. The algorithms are our own Python implementations and are **not identical** to the R functions.

## Installation

Requires Python 3.10 or newer.

```bash
pip install "flet>=0.85.1" flet-camera flet-geolocator flet-map "segno==1.6.6" pillow
flet run main.py          # run on the desktop
flet build apk            # build the Android app
```

Camera (QR scanner) and location permissions are declared in `pyproject.toml`. Background location is not requested.

### Offline map tiles

The field layout uses only bundled OpenStreetMap tiles from the folder `tiles/` next to `main.py`. Outside the bundled area the map background stays empty (the grid is still drawn correctly).

```bash
python fetch_tiles.py you@example.org
```

Edit `CENTER`, `RADIUS_M` and `ZOOMS` in `fetch_tiles.py` first. Please respect the [OSM tile usage policy](https://operations.osmfoundation.org/policies/tiles/): keep the area small and use your own contact address. The contact address of the app itself can be set in the app settings.

## Data formats

- **Plan JSON:** design, params, seed, plan table, info, and optionally `site`, `layout`, `analysis`, `responses`.
- **Parameter JSON / QR code:** design, params and seed only. Enough to regenerate the plan exactly.
- **CSV:** one row per plot. With a field layout, the columns `field_row` and `field_col` are added, and with a location also `plot_lat` and `plot_lon`.
- **Field layout:** key `layout` in the plan (rows, columns, dimensions, paths, protective strips, anchor, GPS position, heading). Row 1 lies in front, column 1 on the left. Plots of the plan are placed in plan order, row by row from the front left.

## Project structure

| File | Purpose |
|---|---|
| `main.py`, `app.py` | Entry point (language detection) and the app itself |
| `i18n.py`, `translations_en.json` | Language handling (English default, German) |
| `agricolae_py.py`, `fielddhub_py.py` | Design generators, plan building, exports |
| `fieldlayout.py`, `layout_ui.py` | Field layout geometry and map screen |
| `tileserver.py`, `fetch_tiles.py` | Local offline tile server, tile download tool (PC) |
| `siteinfo.py`, `prereg.py` | Location/heading and pre-specified α |
| `qrtools.py`, `sketch_export.py`, `symbols.py`, `palettes.py` | QR code, field sketch export, custom symbols, colour scales |
| `a11y.py`, `about.py` | Accessibility settings and themes, about dialog |
| `factor_editor.py`, `suggest.py` | Factor input and design suggestions |
| `analysis_scripts.py`, `analysis_scripts_r.py` | Generated starter analysis scripts (experimental) |

## Status and limitations

- [Update before publishing: test status, validation against agricolae/FielDHub, field testing on devices.]
- The field layout is limited to 400 plots, free drawing of the grid up to 12 × 12 cells. By default, from 37 plots the map shows only the outline and grid lines; the limit can be changed in the app settings (high values can make the app slow or crash it).
- Declination for the compass is fixed to 4° (Germany).
- The generated analysis scripts contain German comments and are experimental.

## Citation

[Add citation or DOI once available.]

## Licence

MIT licence, Copyright (c) 2026 Harbers Agrarinformatik. See `LICENSE`.

Map data © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright) (ODbL). Third-party software: Flet and flet-map/flet-geolocator (Apache 2.0), Flutter (BSD-3-Clause), segno (BSD), Pillow (HPND).

## AI assistance

The app was developed with the support of AI: Claude (Anthropic) was used as the coding AI, ChatGPT (OpenAI) for the design of the interface (UX).

## Acknowledgements

Design types and help texts follow the R packages agricolae (Felipe de Mendiburu) and FielDHub (Murillo, Gezan et al.). Colour scales come from matplotlib (viridis family), seaborn (mako, rocket) and Google (turbo).
