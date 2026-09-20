---
name: detection-piscine-panneaux-solaires
description: "EXPERIMENTAL / NOT RELIABLE — ONNX-based detection of pools and solar panels from satellite/aerial imagery as wealth signals for tax assessment. Known issues: heavy compiled dependencies, slow inference, inflated/overconfident results. Do not present detections as ground truth without human review."
version: 0.1.0-experimental
author: Hermes Agent
license: MIT
dependencies: [onnxruntime]
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, object-detection, onnx, experimental, unreliable, remote-sensing]
    related_skills: [jointure-fiscale-spatiale, detection-changement-batiments, extraire-batiments-open-buildings]

---

# Détection piscine / panneaux solaires — ⚠️ EXPÉRIMENTAL

**Status: functional but not trustworthy as-is** — heavy, slow, and prone to inflated (overconfident / false-positive-heavy) results. Documented honestly rather than presented as a solid capability.

## When to use

Only when explicitly asked to detect pools or solar panels from imagery as a wealth signal for `jointure-fiscale-spatiale` — and even then, set expectations: this produces candidates for human review, not a finished dataset.

## What it's supposed to do

Run an object-detection model (referenced here as RAMP-family / ONNX runtime, matching what was used on this project) over tiled satellite/aerial imagery for a zone, producing point or polygon detections for pools and solar panel installations, with a confidence score per detection.

## Known problems

- **Heavy compiled dependencies**: ONNX Runtime (plus whatever produced the model — often a full PyTorch→ONNX export chain) is a much heavier, harder-to-deploy dependency than the rest of this skill set (which is pure GDAL/GeoPandas/QGIS). Expect real friction getting this to run in a lightweight server/cron context — pin exact package versions, and budget setup time separately from the rest of the pipeline.
- **Slow inference**: tiling a city's worth of high-resolution imagery and running inference tile-by-tile is the actual bottleneck for city-scale runs — batch inference and GPU acceleration are close to mandatory for anything beyond a small pilot zone; CPU-only inference over a whole city is impractical.
- **Inflated / overconfident results**: detections have skewed toward false positives in practice (reflective roofing misclassified as solar panels, decorative water features or unusually-shaped structures misclassified as pools). Confidence scores from the model have not been well-calibrated against this project's actual imagery/region — don't treat a high confidence score as a strong reliability signal without independent validation.

## What would help before trusting this in production

1. **A labeled validation set specific to the target region** (Ouagadougou or wherever the deployment zone is) — model performance on generic/other-region training data does not transfer reliably; measure actual precision/recall locally before trusting output volume.
2. **Confidence recalibration** against that local validation set, not just using the model's raw output score as-is.
3. **A cheap pre-filter** (e.g. size/shape heuristics on candidate regions) to cut the false-positive rate before it reaches the expensive detection step, if inference cost is a bottleneck.
4. **Mandatory human review step** for any detection that will actually influence a tax tier — this pipeline should narrow what a human inspector looks at, not replace them.

## Limites

- Do not feed raw detections from this skill directly into `jointure-fiscale-spatiale`'s tax tier logic without a review/approval step in between.
- No validated precision/recall numbers exist for this detector on real deployment imagery as of writing — don't quote any.
- If imagery resolution for the target zone is materially different from what the model was trained/tested on, expect performance to degrade further and say so.
