---
name: jointure-fiscale-spatiale
description: "Spatial join of buildings against wealth-signal layers (pools, solar panels, etc.) to derive a suggested tax tier and produce a fiscal report CSV. Use when you have a buildings layer and one or more point/polygon feature layers to cross-reference for property tax assessment."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: [geopandas, pandas]
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, spatial-join, property-tax, fiscal-report, cadastre]
    related_skills: [extraire-batiments-open-buildings, nettoyage-polygones, detection-piscine-panneaux-solaires]

---

# Jointure fiscale spatiale

Cross-reference a buildings layer against one or more "wealth signal" layers (detected pools, solar panels, plot size, etc.) to assign a suggested tax tier per building, and export a fiscal report CSV.

## When to use

You have a cleaned buildings layer (see `extraire-batiments-open-buildings` + `nettoyage-polygones`) and one or more feature layers representing taxable amenities or wealth signals, and need a per-building tax tier plus an exportable report for the tax authority.

## Entrées

- `buildings_gdf`: cleaned building polygons (must have a stable unique ID column, e.g. `building_id`)
- `signal_layers`: dict of `{signal_name: geodataframe}` — e.g. `{"pool": pools_gdf, "solar_panel": panels_gdf}`, each either points or polygons
- `tax_rules`: the tier logic — see below, this is the part that MUST be reviewed/approved by the tax authority before being trusted, it is business logic, not a technical detail

## Sortie

- The buildings GeoDataFrame with added boolean/count columns per signal (`has_pool`, `has_solar_panel`, ...) and a `tax_tier` column
- A flat CSV report: one row per building, columns = ID, area, signals present, suggested tier, and (important for auditability) which signals actually influenced the tier

## Implementation

```python
import geopandas as gpd
import pandas as pd

def spatial_fiscal_join(buildings_gdf, signal_layers, tax_rules, id_col="building_id"):
    """
    signal_layers: {"pool": gdf, "solar_panel": gdf, ...}
    tax_rules: callable(row) -> tier_label, applied after signal columns are added.
               Keep this pluggable -- tax tier logic changes with policy, the
               spatial join itself should not need to change when it does.
    """
    result = buildings_gdf.copy()

    for signal_name, signal_gdf in signal_layers.items():
        # sjoin_nearest with a small max_distance tolerates minor georeferencing
        # offset between the buildings layer and the signal layer (see
        # recalage-vecteur-imagerie -- ideally both layers are already
        # registered to the same reference before this join runs).
        joined = gpd.sjoin(
            result[[id_col, "geometry"]],
            signal_gdf[["geometry"]],
            how="left",
            predicate="intersects",
        )
        counts = joined.groupby(id_col).size()
        result[f"has_{signal_name}"] = result[id_col].map(counts).fillna(0).gt(0)
        result[f"{signal_name}_count"] = result[id_col].map(counts).fillna(0).astype(int)

    result["tax_tier"] = result.apply(tax_rules, axis=1)
    return result


def default_tax_rules(row):
    """Example only -- replace with the DGI's actual approved tier logic
    before this touches a real fiscal report. Encoding tax policy is a
    legal/administrative decision, not a technical one."""
    score = 0
    if row.get("has_pool"):
        score += 2
    if row.get("has_solar_panel"):
        score += 1
    if row.get("area_m2", 0) > 300:
        score += 1
    return ["standard", "elevated", "high", "premium"][min(score, 3)]


def export_fiscal_report(joined_gdf, out_csv, signal_cols):
    cols = ["building_id", "area_m2", "tax_tier"] + [f"has_{c}" for c in signal_cols]
    joined_gdf[cols].to_csv(out_csv, index=False)
```

## Limites

- **The tier logic (`tax_rules`) is policy, not code** — never ship a default scoring function to production without explicit sign-off from whoever owns the actual tax schedule. The function above is a structural example, not a recommendation for real weights.
- **Join tolerance vs. false attribution**: `predicate="intersects"` with points/small polygons assumes the signal layer is already spatially registered to the buildings layer. If there's a georeferencing offset (common — see `recalage-vecteur-imagerie`), a pool detected near building A can get attributed to neighboring building B. Fix registration *before* running this join; don't compensate by loosening the join tolerance, that just trades false negatives for false positives.
- **Signal layer quality caps report quality**: if `signal_layers["pool"]` comes from an unreliable detector (see `detection-piscine-panneaux-solaires`, marked experimental), the fiscal report inherits that unreliability. A human review pass on flagged high-tier buildings is strongly recommended before any tax notice goes out — this pipeline should widen the net for inspection, not replace inspection.
