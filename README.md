## Hi 👋

## I’m Sokol Gora

I’m building **ZË-RO** — a deterministic **seven-vowel linguistic decoder** that treats vowels as the measurable “carrier core” of speech (with consonants as interrupters/shape).

ZË-RO is designed as a **calibrated instrument**, not a vibes generator:
- **Same input → same output** (deterministic engine + locked snapshots)
- **Evidence-first telemetry** (UI reads a contract/VM; missing data is explicit)
- **Mask vs Carrier honesty**: spelling path vs spoken (IPA) path can **diverge**
- **Anti-regression rails**: Canon C2 baseline + diff reports catch drift

---## How it works (high level)


Input word (+ optional IPA)
→ Heart: deterministic vowel/carrier extraction (SSOT)
→ Gates + Evidence telemetry + Canon C2 drift checks

## Flagship
- **ZË-RO / Linguistic Decoder:** https://github.com/sokolgora-sketch/linguistic-decoder  
  Deterministic vowel-path extraction, gates, evidence ledger, Canon C2 drift harness.

## Also
- **Seven Voices Orb:** https://github.com/sokolgora-sketch/seven-voices-orb  
  A rules/logic prototype built around the Seven-Voices system.

---

## What I care about
- Determinism (same input → same output)
- Evidence-first UI (no raw payload rendering)
- Guardrails (tests + baselines so changes don’t silently break meaning)

## Current focus
- Expand **Canon C2** corpus + validation reporting
- Tighten “carrier vs mask” analysis across spelling vs IPA

*(Last updated: 2026-02)*
