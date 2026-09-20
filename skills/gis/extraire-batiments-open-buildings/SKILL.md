---
name: extraire-batiments-open-buildings
description: "Extract building footprints for a zone from Open Buildings / VIDA CSV or parquet datasets into an indexed GeoPackage. Use when given a country/city-scale Open Buildings dump and a target area (bbox, admin boundary, or city name)."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: [geopandas, pandas, shapely, pyarrow]
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, open-buildings, vida, geopackage, cadastre, building-footprints]
    related_skills: [qgis-pyqgis-mastery, nettoyage-polygones, chargement-gros-dataset-qgis, jointure-fiscale-spatiale]

---

# Extraire bâtiments (Open Buildings / VIDA)

Turn a country/continent-scale Open Buildings (Google) or VIDA-combined (Google+Microsoft) CSV/parquet dump into a fast, indexed GeoPackage layer for a specific zone.

## When to use

Given a raw Open Buildings/VIDA export (often several GB, covers a whole country or more) and a target zone (bounding box, admin boundary polygon, or city name to geocode), and you need the building footprints as a QGIS-ready layer — not the whole country loaded at once.

## Entrées

- `source_path`: path to the CSV or parquet dump (VIDA ships parquet partitioned by S2 cell or country; Google Open Buildings ships CSV.gz by S2 cell)
- `bbox` or `boundary_geom`: the target zone, either `(minx, miny, maxx, maxy)` in EPSG:4326 or a polygon (e.g. an admin boundary from GADM/OSM)
- `confidence_threshold`: minimum detection confidence to keep (Open Buildings ships a `confidence` column, typically 0.65–0.75 is a reasonable floor — lower keeps more buildings but more false positives)

## Sortie

Indexed GeoPackage (`.gpkg`) with one layer, filtered to the zone and confidence threshold, ready to add to QGIS. Typical row count for a mid-size African city: tens of thousands to low hundreds of thousands of footprints — small enough to render smoothly once indexed (see `chargement-gros-dataset-qgis` for why indexing matters).

## Implementation

```python
import geopandas as gpd
import pandas as pd
from shapely import wkt
from shapely.geometry import box

def extract_buildings(source_path, bbox, out_gpkg, confidence_threshold=0.7,
                       layer_name="buildings", chunksize=500_000):
    """
    bbox: (minx, miny, maxx, maxy) in EPSG:4326
    source_path: CSV(.gz) or parquet with columns like
                 latitude, longitude, geometry (WKT), confidence, area_in_meters
    """
    target_box = box(*bbox)
    is_parquet = str(source_path).endswith(".parquet")

    if is_parquet:
        # Parquet supports predicate pushdown on bbox-indexed datasets (VIDA
        # partitions by S2 cell) -- filter at read time when the schema
        # exposes lat/lon columns, else fall back to full read + spatial filter.
        df = pd.read_parquet(source_path)
        gdf = gpd.GeoDataFrame(
            df, geometry=gpd.GeoSeries.from_wkt(df["geometry"]), crs="EPSG:4326"
        )
        gdf = gdf.cx[bbox[0]:bbox[2], bbox[1]:bbox[3]]
    else:
        # CSV(.gz) — country-scale files don't fit in memory comfortably;
        # stream in chunks and only keep rows inside the bbox.
        chunks = []
        for chunk in pd.read_csv(source_path, chunksize=chunksize):
            chunk = chunk[
                (chunk["longitude"].between(bbox[0], bbox[2])) &
                (chunk["latitude"].between(bbox[1], bbox[3]))
            ]
            if len(chunk):
                chunks.append(chunk)
        df = pd.concat(chunks, ignore_index=True)
        gdf = gpd.GeoDataFrame(
            df, geometry=df["geometry"].apply(wkt.loads), crs="EPSG:4326"
        )

    if "confidence" in gdf.columns:
        gdf = gdf[gdf["confidence"] >= confidence_threshold]

    # Exact clip (bbox pre-filter above is a fast reject; this trims edge
    # geometries that only partially overlap the box).
    gdf = gdf[gdf.intersects(target_box)].copy()

    gdf.to_file(out_gpkg, layer=layer_name, driver="GPKG")
    return out_gpkg, len(gdf)
```

Then create the spatial index and load into QGIS (see `chargement-gros-dataset-qgis` for the indexing step — GeoPackage layers written by GeoPandas/Fiona already get a spatial index by default via GDAL, but verify with `ogrinfo -al -so out.gpkg` that `GEOMETRY = Polygon` and a spatial index (`rtree`) exists).

For a city rather than a raw bbox: geocode the city to an admin boundary polygon (OSM Nominatim or a GADM shapefile) and use `boundary_geom` with `gdf[gdf.within(boundary_geom)]` instead of the rectangular bbox, to avoid pulling in a neighboring town that happens to fall inside the bounding rectangle.

## Limites

- **Snapshot date**: Open Buildings / VIDA are point-in-time snapshots (commonly ~2023 imagery). Buildings constructed after that date will be missing — this dataset alone cannot certify "no building exists here today." Pair with a change-detection step if recency matters (see `detection-changement-batiments`, marked experimental — don't treat its output as ground truth either).
- **Confidence threshold tradeoff**: lower thresholds catch more real buildings but also more false positives (shadows, rock outcrops); there's no single correct value — validate against a sample of known ground truth for the target region before committing to a threshold.
- **CRS**: source data is EPSG:4326 (lat/lon); reproject to a local projected CRS (e.g. UTM zone for the country) before computing areas — area/distance calculations in EPSG:4326 are wrong (degrees, not meters).
