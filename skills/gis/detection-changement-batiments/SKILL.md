---
name: detection-changement-batiments
description: "EXPERIMENTAL / NOT RELIABLE — building change detection (confirmed/demolished/new) between an imagery-based buildings snapshot and current imagery. Documented known-broken failure mode: sensitive to georeferencing offset. Do not present output as trustworthy without a working recalage step first and human review."
version: 0.1.0-experimental
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [gis, qgis, change-detection, experimental, unreliable]
    related_skills: [recalage-vecteur-imagerie, extraire-batiments-open-buildings, detection-piscine-panneaux-solaires]

---

# Détection de changement (bâtiments) — ⚠️ EXPÉRIMENTAL

**Status: known broken in practice, do not treat as production-ready.** Documented here honestly so the agent doesn't present this capability with more confidence than it deserves, and so the failure mode is captured for whoever picks this back up.

## When to use

Only when explicitly asked to attempt change detection (confirmed / demolished / new construction) between a buildings snapshot and newer imagery — and even then, warn the user upfront that this is unreliable in the current state, and check `recalage-vecteur-imagerie` has actually been applied first.

## What it's supposed to do

Compare a buildings snapshot (e.g. Open Buildings 2023) against current imagery to classify each footprint as:
- **confirmed** — building still present in current imagery
- **demolished** — footprint present in snapshot, no longer present in imagery
- **new** — structure visible in current imagery, absent from the snapshot

## Why it's broken

The comparison is fundamentally a spatial overlap test between (a) the snapshot polygon and (b) whatever "is there now" detection method is used on current imagery. When the two inputs have even a small, uncorrected georeferencing offset (see `recalage-vecteur-imagerie`), every real building looks shifted relative to the current-imagery detection — the overlap test produces false "demolished" + false "new" pairs for buildings that never actually changed, at a rate that can swamp the real signal. This was observed directly on this project: change detection output was dominated by offset artifacts, not real construction changes.

## What would need to be true before trusting this

1. **Registration must be solid first** — run and validate `recalage-vecteur-imagerie` (or equivalent) on both the snapshot and whatever produces the "current state" layer, and confirm the offset spread is small relative to typical building size, *before* attempting change detection. Garbage registration in → garbage change detection out, no amount of tuning the comparison logic fixes this.
2. **The "current state" detection method needs its own accuracy validated independently** — if that step comes from `detection-piscine-panneaux-solaires`-style ONNX detection or similar, its own false positive/negative rate compounds with the offset problem.
3. **A buffer/tolerance-based overlap test** (not exact intersection) is necessary regardless — but the buffer size should be derived from the *measured* residual offset spread after registration, not guessed.

## Limites

- Do not report `demolished`/`new` classifications from this skill as fact to an end user (e.g. a tax authority) without a human reviewing flagged cases against the actual current imagery.
- No known-good accuracy figure exists for this pipeline as currently implemented — don't quote one.
- If asked to "just run it anyway," proceed but prepend the output with the caveat above; don't silently drop the warning because the user wants a fast answer.
