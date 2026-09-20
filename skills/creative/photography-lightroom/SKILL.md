---
name: photography-lightroom
description: "Photo editing (Lightroom AI masking, generative remove, adaptive presets, denoise) and shooting fundamentals (exposure triangle, composition, light). Use when asked about Lightroom, photo retouching, camera settings, or improving photography skills."
version: 1.0.0
author: Hermes Agent
license: MIT
dependencies: []
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [photography, lightroom, photo-editing, adobe, composition, exposure, ai-editing]
    related_skills: [comfyui, blogwatcher]

---

# Photography & Lightroom Mastery

Reference skill for photo editing (Adobe Lightroom, AI-assisted retouching) and shooting fundamentals. Use it to answer questions, critique a workflow, or build a weekly research digest of new techniques.

## When to use

- User asks how to edit/retouch a photo, or how a specific Lightroom tool works
- User asks about camera settings, exposure, or composition for a shot they're planning or reviewing
- Building a recurring "what's new in photo editing / Lightroom" digest (pair with a research skill for fresh web results — this file is the baseline, not a replacement for live search)

## Lightroom — AI tooling (2026 baseline)

- **AI Masking** — auto-detects Subject, Sky, Background, People (skin/hair/eyes), Objects. Use instead of manual gradient/brush masks whenever the target matches one of these categories.
- **Generative Remove** (Firefly-powered) — erases unwanted objects; has an on-device mode (no upload to Adobe servers) for offline work or privacy-sensitive shoots.
- **Adaptive Presets** — presets built on AI masks (e.g. a Sky mask) instead of fixed linear gradients, so the same preset self-adjusts per photo instead of requiring manual re-alignment.
- **Denoise AI** — cleans high-ISO noise while preserving fine detail; run before sharpening, not after.
- **AI Sharpen** — recovers fine texture (fur, foliage, fabric) at near-pixel precision.
- **Lens Blur (AI)** — synthetic bokeh with automatic depth mapping; use for lenses/bodies that couldn't produce shallow DOF optically.

Typical AI-assisted edit order: cull → white balance → exposure/tone → Denoise AI → masks (subject/sky/background) → Adaptive Preset or manual local adjustments → Generative Remove for cleanup → AI Sharpen → export.

## Shooting fundamentals

**Exposure triangle** (get this right before composition matters at all):
- Aperture (f-number): lower = more light + shallower depth of field (subject isolation); higher = more of the scene in focus
- Shutter speed: fast (1/1000s+) freezes motion; slow (1/30s and below) introduces motion blur / needs stabilization
- ISO: low (100) = clean; high (3200+) = brighter but noisier — push ISO before accepting a shutter speed that will blur the shot

**Composition**:
- Rule of thirds — place the subject/horizon off-center on the grid intersections
- Leading lines — roads, rivers, fences, railings that pull the eye toward the subject
- Simplify the frame — remove anything that doesn't serve the subject before pressing the shutter, not just in post

**Light** (matters more than composition or gear):
- Golden hour (early morning / evening) — soft, warm, flattering, low contrast
- Avoid harsh midday sun — hard shadows, blown highlights; if unavoidable, shoot in open shade or use a diffuser/fill

## Researching what's new

Lightroom AI features ship fast (monthly-ish updates). When asked for current best practice, don't rely solely on this file — pull fresh results (Adobe's official Lightroom help pages, DPReview, and photography tutorial sites) and cross-check the date on any tutorial against the current Lightroom version.
