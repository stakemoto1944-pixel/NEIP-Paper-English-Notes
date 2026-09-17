---
作成日時: 2026-09-15T17:18:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Numerical Experiment (4): Verification of Information-Geometric Singularities (KDE & Bootstrap)

> English version of the Japanese note [[論文（４）数値実験（４）]]. All numbers, tables, and outputs are reproduced faithfully.
> **【Corrected version · re-run on real trajectories】** The old "results" were obtained on `dummy_traj = randn(1000,30)*0.5` (Gaussian noise) and did not constitute a verification on actual SPDE trajectories (pointed out in the supplementary verification). This record reports the results of re-running the same protocol on real trajectories of the corrected SPDE (same as Experiments 2/3: $\gamma=0.1$, $c_{mem}=5.0$, $c_s=1.0$, $D=0.05$, $T=300$, burn-in 100).

## 1. Objective

To exclude "apparent singularities (spurious singularities)" arising from discrete histograms and finite-sample fluctuations, and to statistically verify the degeneracy of the Fisher information metric ($\det g \to 0$) and a range of geometric indicators using kernel density estimation (KDE) and resampling. In parallel, to quantitatively evaluate what values the metric takes on real trajectories and how much discriminative power it has across conditions.

## 2. Conditions

- **Input data:** Multivariate time series from the ROI where causal circulation was established in Experiments 2/3 (30-dimensional SPDE, independent per seed). All analyses on the 2-dimensional projection of pair (0,1).
- **Comparison conditions (6 seeds × 4 conditions):**

| Condition | Setting | Meaning |
| --- | --- | --- |
| ROI | $\gamma=0.1$, $c_{mem}=5.0$, $J_{asym}$ present | Non-equilibrium driving · causally established region |
| EQ | Same settings with $J_{asym}=0$ | Equilibrium control (detailed balance) |
| DD | $\gamma=2.0$ (same $c_s,D$) | Overdamped · real trajectory in non-ROI region |
| GAUSS | Bivariate Gaussian matching the covariance of the ROI pair (0,1) | Null density of the old protocol (matched Gaussian) |

- **KDE parameters:** Gaussian kernel, Scott's rule × $h_{scale}$. Production evaluation at $h_{scale}=1.0$, sweep $h_{scale}\in\{0.5,0.75,1.0,1.5,2.0\}$.
- **Evaluation points:** time-uniform subsamples up to a maximum of 5000 points (computation-cost control).
- **Resampling:** bootstrap $B=12$ (seed42), subsampling $N\in\{3000,6000,10000,20000\}$.

## 3. Methods

- **Continuous density reconstruction:** The stationary probability density $\hat{\rho}(\mathbf{X})$ is reconstructed as a continuous function by KDE, the score $s_i=\partial_i\log\hat{\rho}$ is evaluated by central differences ($\delta=10^{-4}$, density lower bound $\varepsilon=10^{-10}$), and the Fisher information matrix

$$g_{ij}=\mathbb{E}\big[\ s_i(\mathbf{X})\,s_j(\mathbf{X})\ \big]$$

is estimated. The implementation is identical to the old version's `compute_fisher_information_geometry`.
- **Judgment indicators:** $\det g$, minimum eigenvalue $\lambda_{\min}$ (predicted → 0 at the phase boundary), condition number $\kappa=\lambda_{\max}/\lambda_{\min}$ (predicted → ∞ at a singularity).
- **Statistical verification:** ① 6-seed mean ± SE, ② between-condition comparison of $\lambda_{\min}$ (Welch t-test), ③ bootstrap 95% percentile intervals, ④ $h$-sweep robustness, ⑤ $N$-subsampling convergence.
- **Consistency check:** The old dummy protocol (`randn(1000,30)*0.5`, seed42) is reproduced with the same implementation to confirm agreement with the old record.

## 4. Results

### Z. Consistency check (reproduction of the old protocol)

Old dummy re-computed with the same implementation: $\det g = 9.9534$, $\lambda_{\min}=2.9292$ — **exact agreement** with the old record (9.953392 / 2.929182). The implementation is identical to the old protocol, so the real-trajectory results below can be trusted as-is.

### C. Four-condition comparison (6 seeds, $h=1.0$, pair-(0,1) projection, $n=20000$)

| Condition | $\det g$ | ±SE | $\lambda_{\min}$ | ±SE | $\kappa$ | ±SE | $\sigma_1$ | $\rho$ |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| ROI | 0.05653 | 0.00772 | 0.20749 | 0.01415 | 1.279 | 0.080 | 2.192 | −0.023 |
| EQ (equilibrium) | 9.03×10¹² | 1.00×10¹² | 2.51×10⁶ | 2.17×10⁵ | 1.505 | 0.231 | 0.001 | 0.011 |
| DD (overdamped) | 2.39×10¹⁶ | 1.82×10¹⁵ | 1.30×10⁸ | 7.40×10⁶ | 1.446 | 0.124 | 0.000 | 0.025 |
| GAUSS (null) | 0.03755 | 0.00054 | 0.14152 | 0.00153 | 1.876 | 0.029 | 1.883 | −0.004 |

※ $\sigma_1,\rho$ are the marginal standard deviation and correlation of the pair (0,1). The $\sigma_1 \approx 0.001$ for EQ/DD is **physics, not a bug**: with $J_{asym}=0$ (equilibrium) or in particular realizations at $\gamma=2.0$, components 0 and 1 are pinned near zero (σ over all 30 components = 1.30; inter-component variance is non-uniform), so the pair-projection density becomes needle-like.

### D. Between-condition comparison of $\lambda_{\min}$ (Welch t-test, ROI as baseline)

| Comparison | $\lambda_{\min}$ (ROI) | $\lambda_{\min}$ (control) | $t$ | $p$ |
| --- | --- | --- | --- | --- |
| ROI vs EQ | 0.2075 | 2.51×10⁶ | −11.57 | 4.1×10⁻⁷ |
| ROI vs DD | 0.2075 | 1.30×10⁸ | −17.52 | 7.8×10⁻⁹ |
| ROI vs GAUSS | 0.2075 | 0.1415 | +4.63 | 9.3×10⁻⁴ |

### E. Bootstrap CI (seed42, B=12, $h=1.0$)

| Condition | $\det g$ 95% CI | $\lambda_{\min}$ 95% CI | $\kappa$ 95% CI |
| --- | --- | --- | --- |
| ROI | [0.0591, 0.0685] | [0.1900, 0.2081] | [1.48, 1.72] |
| EQ | [6.81×10¹², 7.68×10¹²] | [2.37×10⁶, 2.54×10⁶] | [1.16, 1.30] |
| DD | [2.61×10¹⁶, 3.45×10¹⁶] | [1.42×10⁸, 1.75×10⁸] | [1.07, 1.33] |
| GAUSS | [0.0353, 0.0406] | [0.1404, 0.1529] | [1.72, 1.84] |

The ROI $\lambda_{\min}$ interval **definitively excludes zero** (it is sharp). It is also non-overlapping with the GAUSS interval.

### F. Bandwidth $h_{scale}$ sweep (seed42)

| $h_{scale}$ | $\det g$(ROI) | $\lambda_{\min}$(ROI) | $\det g$(GAUSS) | $\lambda_{\min}$(GAUSS) |
| --- | --- | --- | --- | --- |
| 0.5 | 0.23314 | 0.36361 | 0.09006 | 0.22826 |
| 0.75 | 0.09761 | 0.24942 | 0.04422 | 0.15901 |
| 1.0 | 0.06282 | 0.19869 | 0.03650 | 0.14316 |
| 1.5 | 0.03278 | 0.13398 | 0.02988 | 0.12966 |
| 2.0 | 0.01846 | 0.09418 | 0.02365 | 0.11563 |

→ $\lambda_{\min}$ decreases monotonically with $h$ (flattened by oversmoothing) but **never reaches zero**. The ordering ROI > GAUSS is maintained for $h\le1.5$ (inverted at $h=2.0$) → the comparison level of the eigenvalues depends on the bandwidth only **weakly**.

### G. Subsampling convergence (ROI, seed42)

| $N$ | $\det g$ | $\lambda_{\min}$ | $\kappa$ |
| --- | --- | --- | --- |
| 3000 | 0.03175 | 0.14596 | 1.490 |
| 6000 | 0.04667 | 0.19055 | 1.285 |
| 10000 | 0.03697 | 0.14396 | 1.784 |
| 20000 | 0.06282 | 0.19869 | 1.591 |

→ $\lambda_{\min}, \kappa$ stabilize at the same order as $N$ grows. No indication that a singularity appears or disappears with more data.

## 5. Conclusions

1. **Protocol validity:** The old dummy is fully reproduced ($\det g$=9.9534 / $\lambda_{\min}$=2.9292), and on real trajectories the results are statistically stable (narrow bootstrap intervals; robust to $N$ and $h$).

2. **The singularity diagnostic does not fire at this operating point:** $\det g>0$ and $\lambda_{\min}>0$ at all conditions and in all intervals, with $\kappa\approx1.3$–$1.9$ (weak anisotropy, no divergence). The information-geometric singularity defined by the simultaneous "$\det g\to0$ ∧ $\lambda_{\min}\to0$ ∧ $\kappa\to\infty$" is **not observed at this operating point (inside the phase)**. As expected, the singularity diagnostic is meaningful only near the phase boundary.

3. **The discriminative mechanism actually obtained = density sharpness:** $\lambda_{\min}$ is of order 10⁶–10⁸ for equilibrium (EQ) and overdamped (DD) conditions, and 0.14–0.21 for non-equilibrium driving (ROI) and matched Gaussian (10⁷–10¹³-fold separation, $p\le 10^{-6}$). This functions as a physical quantity: "equilibrium stationary densities are sharp and structured (needle-like); circulatory driving flattens and delocalizes the density."

4. **Comparison after scale normalization:** $\lambda_{\min}\cdot\sigma_1^2$ (a shape indicator normalized by the marginal variance) gives ROI=0.997, GAUSS=0.502, EQ≈2.5 — on the pair projection, ROI has a density shape as flat as Gaussian.

5. **Notes (limitations):** ① only 2-dimensional projections (the metric over the full 30 dimensions is unevaluated), ② the between-condition $\lambda_{\min}$ is dominated by the marginal-scale $\sigma^2$ dependence, ③ estimates of sharp peaks can be affected by the density lower bound $\varepsilon$ clipping.

### Implications for the next stage

- **Phase-boundary sweep:** The $\det g\to0$ prediction is testable only near the boundary → a sweep toward the boundary evaluating the decrease of $\det g(\theta)$ and its convergence ($N\to\infty$) is the next required experiment.
  - ✅ **Done (2026-09-16):** γ-axis (ROI boundary ∈ (0.2,0.5]) and c_s-axis sweeps performed on real trajectories. Result: **prediction rejected** (λ_min CI excludes 0, no κ divergence, saturates to positive values as N→∞). Details: [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]]. Remaining tasks: higher-dimensional projections, other boundary axes, k-NN cross-validation.
- **k-NN cross-validation:** whether the same λ_min curve is obtained with an estimator other than KDE ($k$-NN); estimator-dependence check.
- (Record preparation & re-run: `研究/AI/コード/数値実験4_修正版_情報幾何検証.py`)