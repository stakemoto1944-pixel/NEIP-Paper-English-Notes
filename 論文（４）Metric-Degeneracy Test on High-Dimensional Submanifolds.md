---
作成日時: 2026-09-16T15:50:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Metric-Degeneracy Test on High-Dimensional Submanifolds (d = 2–5)

> English version of the Japanese note [[論文（４）高次元部分多様体での計量縮退検証]]. All numbers, tables, and links are reproduced faithfully.
> Addresses remaining task ① of [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]].
> **Objective:** to re-test the metric-degeneracy prediction (det g → 0), whose verification on 2-dimensional pair-(0,1) projections was flagged as a limitation in Experiments 4–6, without that constraint. A 2D projection can miss the degeneracy structure "when the soft mode (critical direction) is not in components (0,1)", so we project onto **submanifolds spanned by the top-d directions from the eigen-decomposition (PCA) of the empirical covariance of all 30 components** and inspect Fisher-metric degeneracy for each of d = 2, 3, 4, 5.

## 1. Methods

- Same SPDE (c_mem=5.0, D=0.05, T=300, burn-in 100, J_asym present), 2 seeds (42, 100).
- γ∈{0.05, 0.1, 0.2, 0.3, 0.5, 1.0} (ROI deep → boundary band (0.2, 0.5] → non-ROI).
- At each point: eigen-decomposition of the all-30-component covariance → projection onto the top-d eigenvectors → d-dimensional KDE–Fisher metric (central differences δ=1e-4, ρ lower bound 1e-10, Scott's rule).
- Simultaneous monitoring:
  - **Soft-mode monitoring** λ₁(Σ30) (maximum variance). If critical, it should diverge at the boundary (1/λ₁ = λ_min^G for a Gaussian).
  - **Estimator baseline:** matched-Gaussian samples with the same PCA covariance passed through the same KDE pipeline → ratio λ_min(emp)/λ_min(G).
- Bootstrap (B=12, seed42) at the boundary candidate γ=0.2 (d=3) and the global minimum γ=0.05 (d=5).

## 2. Results

### A. Soft-mode monitoring and λ_min(γ, d) (2-seed average)

| γ | λ₁(Σ30) | 1/λ₁ (=λminᴳ) | d=2 | d=3 | d=4 | d=5 | κ: d=2→5 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 0.05 | 12.88 | 0.0776 | 0.0758 | 0.0640 | 0.0470 | 0.0287 | 1.07→1.39 |
| 0.1 | 9.35 | 0.1069 | 0.107 | 0.0913 | 0.0650 | 0.0389 | 1.06→1.37 |
| 0.2 | 7.07 | 0.1415 | 0.143 | 0.121 | 0.0787 | 0.0465 | 1.07→1.53 |
| 0.3 | 6.21 | 0.1611 | 0.174 | 0.140 | 0.0912 | 0.0504 | 1.09→1.59 |
| 0.5 | 5.18 | 0.1932 | 0.198 | 0.161 | 0.103 | 0.0532 | 1.28→1.72 |
| 1.0 | ≈0 (fully pinned) | 1.3×10⁷ | 1.4×10⁷ | 2.7×10⁶ | 6.9×10⁵ | 1.8×10⁵ | 1.29→2.18 |

→ **The soft mode (λ₁) does not diverge at the boundary; it actually decreases** (deep 12.9 → boundary band 6–7 → 5.2 at γ=0.5; boundary/deep ratio 0.57). λ_min is **monotonically increasing in γ for all d**: the minimum is not at the boundary but deep in the ROI (γ=0.05, d=5), where λ_min = 0.029. At γ=1.0 all components freeze near zero and λ_min jumps to 10⁵–10⁷ (the same needle phase as Experiments 4–6).

### B. Matched-Gaussian ratio (d=3, 5)

| γ | d=3 ratio emp/G | d=5 ratio emp/G |
| --- | --- | --- |
| 0.1 | 1.00 | 0.73 |
| 0.2 | 1.02 | 0.65 |
| 0.5 | 0.96 | 0.54 |

→ At d=3 the measurement agrees with the Gaussian; **at d=5 the measurement is 30–45% flatter than the Gaussian** (non-Gaussian structure with heavy tails / flat top along the top PCA directions) — however, the "flattening" is at most 2-fold, far from degeneracy (→0).

### C. Bootstrap CI (B=12)

| Point | λ_min CI | det g CI | κ CI |
| --- | --- | --- | --- |
| γ=0.2, d=3 | **[0.104, 0.118]** | [1.8×10⁻³, 2.2×10⁻³] | [1.26, 1.46] |
| γ=0.05, d=5 (global minimum) | **[0.0270, 0.0315]** | [4.4×10⁻⁸, 6.2×10⁻⁸] | [1.36, 1.62] |

Even at the global minimum, the 95% interval of λ_min **clearly excludes 0**.

## 3. Verdict and Conclusions

**Verdict: prediction rejected (also in higher dimensions)** — the information-geometric metric degeneracy (det g→0 ∧ λ_min→0 ∧ κ→∞) does not occur on PCA submanifolds (d=2–5), nor at the phase boundary (γ∈[0.2,0.5]).

1. **The soft mode is not at the boundary:** the maximum variance λ₁(Σ30) is largest deep in the ROI (13), decreases at the boundary (5–7), ratio 0.57. The critical-point hallmark "divergence of order-parameter fluctuations" is not observed.
2. **λ_min is positive throughout:** at every (γ, d), λ_min > 0.027 and the CI excludes 0. It increases monotonically with γ (the flattest density is deep in the ROI).
3. **κ increases mildly with d** (1.1→1.7) but remains bounded. No divergence.
4. **Methodological note (for the paper):** det g decays exponentially in d (det = Π λ_i), so "small det g" is a dimensional effect, not evidence of degeneracy. **The dimension-invariant degeneracy indicators are λ_min(g_d) and the matched-Gaussian ratio**, and this experiment's emp/G ≥ 0.54 shows no approach to degeneracy.

**Positioning:** it has been established, in a falsifiable way and across three independent experiments (④ 2D KDE, ⑤ γ/c_s sweep, ⑥ k-NN, ⑦ high-dimensional PCA), that Paper (III)'s metric-degeneracy prediction does not fire in this system (non-Markovian memory spin-glass SPDE) on any finite-dimensional submanifold of real trajectories (2–5 components, PCA-optimal basis). The theory side is thereby prompted to reconsider "the assumptions under which the prediction holds" (e.g., the phase boundary existing on a different parameter axis, a metric structure requiring the full 30 dimensions, or a re-parameterization in phase variables).

## 4. Artifacts

- Script: `研究/AI/コード/数値実験7_修正版_高次元計量検証.py`
- CSV (for plotting): `研究/AI/コード/高次元計量_result.csv`
- Related: [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]] (remaining task ① ✅), [[論文（４）kNNによる推定器交差検証]], [[論文（４）Numerical Experiments (4)]]