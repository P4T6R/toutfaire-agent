---
name: chargement-gros-dataset-qgis
description: "Pattern for loading large vector datasets (hundreds of thousands+ features) into QGIS without freezing the app: indexed GeoPackage conversion + scale-dependent visibility. Use whenever a dataset is too large to load and render smoothly as-is (raw CSV, huge Shapefile, unindexed GPKG)."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, performance, geopackage, spatial-index, large-dataset]
    related_skills: [qgis-pyqgis-mastery, extraire-batiments-open-buildings]

---

# Charger un gros dataset sans figer QGIS

General-purpose pattern, not specific to any one dataset: any vector layer with hundreds of thousands of features (or a raw CSV/huge Shapefile) will freeze QGIS's UI or take minutes to render/pan if loaded naively. Fix with indexed GeoPackage + scale-based rendering, not by just "waiting it out" or downsampling data you actually need.

## When to use

QGIS becomes unresponsive, panning/zooming is laggy, or the initial load takes an unreasonable amount of time, for a vector layer in the 100k-10M+ feature range.

## Why this happens

- Raw CSV/Shapefile without a spatial index forces QGIS to scan every feature for any spatial query (pan, zoom, selection) — O(n) per redraw instead of O(log n).
- Rendering every feature regardless of zoom level wastes GPU/CPU when zoomed out (thousands of tiny buildings collapse to sub-pixel noise anyway at country/region zoom).

## Implementation

**1. Convert to indexed GeoPackage** (GeoPackage has a built-in R-tree spatial index; GeoPandas/Fiona/ogr2ogr create it automatically on write, but verify):

```bash
# From Shapefile/CSV/anything GDAL reads:
ogr2ogr -f GPKG output.gpkg input.shp -nlt PROMOTE_TO_MULTI -makevalid

# Verify the spatial index exists:
ogrinfo -al -so output.gpkg | grep -i "rtree\|spatial index"
```

If converting from a GeoDataFrame instead:

```python
gdf.to_file("output.gpkg", driver="GPKG", layer="my_layer")
# GeoPandas/Fiona create the gpkg_spatial_ref_sys + rtree index automatically;
# for very large writes, disable the index during write and build it after
# for a meaningful speedup:
gdf.to_file("output.gpkg", driver="GPKG", layer="my_layer",
            SPATIAL_INDEX="NO")
# then, via ogr:
# ogrinfo output.gpkg -sql "SELECT CreateSpatialIndex('my_layer', 'geom')"
```

**2. Set scale-dependent visibility** so QGIS doesn't try to render 500k features at country zoom:

```python
from qgis.core import QgsMapLayer

layer.setScaleBasedVisibility(True)
# Only render below this scale denominator (i.e. only when zoomed in enough
# that individual buildings are visually meaningful) -- tune per dataset,
# this is not a universal number.
layer.setMinimumScale(1)       # no upper visible-scale limit
layer.setMaximumScale(50000)   # hidden when zoomed out past 1:50,000
```

**3. For symbology-heavy layers, simplify geometry for rendering only** (not the underlying data) via layer properties → Rendering → "Simplify geometry" — this reduces vertex count for on-screen drawing at low zoom without touching the stored geometry, so downstream analysis still uses full-precision data.

## Limites

- Scale-based visibility hides data at low zoom — fine for interactive exploration, but don't rely on it for anything that needs "all features visible" (e.g. exporting a print layout at a fixed scale where the threshold matters — check the export scale against `maximumScale` first).
- GeoPackage write performance for very large single-transaction writes (multi-million rows) benefits from chunked writes with periodic commits rather than one giant `to_file()` call — if a single write is taking unreasonably long, batch it.
- This pattern addresses *rendering/interaction* performance, not analysis performance — a spatial join or buffer operation on the full dataset is still O(n) or worse regardless of the GeoPackage index; index-aware analysis (e.g. `gpd.sjoin` which does use the spatial index under the hood) still benefits, but a naive nested-loop custom join will not.
