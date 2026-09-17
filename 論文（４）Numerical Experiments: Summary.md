---
作成日時: 2026-09-16T17:00:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Paper 論文（４）: Numerical Experiments — Summary (Corrected Version, Experiments 1–8)

> English version of the Japanese note [[論文（４）数値実験の総括]]. All numbers, tables, and links are reproduced faithfully.
> Integrated index of the records of numerical experiments 1–8 (corrected version) carried out on 2026-09-15〜16. For individual details and raw data, see each linked note. The final code is collected in `研究/AI/コード/`.

## （1）Background

- **Target:** Data-driven hybrid screening verification of non-equilibrium integrated phases (NEIP). The model is an asymmetric spin-glass SPDE (asymmetric coupling = solenoidal-flow driving + non-Markovian memory + thermal noise, 30 components).
- **Proposed framework:** ① spectral imaginary-part screening (Phase-1 fast extraction) → ② external intervention & noise sweep (causal identification) → ③ information-geometric singularity verification (KDE + bootstrap).
- **Theoretical prediction (Paper III):** the Fisher metric degenerates at the NEIP phase boundary ($\det g \to 0 \wedge \lambda_{\min} \to 0 \wedge \kappa \to \infty$).
- **Origin of the problem:** The old implementation (2026-09-15) had two inconsistencies in the update equation — a **sign flip and a missing dt** — and sticking at the clip boundary ±10 produced an artificial cross-spectrum (−0.00890 / ROI=True). The supplementary verification proved it a numerical artifact → Experiments 1–5 were **fully re-run with the corrected version**, and the follow-up experiments (6–8) were also run with the corrected version.

## （2）Objective

1. Confirm the soundness of the numerical foundation of the corrected SPDE (Experiment 1: stability, dt convergence, stationarity, clip-free).
2. Identify the ROI in parameter space while excluding false positives (Experiment 2: Phase 1).
3. Causally establish that the detected circulation is due to true solenoidal driving, not passive delay or non-Gaussian noise (Experiment 3).
4. Test Paper (III)'s information-geometric prediction (det g → 0) in a falsifiable form on real trajectories:
   - phase-boundary sweep (γ · c_s axes) 【Experiment 5】
   - high-dimensional submanifolds (top-d = 2–5 PCA) 【Experiment 7】
   - estimator independence (k-NN) 【Experiment 6】
   - other boundary axes (D→0, c_mem) and phase-variable (r,θ) re-parameterization 【Experiment 8】

## （3）Methods

**Common dynamics (corrected version):** $\xi_i = \psi^3 - \psi - J_{sym}\psi - J_{asym}\psi$, $Q += (-\gamma Q + c_{mem}\xi)dt$, $\psi += (-Q + \sqrt{2D\,dt}\,\eta)\,dt$. Base settings $c_{mem}=5.0$, $D=0.05$, $T=300$, burn-in 100, $J_{asym}$ present.

| Experiment | Method highlights |
| --- | --- |
| 1 | Stability of Euler–Maruyama integration · clip count · double-well exploration · dt convergence (common noise path) · initial-condition probe · stationarity |
| 2 | Cross-spectral imaginary-part band integral over all 435 pairs $I_{ij}=\int_{[0.5,5]\mathrm{Hz}}\mathrm{Im}[S_{ij}]df$. Significant pair: 99th percentile of σ-matched iid null + sign consistency > 0.85; ROI=True if ≥1 pair. γ×c_mem and coupling×D grid search, 6-seed robustness |
| 3 | J_asym gain λ sweep (driving intervention) + J_sym gain sweep (negative control) + step intervention (paired, same-seed t-test) + noise D sweep (plateau verification) |
| 4 | Pair-(0,1) 2D KDE–Fisher metric $g_{ij}=\mathbb{E}[(\partial_i\ln\rho)(\partial_j\ln\rho)]$ (central differences $\delta=10^{-4}$, density lower bound $\varepsilon=10^{-10}$, Scott's rule). 4 conditions (ROI/EQ/DD/GAUSS) × 6 seeds, bootstrap B=12, $h_{scale}$ sweep, N scaling |
| 5 | **γ sweep** (0.05–2.0, boundary ∈ (0.2,0.5]) and **c_s sweep** (0.2–1.8) with the Experiment-4 pipeline. At boundary candidates: 6 seeds + bootstrap + N scaling |
| 6 | Same data, same numerical differentiation, with **only the density estimation replaced by k-NN** ($k=700\approx n^{2/3}$, k sweep). Reproduced the analytic Gaussian-control agreement · 4 conditions · γ profile · CI |
| 7 | Projection metric onto the top $d=2,3,4,5$ PCA directions of all 30 components. **Soft-mode monitoring** $\lambda_1(\Sigma_{30})$ (Gaussian prediction $\lambda_{\min}^G=1/\lambda_1$) + matched-Gaussian estimator ratio + bootstrap at the minimum point |
| 8 | **D→0 limit** (ROI/EQ control) × **c_mem sweep** (onset boundary) × **phase-variable re-parameterization** (polar score via chain rule $\partial_\theta = -y\partial_x + x\partial_y$, measuring $g_{\theta\theta}$). Winding-number rate, circular concentration R1, matched-Gaussian null |

## （4）Results

| Experiment | Main result |
| --- | --- |
| 1 | Corrected version: 0 clips · $\psi\in[-1.14,1.17]$ · well exploration (22.2/54.8/23.0%) · dt convergence (σ=0.669/0.671/0.672) · stationarized at T=200. However, Phase-1 signal ≈ 0 at default parameters |
| 2 | **ROI = γ≲0.2** (best γ=0.1, c_mem=5–10, max|I|≈1.3–1.6). False at γ≥0.5 (previous record's True was due to a D misconfiguration). No seed failures (max|I|=1.12–1.93) |
| 3 | J_asym step intervention: $\Delta\max|I|=+1.738\pm0.308$, **t=5.65, p=0.0024**, ROI 0→1 reproduced across all seeds. No response to the J_sym control (p=0.47). Detection robust for noise $D\in[0.01,0.2]$. The response quantity is |I| (direction realization-dependent) |
| 4 | No singularity **fires** at the operating point (inside the phase): ROI (λ_min=0.21, κ=1.28) ≪ EQ (2.5×10⁶) < DD (1.3×10⁸), GAUSS null = 0.14. λ_min robust to N and h (does not converge to zero). The discriminative mechanism is "density sharpness" |
| 5 | γ sweep: λ_min **crosses the boundary band (0.2–0.5) monotonically increasing, never approaching zero** (0.28→0.47), CI=[0.272,0.295] excludes 0, saturates to positive values as N→∞, κ≤2.3. c_s sweep likewise decreases monotonically without reaching 0. **Prediction rejected (this projection, this axis)** |
| 6 | Same island structure with k-NN: 4-condition ordering, order of magnitude, and γ curve all agree, γ=0.2 CI=[3.82,4.00]. λ_min always positive, κ≈1. **Verdict estimator-independent** (k-NN systematically overestimates λ_min → judgment must use the matched-Gaussian ratio) |
| 7 | Rejected also in higher dimensions (d=2–5): the soft mode λ₁(Σ30) **does not diverge at the boundary and is maximal deep in the ROI** (boundary/deep ratio 0.57). Even at the global minimum (γ=0.05, d=5) CI=[0.027,0.032] excludes 0. The d-dependent shrinkage of det g is a dimensional artifact (O(0.03–0.2) after det^(1/d) normalization). Matched-G ratio ≥ 0.54 |
| 8 | Also rejected in the final verification: **(a)** as D→0 the ROI density does not become ring-like and λ_min stays bounded (0.13–0.38) with g_θθ≈1.3–1.9 (only EQ becomes needle-like = anti-degeneracy λ_min→10⁹); **(b)** the c_mem onset point (∈(0.1,0.2)) is non-singular (λ_min=0.51, κ=2.0), and the maximum κ=34.7 is on the needle side **before** onset; **(c)** in (r,θ) coordinates $g_{\theta\theta}$ is 2.0–2.7× the G-null across the ROI (the phase direction is not flat; no zero mode appears) |

## （5）Conclusions

1. **The metric-degeneracy prediction is falsified (over the entire verifiable range):** across all axes (γ, c_s, D, c_mem) × all dimensions (d=2–5) × all coordinate systems (Cartesian, PCA, polar (r,θ)) × all estimators (KDE, k-NN), the singularity $\det g \to 0 \wedge \lambda_{\min}\to 0 \wedge \kappa\to\infty$ does not appear.
2. **Identity of the condition under which the prediction would hold:** $g_{\theta\theta}\to 0$ can occur only when the density is completely invariant along the phase direction (uniformly rotationally symmetric), but the non-equilibrium driving of this system breaks that symmetry, imprinting Fisher information of 1.0–1.8 along the phase direction.
3. **What is effective for NEIP detection is not "metric degeneracy" but "density sharpness":** non-equilibrium driving flattens and delocalizes the τ-density (small λ_min; ROI ≈ matched Gaussian), while equilibrium/overdamped conditions needle it (λ_min→∞, 10⁶–10⁸). The two are separated by 10⁷–10¹³-fold, so **λ_min (via its matched-Gaussian ratio) is a robust NEIP discriminator**.
4. **The existence of NEIP (circulation) is causally established:** the spectral imaginary-part band signal tracks a step intervention on J_asym with p=0.0024 and is unresponsive to the control (J_sym); it is robust to thermal noise.
5. **Methodological lessons:** (i) det g shrinks exponentially with dimension, so dimension-invariant indicators (λ_min, det^(1/d), matched-G ratio) should be reported; (ii) absolute thresholds differ per estimator (k-NN overestimates 16-fold) → the ratio to a null baseline is mandatory; (iii) verification of the continuous limit of the numerical scheme (dt convergence) and clip statistics is a precondition of any Phase-1 verdict.

## （6）Remaining tasks

1. **Theory side (Paper III) reconsideration:** the assumptions making the prediction valid (choice of projection, soft mode, phase symmetry) were found not to hold on this system's real trajectories. The theory must either identify "conditions under which the prediction applies" or propose a different boundary signature (e.g., quantification of the τ-density sharpening transition).
2. **The full 30-dimensional metric:** Experiment 7 covers PCA submanifolds (d≤5). The d=30 full-space metric is blocked by estimation error (curse of dimensionality) and is unevaluated under this protocol. Estimator extensions (k-NN, slice methods, etc.) should be considered according to the dimensionality the theory requires.
3. **Quantification of the boundary "sharpness transition":** λ_min(γ) jumps to 10⁵−10⁸ around γ≈0.5–0.75. Systematic estimation of this transition point (scaling law, finite-size effects) is not yet done.
4. **Application to real data:** toward applications such as neural time series: (i) non-stationarity, missing observations, partial observation; (ii) passive causal estimation when intervention is impossible; (iii) coarse sampling remain to be extended.
5. **Quantification of ablations:** in the framework of Record 5 (Numerical Experiment (5)), quantitative comparison of false-positive rates when each component is removed remains a working hypothesis (constructive verification is effectively guaranteed by the combination of Experiments 2–4).

## Artifacts (final-code collection)

- **Code:** only the **8 corrected/final scripts of Experiments 1–8** are collected in `研究/AI/コード/` (old-implementation code remains only inside the notes as invalid records).
  - `数値実験1_修正版.py` 〜 `数値実験8_修正版_境界軸と位相変数.py`
- **Result CSVs:** `相境界掃引_result.csv`, `高次元計量_result.csv`, `境界軸位相_result.csv`
- Individual records:
  - [[論文（４）Numerical Experiments (1)]] / [[論文（４）Numerical Experiments (2)]] / [[論文（４）Numerical Experiments (3)]] / [[論文（４）Numerical Experiments (4)]] / [[論文（４）Numerical Experiments (5)]] (originals: [[論文（４）数値実験（１）]] ほか)
  - [[論文（４）Supplementary Verification of the Numerical Experiments]] (original: [[論文（４）の数値実験の追補検証]]; full record of the old implementation's defect discovery and correction)
  - [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]] (Experiment 5; original: [[論文（４）相境界掃引による計量縮退予言の検証]])
  - [[論文（４）kNNによる推定器交差検証]] (Experiment 6)
  - [[論文（４）Metric-Degeneracy Test on High-Dimensional Submanifolds]] (Experiment 7; original: [[論文（４）高次元部分多様体での計量縮退検証]])
  - [[論文（４）Boundary Axes and Phase Variables: Verification]] (Experiment 8; original: [[論文（４）境界軸と位相変数の検証]])