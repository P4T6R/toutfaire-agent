---
name: recalage-vecteur-imagerie
description: "Correct a systematic georeferencing offset between a vector layer (buildings, parcels) and a basemap/satellite image using a robust median-offset estimate. Use when vector footprints appear visibly shifted from the imagery they should align with."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: [geopandas, shapely, numpy]
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, georeferencing, coregistration, offset-correction, remote-sensing]
    related_skills: [qgis-pyqgis-mastery, extraire-batiments-open-buildings, jointure-fiscale-spatiale, detection-changement-batiments]

---

# Recalage vecteur sur imagerie (correction d'offset)

Universal GIS problem: a vector layer (buildings, parcels, roads) and a raster basemap were produced from different sources/dates/projections and are visibly shifted relative to each other by a near-constant offset. This skill estimates that offset robustly and corrects it.

## When to use

Building footprints (or any vector layer) appear consistently shifted from the satellite/aerial imagery underneath when overlaid in QGIS — same shape, wrong position, shift looks roughly constant across the visible area (not a rotation or scale distortion, which needs a different fix — full georeferencing/warping, not a translation).

## Entrées

- `vector_gdf`: the layer to correct
- `reference_points` OR `reference_features`: either manually-picked control point pairs `[(vector_xy, imagery_xy), ...]`, or a set of matched features (e.g. a handful of very confidently-identified buildings) present in both the vector layer and a trusted reference (imagery, or a known-good vector source)
- Both inputs must be in the same CRS before computing offsets — reproject first if not

## Sortie

- The corrected vector layer, translated by `(-median_dx, -median_dy)`
- The estimated offset (`dx`, `dy`, and the spread/std of the individual sample offsets — a high spread means "constant offset" was the wrong model, see Limites)

## Implementation

```python
import numpy as np
import geopandas as gpd
from shapely.affinity import translate

def estimate_offset(vector_points, reference_points):
    """
    vector_points, reference_points: lists of (x, y) tuples, same length,
    same order -- each pair is the same real-world feature located in
    both datasets.
    Returns (dx, dy, spread) where spread is the std-dev of the per-point
    offsets -- a high spread means a single constant offset does not
    describe the misalignment well (see Limites).
    """
    vx, vy = np.array(vector_points).T
    rx, ry = np.array(reference_points).T
    dx_samples = rx - vx
    dy_samples = ry - vy
    dx, dy = np.median(dx_samples), np.median(dy_samples)
    spread = float(np.std(np.hypot(dx_samples - dx, dy_samples - dy)))
    return dx, dy, spread

def apply_offset_correction(vector_gdf, dx, dy):
    corrected = vector_gdf.copy()
    corrected["geometry"] = corrected["geometry"].apply(
        lambda geom: translate(geom, xoff=dx, yoff=dy)
    )
    return corrected
```

**Picking reference points in practice**: the most reliable source is a handful (10-30) of unmistakable features visible in both the vector layer and the imagery — building corners, road intersections, distinctive rooftops. Spread them across the zone rather than clustering them in one corner, so the median offset represents the whole area, not just one neighborhood. In QGIS this is normally done by eye with the two layers toggled/blended, digitizing point pairs manually — there is no reliable way to fully automate this matching step for arbitrary imagery without a feature-matching model, which is a materially harder problem than the offset correction itself.

## Limites

- **This corrects translation only.** If the true misalignment includes rotation or scale drift (common when the vector source and the imagery come from different projections or the imagery has lens distortion not fully corrected), a single (dx, dy) will not fix it — check the `spread` value; if it's large relative to typical building size, the offset is not constant across the zone and you need a proper affine/polynomial georeferencing (QGIS's Georeferencer tool) instead of this simple translation.
- **The offset can genuinely vary by sub-region** for large zones (imagery mosaicked from multiple source tiles/dates often has per-tile offset). For a city-scale correction, consider computing the offset per administrative sub-zone rather than one global median if quality matters.
- **Do this before, not after, downstream joins.** `jointure-fiscale-spatiale` and `detection-changement-batiments` both assume the vector layer is already correctly registered — running this correction after those steps means redoing them.
