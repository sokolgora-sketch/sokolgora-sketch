# ZË-RO — Linguistic Decoder

*A deterministic seven-vowel analysis instrument (orthography + optional IPA) with evidence-first telemetry and baseline-locked research harnesses.*

[![CI](https://github.com/sokolgora-sketch/linguistic-decoder/actions/workflows/ci.yml/badge.svg?branch=main)](https://github.com/sokolgora-sketch/linguistic-decoder/actions/workflows/ci.yml)
[![License: AGPL-3.0](https://img.shields.io/badge/license-AGPL--3.0-blue)](LICENSE)

---

## What this repo is

ZË-RO is a **calibration-grade decoder** for vowel-carrier structure in words.

It is designed to **measure**, **reproduce**, and **catch drift** — not to “sound right”.

Deterministic core behaviors:

1) **Extract a 7-vowel “voice path”** using only: **A, E, I, O, U, Y, Ë**
   - from **orthography** (spelling)
   - from **phonetics** when an **IPA string** is provided (optional)

2) **Detect Mask vs Carrier divergence**
   - if spelling path ≠ IPA carrier path, the UI marks **DIVERGE**

3) **Emit audit-friendly telemetry**
   - evidence-first output (stable references)
   - explicit “not emitted” instead of silent nulls
   - research harnesses write **MD + JSON** reports

This is a research instrument. It helps test hypotheses about vowel structure and meaning. It does **not** claim conclusions by default.

---

## Determinism & anti-regression

- **SSOT vowel extraction** (one authoritative mapper)
- **Evidence-first contracts** (UI reads VM; missing data is explicit)
- **Baseline locking** (drift detection via committed baselines)

Commands:

- `npm run gate:quick` — lint + unit tests + integration + build
- `npm test` — full suite
- `npm run canon:c2` — detect drift vs baseline (fails on unexpected change)
- `npm run canon:c2:update` — refresh baseline after an intentional change

---

## Deterministic aperture model

ZË-RO reduces vowel carriers to 7 categories and assigns a fixed **aperture proxy**:

| Voice | Aperture |
|------:|--------:|
| A | 1.0 |
| O | 0.8 |
| E | 0.6 |
| Ë | 0.5 |
| U | 0.4 |
| Y | 0.3 |
| I | 0.1 |

Two readouts are computed per item:
- **primary** = first carrier
- **presence mean** = mean aperture over unique carriers (in order)

---

## Example: “rhythm” (mask vs carrier)

```ts
analyzeWord("rhythm", { mode: "strict", ipa: "/ˈrɪð(ə)m/" });

Typical behavior:

Orthography (spelling): Y

Phonetics (IPA carriers): I → Ë

Status: DIVERGE

Benchmark: Albanian STEP10 v0.3 (baseline-locked)

Calibration spectrum suite: Albanian Gegë vs Tosk (orthography-only).

Corpus: tests/research/albanian.spectrum.gegTosk.step10.v0.3.txt (N=140, 10/bucket/dialect)

Harness: tests/research/albanian.spectrum.gegTosk.step10.v0.3.spec.ts

Baselines (committed):

tests/validation/baselines/albanian.spectrum.gegTosk.step10.v0.3.md

tests/validation/baselines/albanian.spectrum.gegTosk.step10.v0.3.json

plus audit/compare baselines in the same folder

Bucket means — ALL (N=140)

(From tests/validation/baselines/albanian.spectrum.gegTosk.step10.v0.3.md)

Bucket	N	aperture(primary)	aperture(presence mean)
V1	20	1.000	0.875
V2	20	0.860	0.735
V3	20	0.600	0.550
V4	20	0.550	0.530
V5	20	0.400	0.433
V6	20	0.160	0.345
V7	20	0.120	0.150

V7 presence-mean by dialect (from JSON baseline):

Tosk V7: 0.160

Gegë V7: 0.140

Slope test (12,000-iter permutation; bucket means vs semantic index 1..7)

(From tests/validation/baselines/albanian.spectrum.gegTosk.step10.v0.3.md)

Cohort	Score	Pearson r	p (perm)	Spearman ρ	p (perm)
ALL	aperture(primary)	-0.989	0.000	-1.000	0.000
ALL	aperture(presence mean)	-0.984	0.000	-1.000	0.000
Tosk	aperture(primary)	-0.989	0.000	-1.000	0.000
Tosk	aperture(presence mean)	-0.984	0.000	-1.000	0.000
Gegë	aperture(primary)	-0.989	0.000	-1.000	0.000
Gegë	aperture(presence mean)	-0.983	0.000	-1.000	0.001

Interpretation (instrument-level, not philosophy):

Rank ordering across V1→V7 bucket means is strictly descending (ρ = -1.000) in the baseline.

The harness exists to make drift visible when the dataset expands or words are replaced.

Honest null / drift visibility: Taiwan Zhuyin suite (Mandarin)

This suite exists to prove the instrument does not force-fit correlations under expansion.

Baselines (committed):

tests/validation/baselines/taiwan.spectrum.rootOnly.v1.0.compare.v0.1.md|json

tests/validation/baselines/taiwan.spectrum.rootOnly.v1.0.audit.v0.1.md|json

tests/validation/baselines/taiwan.spectrum.rootOnly.v1.0.toneSlope.v0.1.md|json

From taiwan.spectrum.rootOnly.v1.0.compare.v0.1.md:

Cohort	Score	Pearson r	p (perm)	Spearman ρ	p (perm)
N=10	aperture(presence mean)	-0.815	0.026	-0.821	0.032
N=20	aperture(presence mean)	-0.746	0.056	-0.571	0.204

Reading:

Stable signal should persist or strengthen under expansion.

Fragile signal should weaken (selection bias / drift becomes visible).

Tone diagnostics are tracked separately in toneSlope baselines.

SSOT extraction paths

Orthography SSOT: src/shared/vowels/extractOrthographyVoicesFromWord.v0.1.ts

Zhuyin SSOT: src/shared/vowels/extractZhuyinSignal.v0.1.ts

Reproduce
# Full gate
npm run gate:quick

# Albanian STEP10 baseline suite
npm test -- tests/research/albanian.spectrum.gegTosk.step10.v0.3.spec.ts

# Taiwan suite
npm test -- tests/research/taiwan.spectrum.rootOnly.v1.0.spec.ts

Outputs:

tests/validation/out/*.md|*.json (current run)

tests/validation/baselines/*.md|*.json (committed drift references)

Run locally
npm install
npm run dev
# http://localhost:3000
License

GNU Affero General Public License v3.0 (AGPL-3.0). See LICENSE.
