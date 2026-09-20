---
name: qgis-pyqgis-mastery
description: "PyQGIS scripting, headless QGIS processing, and QGIS plugin development. Use when asked to automate QGIS, write a QGIS plugin, or run geoprocessing without the QGIS GUI."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [qgis, pyqgis, gis, geoprocessing, plugin-development, gdal]
    related_skills: [extraire-batiments-open-buildings, jointure-fiscale-spatiale, recalage-vecteur-imagerie, nettoyage-polygones, chargement-gros-dataset-qgis]

---

# QGIS / PyQGIS Mastery

Reference skill for automating QGIS: running geoprocessing headlessly (no GUI), scripting inside the QGIS Python console, and building QGIS plugins. This is the foundation the other `gis/*` skills in this bundle build on.

## When to use

- Writing a script that needs to run QGIS processing algorithms outside the GUI (server, cron job, CI)
- Building or debugging a QGIS plugin
- Any task involving `qgis.core`, `qgis.processing`, `osgeo.gdal/ogr`, or GeoPackage/Shapefile manipulation

## Three ways to run PyQGIS code

1. **QGIS Python console** (interactive, inside the GUI) — fastest for exploration, `iface` is available.
2. **Headless via `QgsApplication`** — for scripts, cron jobs, servers with no display:

```python
from qgis.core import QgsApplication
import sys

QgsApplication.setPrefixPath("/usr", True)  # adjust to your QGIS install prefix
qgs = QgsApplication([], False)
qgs.initQgis()

# ... processing code here ...

qgs.exitQgis()
```

   On Linux, `QGIS_PREFIX_PATH` env var can replace the hardcoded prefix. Import `processing` and initialize the Processing framework explicitly if you need `processing.run(...)`:

```python
sys.path.append('/usr/share/qgis/python/plugins')  # where the core Processing plugin lives
from processing.core.Processing import Processing
Processing.initialize()
import processing
```

3. **Plugin** — code runs inside a live QGIS session, `iface` and the canvas are available; needed only when the user interacts with QGIS directly (button, panel, menu entry).

## Running a processing algorithm

```python
result = processing.run("native:orthogonalize", {
    'INPUT': input_layer,
    'MAX_ANGLE_DELTA': 15,
    'MAX_ITERATIONS': 1000,
    'OUTPUT': 'memory:'
})
output_layer = result['OUTPUT']
```

Find algorithm IDs with `processing.algorithmHelp("native:orthogonalize")` or `qgis.core.QgsApplication.processingRegistry().algorithms()`. Prefer `native:*` (C++ core, fast, no external deps) over `qgis:*`/`grass:*` unless the task specifically needs GRASS/SAGA.

## Plugin skeleton (minimum viable)

```
my_plugin/
  __init__.py       # classFactory(iface) -> returns the plugin class
  metadata.txt       # name, qgisMinimumVersion, description, version, author
  my_plugin.py        # QgsProcessingProvider or QAction-based entry point
  resources.qrc       # icons (compile with pyrcc5 if used)
```

`metadata.txt` minimum fields: `name`, `qgisMinimumVersion`, `description`, `version`, `author`, `email`. Register a custom Processing provider (so algorithms show up in the Processing Toolbox, not just a menu button) by subclassing `QgsProcessingProvider` and `QgsProcessingAlgorithm`, and calling `QgsApplication.processingRegistry().addProvider(...)` in the plugin's `initGui()`.

## Common pitfalls

- Headless scripts freeze if `Processing.initialize()` is skipped before calling `processing.run()` — the algorithm registry is empty and you get a cryptic `QgsProcessingException: Error retrieving algorithm`.
- Always call `qgs.exitQgis()` at the end of a headless script — leaving it out leaks the QGIS instance and can corrupt lock files on repeated runs (cron jobs are especially prone to this).
- `iface` is `None` outside the GUI — any plugin code touching `iface.mapCanvas()` etc. will crash headless. Guard with `if iface is not None`.
- GDAL/OGR version mismatches between the system GDAL and the one QGIS ships with cause silent driver failures (e.g. GPKG writes succeeding but producing an unreadable file). Check `from osgeo import gdal; print(gdal.__version__)` matches `qgis --version`'s bundled GDAL.
