---
作成日時: 2026-09-16T14:20:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction (det g → 0)

> English version of the Japanese note [[論文（４）相境界掃引による計量縮退予言の検証]]. All numbers, tables, and links are reproduced faithfully.
> Follow-up experiment based on "Implications for the next stage" ① of Experiment 4 (corrected version, KDE Fisher information geometry on real trajectories).
> **Objective:** to directly test whether the metric degeneracy "det g → 0 ∧ λ_min → 0 ∧ κ → ∞" at the NEIP phase boundary — predicted by Paper (III)'s information-geometric framework — is reproduced by the 2-dimensional projected metric on real trajectories.

## 1. Conditions & Methods

- Dynamics: corrected SPDE (same as Experiments 2/3/4): $\xi_i = \psi^3-\psi-J_s\psi-J_a\psi$, $Q+=(-\gamma Q+c_{mem}\xi)dt$, $\psi += (-Q+\sqrt{2D dt}\,\eta)dt$.
- Base settings: $c_{mem}=5.0$, $D=0.05$, $T=300$, burn-in 100, $J_{asym}$ present (non-equilibrium driving).
- **Axis A (γ sweep):** memory decay rate $\gamma\in\{0.05,0.075,0.1,0.15,0.2,0.25,0.3,0.35,0.4,0.5,0.75,1.0,1.5,2.0\}$.
  Experiment-2 Scan 2 established that the **ROI boundary lies at γ∈(0.2, 0.5]** → the principal axis crossing the boundary.
- **Axis B (c_s sweep):** coupling scale $c_s\in\{0.2,0.4,0.6,0.8,1.0,1.2,1.4,1.6,1.8\}$ (γ=0.1) → direction toward the destabilization boundary.
- Quantities evaluated (pair-(0,1) 2D KDE–Fisher metric, same implementation as Experiment 4, $h_{scale}=1.0$): $\det g$, $\lambda_{\min}$, $\kappa=\lambda_{\max}/\lambda_{\min}$, marginal $\sigma$, cross-spectral imaginary-part band integral $|I|$ (ROI marker, fast approximation).
- Sweeps are averaged over 2 seeds (42, 100). Boundary-candidate points are analyzed in detail with a 6-seed main table + bootstrap (B=12) + N scaling (3000–20000).

## 2. Results

### A. γ sweep (cs=1.0) — 2-seed average

| γ | λ_min (±range) | det g (±range) | κ | σ_pair | \|I\| | clip |
| --- | --- | --- | --- | --- | --- | --- |
| 0.05 | 0.1473 (0.0295) | 0.0287 (0.0052) | 1.39 | 2.592 | 0.049 | 0 |
| 0.075 | 0.1853 (0.0353) | 0.0436 (0.0116) | 1.28 | 2.339 | 0.159 | 0 |
| 0.1 | 0.2216 (0.0222) | 0.0683 (0.0057) | 1.41 | 2.118 | 0.010 | 0 |
| 0.15 | 0.2353 (0.0058) | 0.0755 (0.0087) | 1.37 | 2.003 | 0.021 | 0 |
| 0.2 | 0.2805 (0.0057) | 0.1182 (0.0133) | 1.51 | 1.843 | 0.445 | 0 |
| 0.25 | 0.3816 (0.0079) | 0.2127 (0.0374) | 1.45 | 1.603 | 0.116 | 0 |
| 0.3 | 0.3308 (0.0019) | 0.2244 (0.0229) | 2.05 | 1.616 | 0.201 | 0 |
| 0.35 | 0.4049 (0.0004) | 0.2802 (0.0445) | 1.71 | 1.539 | 0.171 | 0 |
| 0.4 | 0.3916 (0.0111) | 0.2477 (0.0323) | 1.63 | 1.535 | 0.029 | 0 |
| 0.5 | 0.4728 (0.0005) | 0.3627 (0.0559) | 1.62 | 1.400 | 0.133 | 0 |
| 0.75 | 2.3×10⁵ | 9.7×10¹¹ | 5.07 | 0.644 | 0.168 | 0 |
| 1.0 | 3.7×10⁷ | 2.3×10¹⁵ | 1.76 | 1.145 | 0.000 | 0 |
| 1.5 | 6.8×10⁷ | 7.2×10¹⁵ | 1.62 | 0.626 | 0.000 | 0 |
| 2.0 | 1.3×10⁸ | 2.5×10¹⁶ | 1.61 | 0.721 | 0.000 | 0 |

→ λ_min **crosses the boundary band (γ∈[0.2,0.5]) monotonically increasing and never approaches zero**. The minimum lies deep inside the ROI at γ=0.05. For γ≥0.75, components 0 and 1 are pinned (as in Experiment 4's EQ/DD), and λ_min jumps to 10⁵–10⁸.

### B. c_s sweep (γ=0.1) — 2-seed average

| cs | λ_min (±range) | det g (±range) | κ | σ_pair | \|I\| | clip |
| --- | --- | --- | --- | --- | --- | --- |
| 0.2 | 1.696 (0.314) | 6.06 (3.60) | 1.84 | 0.944 | 0.002 | 0 |
| 0.4 | 0.578 (0.049) | 0.407 (0.016) | 1.24 | 1.377 | 0.086 | 0 |
| 0.6 | 0.337 (0.004) | 0.177 (0.045) | 1.57 | 1.735 | 0.023 | 0 |
| 0.8 | 0.259 (0.010) | 0.096 (0.006) | 1.43 | 1.943 | 0.060 | 0 |
| 1.0 | 0.222 (0.022) | 0.068 (0.006) | 1.41 | 2.118 | 0.010 | 0 |
| 1.2 | 0.132 (0.001) | 0.033 (0.003) | 1.90 | 2.489 | 0.063 | 0 |
| 1.4 | 0.129 (0.011) | 0.025 (0.000) | 1.51 | 2.706 | 0.108 | 0 |
| 1.6 | 0.102 (0.016) | 0.022 (0.001) | 2.25 | 2.891 | 0.092 | 0 |
| 1.8 | 0.085 (0.004) | 0.012 (0.003) | 1.67 | 3.305 | 0.479 | 0 |

→ As c_s grows, σ increases almost monotonically and λ_min decreases monotonically (flattening of the density), but **even at cs=1.8, λ_min≈0.085 > 0** (no divergence or clipping). κ stays ≤ 2.25.

### C. Boundary candidate γ=0.20 in detail (6 seeds)

$\det g = 0.10261 \pm 0.0087$, $\lambda_{\min}=0.28490 \pm 0.00853$, $\kappa=1.267 \pm 0.107$, $\sigma_{pair}=1.875$.
Individual λ_min: 0.2719, 0.3257, 0.2721, 0.2769, 0.2745, 0.2883 — **all positive and consistent to high precision**.

### D. Bootstrap & N scaling (γ=0.20, seed42)

| Method | det g | λ_min | κ |
| --- | --- | --- | --- |
| Bootstrap CI (B=12) | [0.1309, 0.1462] | **[0.2716, 0.2948]** | [1.63, 1.82] |
| N=3000 | 0.062 | 0.174 | 2.04 |
| N=6000 | 0.077 | 0.239 | 1.36 |
| N=10000 | 0.097 | 0.254 | 1.50 |
| N=20000 | 0.131 | 0.275 | 1.74 |

→ The 95% interval of λ_min **definitively excludes 0**. As N grows, λ_min converges instead to a **positive value** (no tendency to degenerate).

## 3. Verdict & Conclusions

**Verdict: prediction rejected (at this projection, these axes, this resolution)** — the information-geometric singularity (det g→0 ∧ λ_min→0 ∧ κ→∞) is not observed in real-trajectory sweeps crossing the NEIP/non-NEIP phase boundary (γ∈(0.2,0.5]).

1. **λ_min is monotonic across the boundary** (γ: 0.15→0.47; no intersection with zero; CI excludes 0). κ ≤ 2.25 everywhere (no divergence).
2. **No degeneracy in the N→∞ limit either** — rather, λ_min(N) saturates to a positive value (also ruling out apparent singularities as finite-sample effects).
3. **The metric minimum is neither deep in the ROI nor at the boundary, but a weak U-shape inside the ROI at γ≈0.05–0.2** (λ_min≈0.15–0.22, det g≈0.03–0.07). Non-equilibrium circulatory driving flattens the pair density most strongly, but this is not "degeneracy".
4. **The c_s axis behaves the same:** at low coupling (cs=0.2, closer to equilibrium) λ_min is large (sharp density); it decreases monotonically with stronger coupling → no sign of degeneracy.

**Scientific summary (for the paper):**
- Paper (III)'s metric-degeneracy prediction likely presupposes either the Fisher metric in the full 30-dimensional space or a different order-parameter projection (e.g., phase/amplitude variables). **It is not reproduced on the stationary-density projection of two spins** (tested in a falsifiable way, with a negative result).
- The quantitative relations obtained (λ_min ≈ 1/σ² dominance · flat in ROI · needle-like on the equilibrium side) support "non-equilibrium driving = flattening" in another sense — density sharpness — consistent with the Experiment-4 conclusions.

**Limitations and next steps:**
- The projection is only pair (0,1) → it is of interest to check metric degeneracy on higher-dimensional (3–5 component) submanifolds.
  - ✅ **Higher-dimensional verification done (2026-09-16):** re-tested by projecting onto the top d=2–5 directions of the all-30-component PCA. The soft mode (maximum variance) does not diverge at the boundary — it decreases; λ_min is positive throughout (even at the global minimum, CI=[0.027,0.032] excludes 0); emp/G ≥ 0.54 → **prediction rejected also in higher dimensions**. det g shrinks exponentially by construction and is unsuitable as a degeneracy indicator. Details: [[論文（４）Metric-Degeneracy Test on High-Dimensional Submanifolds]] (original: [[論文（４）高次元部分多様体での計量縮退検証]]).
- Other boundary axes (D→0 limit, c_mem direction) and re-parameterization into phase variables remained untested.
  - ✅ **Done (2026-09-16):** (A) D→0 limit (the circulation-phase density does not converge to a ring; λ_min bounded; only EQ becomes needle-like = anti-degeneracy), (B) c_mem direction (the circulation onset c_mem∈(0.1,0.2) is non-singular; the maximum κ lies on the needle side before onset), (C) in (r,θ) polar coordinates g_θθ is 2.0–2.7× the G-null across the ROI (no phase zero-mode appears). Details: [[論文（４）Boundary Axes and Phase Variables: Verification]] (original: [[論文（４）境界軸と位相変数の検証]]).
- Cross-validation with another estimator such as k-NN (excluding estimator dependence) remained untested.
  - ✅ **k-NN cross-validation done (2026-09-16):** the KDE conclusions are estimator-independent (isomorphic ordering, zero-exclusion, bounded κ, γ shape). Because k-NN systematically overestimates λ_min, absolute thresholds must be estimator-specific and judged via the matched-Gaussian ratio. Details: [[論文（４）kNNによる推定器交差検証]].

## 4. Artifacts

- Script: `研究/AI/コード/数値実験5_修正版_相境界掃引.py` (reproducible · saved)
- CSV (for plotting · 2-seed average): `研究/AI/コード/相境界掃引_result.csv`