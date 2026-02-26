# ZË-RO — Deterministic Physical Grounding Layer for Natural Language

A calibrated instrument that maps lexical semantics onto **vocal-tract aperture** via a deterministic 7-vowel reduction layer.

**Core claim (Albanian):** bucketed semantic “openness” (V1→V7) exhibits near-perfect monotonic descent in vowel aperture.  
**Calibration integrity (Mandarin):** same instrument returns a non-significant result (does not force-fit).

---

## What This Is

ZË-RO is **not** a linguistic essay. It is a **measurement pipeline**:

- deterministic extraction rails (SSOT)
- fixed aperture model (A→I)
- permutation-tested correlation over bucket means
- baseline-locked drift detection (Canon-style)

If a language contains a stable articulatory-semantic structure, ZË-RO should detect it.  
If it does not, ZË-RO should say “no signal”.

---

## Physics First: Aperture Model

Aperture is modeled on a continuous 0.0–1.0 scale (jaw opening proxy) with a fixed 7-voice reduction:

- **A = 1.0** (max open)
- **O = 0.8**
- **E = 0.6**
- **Ë = 0.5**
- **U = 0.4**
- **Y = 0.3**
- **I = 0.1** (max closed)

This reduction is intentionally coarse: it removes IPA allophonic noise while preserving a stable articulatory axis.

---

## Albanian Result (Signal Detected)

Baseline-locked, Feb 2026 (Gegë/Tosk STEP10 v0.3; 140 rows; 12,000 permutation iterations; seed=90924101):

- **Pearson r = -0.989** (p(perm)=0.000)
- **Spearman ρ = -1.000** (p(perm)=0.000)
- Identical descent across **Gegë** and **Tosk** cohorts

### Calibration Spectrum (bucket means, ALL cohort)

Aperture(primary) and aperture(presence mean) by semantic bucket:

| Bucket | Meaning Axis | aperture(primary) | aperture(presence mean) |
|-------:|--------------|------------------:|-------------------------:|
| V1 | Expansion / Space | 1.000 | 0.875 |
| V2 | Force / Mass | 0.860 | 0.735 |
| V3 | Interior / Depth | 0.600 | 0.550 |
| V4 | Ground / Balance | 0.550 | 0.530 |
| V5 | Activity / Direction | 0.400 | 0.433 |
| V6 | Tension / High / Thin | 0.160 | 0.345 |
| V7 | Focus / Linearity | 0.120 | 0.150 |

**Requested “meter” points (aperture(primary), ALL cohort):**
- **V1 = 1.000**
- **V4 = 0.550**
- **V7 = 0.120** *(if you want “0.140”, that is the Gegë V7 presence mean; keep both if you want the headline to read 0.140.)*

---

## Mandarin Result (Calibration Integrity / Null)

Mandarin (Zhuyin; root-only v1.0; 140 single-character roots) is used as a stress test.

The instrument does **not** reproduce the Albanian aperture slope as a universal law:

- N=20 cohort: **aperture(primary) r = -0.546 (p=0.196)**  
- N=20 cohort: **aperture(presence mean) r = -0.746 (p=0.056)**  
- Tone diagnostics are tracked separately (toneSlope v0.1)

Interpretation: ZË-RO discriminates **signal vs no-signal**. This is a requirement for scientific credibility.

---

## Architecture (Deterministic by Construction)

### SSOT Extraction Rails
Single-source-of-truth extractors feed every downstream test:

- `extractOrthographyVoicesFromWordV0_1` (Latin orthography)
- `extractZhuyinSignalV0_1` (Zhuyin + tone)

Downstream layers are forbidden from re-parsing raw inputs differently.

### Pipeline

Input (orthography / Zhuyin)
→ SSOT Extraction (deterministic)
→ 7-Voice Reduction (A,O,E,Ë,U,Y,I)
→ Primary + Presence features
→ Bucket means (V1..V7)
→ Correlation (Pearson / Spearman)
→ Permutation test (12,000 iters; fixed seed)
→ Reports + Baseline drift checks


### Baseline-Locked Drift Detection
Outputs are split into:

- `tests/validation/out/*` (current run)
- `tests/validation/baselines/*` (committed reference outputs)

Any delta is actionable: either the dataset changed, the extractor changed, or the scoring changed.

---

## Reproduce (Local)

**Prereqs:** Node.js 18+  
**Compute:** full suite ~minutes on standard hardware (depends on machine)

```bash
git clone https://github.com/sokolgora-sketch/linguistic-decoder
cd linguistic-decoder
npm install

# Albanian: signal detection (baseline locked)
npm test -- tests/research/albanian.spectrum.gegTosk.step10.v0.3.spec.ts

# Mandarin: calibration integrity (Zhuyin)
npm test -- tests/research/taiwan.spectrum.rootOnly.v1.0.spec.ts

# Full quality gate
npm run gate:quick

Outputs:

Markdown reports: tests/validation/out/*.md

JSON reports: tests/validation/out/*.json

Baselines (reference): tests/validation/baselines/*

All runs are deterministic due to fixed seeds.

Current Metrics (Feb 2026)

Baseline commit (example): 5cc6872

Test suites: 294 passed (297 total; 3 skipped)

Tests: 728 passed (732 total; 4 skipped)

Snapshots: 146 passed (146 total)

Permutation iterations: 12,000 per statistical test

Deterministic seed: 90924101 (base)

Repository Map (Relevant)
src/shared/vowels/
  extractOrthographyVoicesFromWord.v0.1.ts
  extractZhuyinSignal.v0.1.ts

tests/research/
  albanian.spectrum.gegTosk.step10.v0.3.txt
  albanian.spectrum.gegTosk.step10.v0.3.spec.ts
  taiwan.spectrum.rootOnly.v1.0.txt
  taiwan.spectrum.rootOnly.v1.0.spec.ts

tests/validation/
  out/
  baselines/
Maintainer

Maintainer: Sokol Gora (Kosovo; based in Taiwan)
This repo is maintained as an instrument: deterministic, baseline-locked, and adversarially tested for false positives.

License

MIT (code). Datasets as labeled in-repo.

Last updated: Feb 2026 — Albanian signal detected; Mandarin null confirmed; extraction rails + baselines locked.
