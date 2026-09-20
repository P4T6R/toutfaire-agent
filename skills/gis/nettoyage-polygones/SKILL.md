---
name: nettoyage-polygones
description: "Clean noisy building/parcel polygons: orthogonalize corners to right angles and filter by area to drop slivers and unrealistically large merged blobs. Use when a polygon layer (often from ML footprint extraction) looks jagged or contains obvious non-buildings."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, polygon-cleaning, orthogonalize, geoprocessing]
    related_skills: [qgis-pyqgis-mastery, extraire-batiments-open-buildings, jointure-fiscale-spatiale]

---

# Nettoyage de polygones (orthogonalisation + filtres)

Clean up building/parcel polygon layers that came out of automated extraction (ML footprint detection, raster-to-vector conversion) looking jagged, and remove geometries that are obviously not real buildings by area.

## When to use

A polygon layer has stair-stepped/jagged edges instead of clean corners (typical of raster-derived vectors), or contains slivers (near-zero area, digitization noise) and/or improbably huge merged blobs (adjacent buildings that got fused into one polygon during extraction).

## Entrées

- `polygons_gdf`: the layer to clean (must be in a projected CRS with meters, not EPSG:4326 — reproject first, area filtering in degrees is meaningless)
- `min_area_m2` / `max_area_m2`: plausible building footprint range for the context (e.g. residential Ouagadougou: maybe 20–2000 m² — calibrate against known real buildings in the target area, don't use generic defaults blindly)
- `max_angle_delta`: orthogonalization tolerance in degrees (QGIS default 15°)

## Sortie

Cleaned polygon layer: corners snapped toward 90°/180° where within tolerance, slivers and outsized blobs removed (moved to a separate `rejected` layer for audit rather than silently deleted).

## Implementation

Orthogonalization is a built-in QGIS Processing algorithm — don't hand-roll corner-snapping geometry math, `native:orthogonalize` already does this well:

```python
import processing
import geopandas as gpd

def orthogonalize_layer(input_layer, max_angle_delta=15, max_iterations=1000):
    result = processing.run("native:orthogonalize", {
        'INPUT': input_layer,
        'MAX_ANGLE_DELTA': max_angle_delta,
        'MAX_ITERATIONS': max_iterations,
        'OUTPUT': 'memory:'
    })
    return result['OUTPUT']


def filter_by_area(polygons_gdf, min_area_m2, max_area_m2, area_col=None):
    """
    polygons_gdf must be in a projected (meters) CRS.
    area_col: use an existing area column if present and trustworthy;
    otherwise compute fresh with .geometry.area (cheap, and avoids trusting
    a stale area value from an upstream dataset that may be in a different CRS).
    """
    areas = polygons_gdf[area_col] if area_col else polygons_gdf.geometry.area
    keep_mask = areas.between(min_area_m2, max_area_m2)
    kept = polygons_gdf[keep_mask].copy()
    rejected = polygons_gdf[~keep_mask].copy()
    rejected["reject_reason"] = np.where(
        areas[~keep_mask] < min_area_m2, "too_small", "too_large"
    )
    return kept, rejected
```

Run orthogonalization *before* area filtering when working from a GeoDataFrame + QGIS processing round-trip (orthogonalizing can slightly change area for slivers, occasionally pushing them across the min-area threshold either way) — decide the order based on which you want to be authoritative for your pipeline, but be consistent and document the choice, since it does change output counts at the margins.

## Limites

- **`max_angle_delta` is a tradeoff, not a universal constant**: too high and it distorts genuinely non-rectangular buildings (round structures, irregular compounds); too low and it barely touches jagged extraction artifacts. Calibrate visually against a sample before running on the full dataset.
- **Fused-blob polygons (`too_large` rejects) usually need splitting, not discarding** — an oversized polygon is often two or more real adjacent buildings merged by the extraction step. Dropping it loses real buildings from the tax roll; flag `too_large` rejects for manual review/splitting rather than treating them the same as digitization noise (`too_small`).
- **Area thresholds are context-specific** — a min/max area tuned for a dense urban neighborhood will misclassify large rural compounds or small urban kiosks. Don't reuse the same thresholds across very different zones without re-checking.
