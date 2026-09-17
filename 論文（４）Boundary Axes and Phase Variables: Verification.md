---
作成日時: 2026-09-16T16:40:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Boundary Axes and Phase Variables: Verification (D→0, c_mem, and the (r,θ) Re-parameterization)

> English version of the Japanese note [[論文（４）境界軸と位相変数の検証]]. All numbers, tables, and links are reproduced faithfully.
> Corresponds to remaining task ② of [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]].
> **Objective:** final verification of the metric-degeneracy prediction (det g→0 ∧ λ_min→0 ∧ κ→∞), rejected on the γ/c_s axes and high-dimensional PCA submanifolds (Experiments 4–7), along three systems:
> (A) the noise-annihilation limit D→0, (B) the memory strength c_mem (the ROI-onset direction), and
> (C) re-parameterization into phase variables (r,θ). In particular, (C) tests the prediction under Paper (III)'s "natural coordinates = amplitude & phase":
> if the phase is uniform in the circulation phase, the theory predicts g_θθ = E[(∂_θ lnρ)²] → 0 (a phase zero mode);
> whether this can be demonstrated is the crux.

## 1. Methods

- Same SPDE (c_mem=5.0, D=0.05, T=300, burn-in 100, J_asym present), seeds 42/100.
- **A (D→0):** γ∈{0.1 ROI, 0.2 boundary, 1.0 EQ} × D∈{0.05, 0.02, 0.01, 0.005}.
  If noise vanishes, the density should concentrate on a deterministic attractor: in the circulation phase a 1-dimensional ring (→ flat in the phase direction), in the equilibrium phase a fixed point (→ needle) is predicted.
- **B (c_mem direction):** γ=0.1 fixed, c_mem∈{0.05, 0.1, 0.2, 0.5, 1, 2, 5, 10, 20} (an axis crossing the circulation-onset boundary).
- **C (phase variables):** pair (0,1) is re-parameterized into (r,θ). The Cartesian-KDE score is converted to the polar score via the chain rule (avoiding the r=0 singularity):
  ∂_r lnρ = (x∂_x + y∂_y)/r,  ∂_θ lnρ = −y∂_x + x∂_y
  → g_rr, g_rθ, g_θθ, det, κ_p. Phase uniformity: winding-number rate ω̄=⟨dθ/dt⟩, circular concentration R1=|⟨e^{iθ}⟩|. A matched-Gaussian null (isotropic Gaussian sample with the same covariance; theoretically g_θθ=0) is used as the estimator baseline.

## 2. Results

### A. D→0 limit (seed 42)

| γ | D | λ_min(2D) | κ | g_θθ (phase direction) | σ_pair | R1 |
| --- | --- | --- | --- | --- | --- | --- |
| 0.1 | 0.05 | 0.200 | 1.60 | 1.47 | 2.25 | 0.019 |
| 0.1 | 0.02 | 0.197 | 1.13 | 0.94 | 2.26 | 0.013 |
| 0.1 | 0.01 | 0.158 | 1.82 | 1.81 | 2.46 | 0.013 |
| 0.1 | 0.005 | 0.130 | 2.16 | 1.68 | 2.57 | 0.033 |
| 0.2 | 0.005 | 0.254 | 1.81 | 1.25 | 1.85 | 0.004 |
| 1.0 | 0.05 | 4.2×10⁷ | 1.51 | 1.3×10⁸ | 0.00 | **1.00** |
| 1.0 | 0.005 | 9.3×10⁸ | 2.85 | 4.3×10⁹ | 0.00 | **1.00** |

→ In ROI/boundary, even as D→0 σ_pair remains ≈ 2.3 (the density is dominated not by noise but by deterministic driving). λ_min stays bounded at 0.13–0.38, and g_θθ also stays at 1.3–1.9, **never approaching zero**. In EQ (γ=1.0), conversely, λ_min→10⁹, g_θθ→10⁹, R1→1.0 (a needle density with the phase completely pinned = explosion of "sharpness").

### B. c_mem sweep (γ=0.1, D=0.05, seed42)

| c_mem | λ_min | κ | g_θθ | R1 | σ_pair | clip |
| --- | --- | --- | --- | --- | --- | --- |
| 0.05 | 7.3×10³ | 1.85 | 2.7×10⁴ | 0.9999 | 0.02 | 0 |
| 0.1 | 23.0 | **34.7** | 801 | 0.9917 | 0.19 | 0 |
| 0.2 | 0.507 | 1.99 | 2.22 | 0.097 | 1.42 | 0 |
| 0.5 | 0.509 | 1.71 | 1.90 | 0.083 | 1.41 | 0 |
| 1.0 | 0.304 | 1.63 | 1.82 | 0.072 | 1.86 | 0 |
| 5.0 | 0.200 | 1.60 | 1.47 | 0.019 | 2.25 | 0 |
| 20.0 | 0.133 | 1.26 | 1.01 | 0.011 | 2.72 | 6283 |

→ **The circulation onset is sharp between c_mem∈(0.1, 0.2)** (R1: 0.99→0.10, σ: 0.19→1.42). Yet immediately above the onset (c_mem=0.2) the metric has λ_min=0.507 · κ=1.99 — **far from degenerate; in fact the most "sharp-est"**. The κ=34.7 at c_mem=0.1 is the maximal anisotropy, but it lies on the needle (quasi-equilibrium) side with R1≈0.99 — the anti-degeneracy side (λ_min grows).

### C. Phase variables (r,θ), γ sweep (2-seed average, D=0.05)

| γ | g_θθ (measured) | g_θθ (G-null) | ratio g_θθ/G | R1 (measured) | R1 (G-null) | ω̄ |
| --- | --- | --- | --- | --- | --- | --- |
| 0.05 | 1.08 | 0.54 | 2.00 | 0.006 | 0.045 | +0.003 |
| 0.1 | 1.26 | 0.54 | 2.32 | 0.015 | 0.045 | +0.003 |
| 0.2 (boundary) | 1.14 | 0.47 | 2.45 | 0.020 | 0.045 | +0.002 |
| 0.3 | 1.83 | 0.84 | 2.19 | 0.026 | 0.043 | +0.002 |
| 0.5 | 1.55 | 0.57 | 2.72 | 0.037 | 0.045 | +0.002 |
| 1.0 (EQ) | 1.2×10⁸ | 0.47 | 2.7×10⁸ | **1.000** | 0.045 | ≈0 |

→ Across the ROI and the boundary **g_θθ is essentially constant at 2.0–2.7× the G-null (≈1–1.8)**. The phase is uniform (R1≈0.02), yet the density has **structured angular dependence**, and the Fisher information along the phase direction does not vanish. "Circulation phase = uniform phase → g_θθ→0 phase zero mode" **does not hold**. Only the EQ side explodes to g_θθ→10⁸ (phase pinned, needle).

## 3. Verdict and Conclusions

**Verdict: prediction rejected (final)** — over every axis tested (γ, c_s, D→0, c_mem) · every coordinate system (Cartesian, PCA d=2–5, polar (r,θ)) · every estimator (KDE, k-NN), the information-geometric singularity λ_min→0 ∧ det g→0 ∧ κ→∞ does not appear.

1. **The D→0 limit is the decisive "falsification mode":** the circulation-phase density does not converge to a 1-dimensional ring even when noise is removed (driving preserves the density width), so the premise itself — "the circulation direction becomes flat" — collapses. Only the equilibrium side converges to a needle, but that is a metric **infinite limit (anti-degeneracy)**, not det g→0.
2. **The circulation-onset point in the c_mem direction is also non-singular:** at the R1 0.99→0.10 transition (onset), λ_min=0.51 · κ=2.0. The maximal κ=34.7 lies **before** onset, on the needle side, where λ_min grows — anti-degeneracy.
3. **The phase-variable re-parameterization extracted the crux of the prediction:** in (r,θ) coordinates, g_θθ is **significantly positive across the whole ROI at 2.0–2.7× the G-null** (the density carries angular structure). The topological-circulation information-geometric consequence "circulation = uniform phase = zero mode along the phase direction" is **not observed** on this system's real trajectories (even with R1≈0.02, g_θθ≈1 does not vanish).
4. **ω̄≈0.002–0.004 is tiny:** the net winding-number rate of the pair-(0,1) phase is nearly zero (the circulation originates in the velocity field of the Q dynamics, not in a real-space phase) — consistent with why no phase zero mode appears.

**Implications for the paper:**
- Not abandonment of the prediction but **identification of the conditions under which it holds**: g_θθ→0 in r,θ coordinates can hold only when the density is invariant along the phase direction (a uniformly rotationally symmetric density), but this system's driving dynamics break that symmetry (imprinting angular structure as 1.0–1.8 of Fisher information).
- The robust fact for the information geometry of this system's non-equilibrium stationary density is "non-degenerate, bounded κ"; instead, the boundary between **NEIP and the quasi-equilibrium (needle) phase is characterized not by metric explosion (λ_min→∞, κ↑) but by the sharpening of the τ-density** (re-confirmation of the same conclusion as Experiments 4–7).

## 4. Artifacts

- Script: `研究/AI/コード/数値実験8_修正版_境界軸と位相変数.py` (reproducible · saved)
- CSV: `研究/AI/コード/境界軸位相_result.csv` (axis, γ, D, c_mem, seed × all metric/phase diagnostics)
- Related: [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]] (original: [[論文（４）相境界掃引による計量縮退予言の検証]]), [[論文（４）Metric-Degeneracy Test on High-Dimensional Submanifolds]] (original: [[論文（４）高次元部分多様体での計量縮退検証]]), [[論文（４）kNNによる推定器交差検証]]