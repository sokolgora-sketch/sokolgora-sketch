# ZË-RO — Linguistic Decoder (Calibration-Grade Language Instrument)

I’m building **ZË-RO**, a deterministic research instrument for measuring **vowel-carrier structure** in words and testing whether **semantic buckets** align with a **physical aperture proxy** (jaw openness / vocal-tract constraint).

This is not “cool etymology.” It’s an **anti-handwave pipeline**: deterministic extraction → fixed scoring → permutation tests → baseline-locked drift detection.

---

## What ZË-RO does (deterministically)

1) **Extracts a 7-vowel “voice path”** using only **A, E, I, O, U, Y, Ë**
   - from **orthography** (spelling)
   - from **phonetics** when an **IPA string** is provided

2) **Detects Mask vs Carrier divergence**
   - when spelling path ≠ IPA carrier path, the UI marks **DIVERGE**
   - this is intentional: ZË-RO does not hide “language mess”

3) **Emits audit-friendly telemetry**
   - evidence-first output (stable references; “not emitted” is explicit)
   - research harnesses write **MD + JSON** reports
   - baselines are locked so drift is measurable, not argued

---

## Fixed meter (Aperture Proxy)

ZË-RO uses a **fixed, public meter** (open → closed):

- **A = 1.0**
- **O = 0.8**
- **E = 0.6**
- **Ë = 0.5**
- **U = 0.4**
- **Y = 0.3**
- **I = 0.1**

Two readouts per token:
- **primary** = first vowel carrier
- **presence mean** = mean aperture over unique carriers (in-order)

---

## Benchmark (Baseline-Locked): Albanian STEP10 v0.3 — Feb 26, 2026

**Status:** TARGET reached → baselines written and locked  
**Corpus:** `tests/research/albanian.spectrum.gegTosk.step10.v0.3.txt`  
**Size:** N = 140 (10 per bucket per dialect; Gegë + Tosk)  
**Permutation iters:** 12,000 (so p=0.000 means p < 1/12000)

### Observed bucket means (ALL)

| Bucket | N | aperture(primary) | aperture(presence mean) |
|--------|--:|------------------:|------------------------:|
| V1 | 20 | 1.000 | 0.875 |
| V2 | 20 | 0.860 | 0.735 |
| V3 | 20 | 0.600 | 0.550 |
| V4 | 20 | 0.550 | 0.530 |
| V5 | 20 | 0.400 | 0.433 |
| V6 | 20 | 0.160 | 0.345 |
| V7 | 20 | 0.120 | 0.150 |

Dialect V7 (presence mean):
- **Tosk:** 0.160  
- **Gegë:** 0.140

### Primary result (slope on bucket means vs semantic index 1..7)

| Cohort | Score | Pearson r | p (perm) | Spearman ρ | p (perm) |
|--------|-------|----------:|---------:|-----------:|---------:|
| ALL | aperture(primary) | **-0.989** | 0.000 | **-1.000** | 0.000 |
| ALL | aperture(presence mean) | **-0.984** | 0.000 | **-1.000** | 0.000 |
| Tosk | aperture(primary) | **-0.989** | 0.000 | **-1.000** | 0.000 |
| Tosk | aperture(presence mean) | **-0.984** | 0.000 | **-1.000** | 0.000 |
| Gegë | aperture(primary) | **-0.989** | 0.000 | **-1.000** | 0.000 |
| Gegë | aperture(presence mean) | **-0.983** | 0.000 | **-1.000** | 0.001 |

**Interpretation (instrument-level, not philosophy):**
- Near-perfect negative slope across V1→V7 means.
- Perfect rank-order descent for multiple cohorts (ρ = -1.000).
- Baseline rails make future disagreements measurable as diffs, not arguments.

**Baselines (MD/JSON):**
- `tests/validation/baselines/albanian.spectrum.gegTosk.step10.v0.3.{md,json}`
- plus audit/compare:
  - `...step10.v0.3.audit.v0.1.{md,json}`
  - `...step10.v0.3.compare.v0.1.{md,json}`

---

## Integrity check (honest null / drift): Mandarin (Zhuyin) stress test

Mandarin suite is there to prove the instrument **does not force-fit** correlations.

From:
`tests/validation/baselines/taiwan.spectrum.rootOnly.v1.0.compare.v0.1.md`

### N=10
- aperture(presence mean): **r = -0.815**, **p = 0.026**
- aperture(primary): r = -0.699, p = 0.078

### N=20 (dataset expanded)
- aperture(presence mean): r = -0.746, p = 0.056  *(weakens / loses significance)*
- aperture(primary): r = -0.546, p = 0.196

Tone correlations tracked separately:
`tests/validation/baselines/taiwan.spectrum.rootOnly.v1.0.toneSlope.v0.1.{md,json}`

**Engineering takeaway:** extractor is doing its job; dataset selection rules can introduce drift, and the harness exposes it.

---

## Reproduce (one command per suite)

```bash
# Full safety gate
npm run gate:quick

# Albanian STEP10 baseline suite
npm test -- tests/research/albanian.spectrum.gegTosk.step10.v0.3.spec.ts

# Albanian pilot probe
npm test -- tests/research/albanian.spectrum.gegTosk.pilot.v0.2.spec.ts

# Mandarin (Zhuyin) suite
npm test -- tests/research/taiwan.spectrum.rootOnly.v1.0.spec.ts
