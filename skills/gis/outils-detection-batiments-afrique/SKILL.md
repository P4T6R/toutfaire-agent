---
name: outils-detection-batiments-afrique
description: "Catalog of models, datasets, and QGIS-integrated tools for building/rooftop-feature detection on satellite imagery, with an emphasis on Africa/low-resource-context coverage and quality. Use when choosing or comparing a detection approach before building or extending a pipeline."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, building-detection, africa, open-buildings, ramp, deepness, fair, hotosm]
    related_skills: [qgis-pyqgis-mastery, extraire-batiments-open-buildings, detection-piscine-panneaux-solaires, detection-changement-batiments]

---

# Outils de détection de bâtiments — contexte Afrique

Reference map of what exists for building/rooftop-feature detection on satellite imagery, before reaching for a custom pipeline. Several of these were built specifically for African/low-resource contexts, which matters — models trained on North American/European imagery transfer poorly (different roof materials, informal settlement density, imagery resolution/vintage).

## When to use

Before extending or replacing the ONNX-based detection in `detection-piscine-panneaux-solaires`, or when asked "does something already exist for X" before writing a custom detector from scratch.

## Datasets (pre-computed, no inference needed)

- **Google Open Buildings** (v3) — 1.8B+ building polygons across Africa, South/SE Asia, Latin America. Model was specifically trained/validated on African imagery (unlike most Western building datasets). Already used in `extraire-batiments-open-buildings`.
- **Microsoft Building Footprints** — global coverage, different model lineage than Google's; VIDA's combined dataset merges both (deduplicated) — generally higher recall than either alone.
- **OpenStreetMap buildings** — variable completeness by region (dense in cities with active mapping communities, sparse in rural/underserved areas) — good as a cross-check layer, not a primary source for tax assessment given the completeness gap is exactly the problem being solved.

## QGIS-integrated inference (run a model directly inside QGIS)

- **Deepness** (QGIS plugin, "Deep Neural Remote Sensing") — runs ONNX models directly on any raster layer inside QGIS: semantic segmentation, object detection, regression. Ships a model registry with ready-to-use models (buildings, roads, farmland, etc.), and supports exporting training tiles + using QGIS's own layer tools for annotation. **This is the natural home for the project's existing ONNX detection work** — instead of a standalone Python script calling `onnxruntime` outside QGIS, Deepness runs the same kind of ONNX model as a QGIS Processing algorithm, with tiling/extent-limiting handled for you. Worth evaluating as a replacement for the custom inference harness in `detection-piscine-panneaux-solaires` — it wouldn't fix model accuracy, but it removes a layer of custom plumbing (tiling, raster I/O) that's currently hand-rolled.
- **Mapflow (Geoalert)** — commercial QGIS plugin, hosted AI mapping pipelines for building footprints/roads/fields. Useful as a comparison baseline, but a paid/hosted dependency — evaluate against the fully open-source stack before committing.

## Africa/humanitarian-specific detection models

- **RAMP** (Replicable AI for Microplanning) — already in use on this project. Built specifically for low/middle-income-country building detection (originated from vaccine-campaign microplanning work). Ships pre-trained weights plus a training pipeline to fine-tune on local imagery — fine-tuning on a labeled Ouagadougou sample is a plausible path to materially improve on the current results, rather than switching models.
- **fAIr** (Humanitarian OpenStreetMap Team) — open AI-assisted mapping service purpose-built for exactly this problem space. Track record: extracted ~18M building footprints across Tanzania and Uganda in a 2019-2020 pilot, nearly tripling daily OSM building-add rate during deployment. **Notable for this project specifically: fAIr's "GeoAI community models" already include solar panel and rooftop-material detection** — this is worth investigating directly as an alternative or training-data source for `detection-piscine-panneaux-solaires`'s solar panel detection, instead of building that detector from scratch. HOT is also an organization actively present in West Africa — worth checking whether a community model already exists for the target region or a comparable one.
- **Few-shot transfer learning for missing-building detection in sub-Saharan Africa** (academic, HeiGIT/Li et al.) — documents the specific technique of fine-tuning a general building detector on a small local sample to close the gap for underrepresented regions; the general strategy (small local fine-tune > generic pretrained model, for African imagery) applies broadly here, not just to buildings.

## Practical recommendation for this project

1. Check whether an existing fAIr community model already covers solar panels for a comparable region before extending the custom ONNX pipeline — could shortcut a meaningful chunk of `detection-piscine-panneaux-solaires`'s remaining work.
2. If a custom model stays necessary, prioritize a small labeled validation/fine-tuning set for Ouagadougou specifically over swapping architectures — the "few-shot transfer learning" pattern above suggests this closes more of the accuracy gap than a bigger generic model would.
3. Consider migrating the ONNX inference from a standalone script to Deepness inside QGIS — same model, less custom plumbing, and it becomes usable by anyone on the team directly from the QGIS GUI, not just via the script.

## Limites

- This is a landscape snapshot — deep-learning-for-remote-sensing tooling moves fast; re-verify current state (dataset versions, plugin maturity) before committing to one, especially for anything not already in production use on this project.
- None of these solve the "confirm before trusting" problem described in `detection-piscine-panneaux-solaires` and `detection-changement-batiments` — swapping tools changes the failure mode, it doesn't remove the need for validation against local ground truth.
