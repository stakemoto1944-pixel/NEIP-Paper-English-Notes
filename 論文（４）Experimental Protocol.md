---
作成日時: 2026-09-17T21:23:00
最終更新日時: 2026-09-17T21:30:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

To reproduce the three-stage protocol in practice: (1) fast-extract the ROI with the band-integrated imaginary-part cross-spectrum over all pairs; (2) confirm causality and robustness via asymmetric-gain interventions (sweep, step) with a symmetric-gain negative control and noise sweeps; and (3) estimate the stationary density by KDE, evaluate the Fisher information metric, remove finite-sample false positives with bootstrap and subsampling, and make the final diagnosis with a degeneracy verdict (or its rejection) plus the sharpness indicator.

> English version of the Japanese note [[論文（４）実験手順書]].

# Experimental Protocol (Step by Step)

## 0. Preliminary Setup (Common)

### 0.1 Confirming the objective and prerequisites

Objective: to identify **NEIP (non-equilibrium integrated phases)** in a data-driven way, performing "candidate extraction → causal confirmation → geometric verification" in three stages (spectral → intervention → information geometry).

Prerequisites (the strongest assumptions of this paper)

**Quasi-stationarity**, **full state observation**, and **interventional capability**.

### 0.2 Data acquisition / simulation settings (per the paper's test-bed)

Target: corrected asymmetric spin-glass SPDE (**N=30**).

Representative setting (example): **D=0.05, T=300, burn-in=100, Δt=5×10⁻⁴** (paper default).

Implementation-soundness checks (mandatory)

**Zero clip events** (the old implementation produced artificial signals because of clipping).

**Δt convergence** (statistics agree as Δt is varied along a fixed noise path).

**Stationarity** (first-half/second-half statistics agree after burn-in).

### 0.3 Recording & reproducibility rules

Random numbers: fixed seeds (averages and SEs over multiple seeds).

Output storage: log all of parameters, time series, FFT settings, KDE bandwidth, bootstrap settings, and subsample size N.

## 1. Phase 1: Fast Spectral Screening (ROI Extraction)

### 1.1 Input

Time series $x_i(t)$ of each component (i=1..N) (after burn-in removal).

### 1.2 Cross-spectrum computation (all pairs)

**1.** Enumerate all $N(N-1)/2$ pairs (**435 pairs** for N=30).

**2.** Estimate the cross-spectrum $S_{ij}(f)$ for each pair (i,j) by Welch's method (segmentation + overlap).

**3.** Set the band $(f_1,f_2)=(0.5,5.0)\,\mathrm{Hz}$ and compute the indicator

$$I_{ij}=\int_{f_1}^{f_2}\mathrm{Im}[S_{ij}(f)]\,df$$

### 1.3 Null-hypothesis calibration and significant-pair judgment

**1.** Null: generate **variance-matched iid white noise** and estimate $I_{ij}^{(\mathrm{null})}$ with the same procedure (average of 5 trials).

**2.** Threshold: use the **99th percentile** of the null distribution.

**3.** Additional condition: require **sign consistency > 0.85** (the sign is stable across segments etc.).

**4.** If there is **at least one significant pair**, set **ROI flag = True**.

### 1.4 Outputs (Phase-1 artifacts)

ROI flag (True/False)

Significant-pair set (which (i,j) passed)

Representative values: max|I|, significant-pair rate (for comparison in later phases)

## 2. Phase 2: Causal Confirmation by Intervention (Driving-Dependence Verification)

Centering on the conditions where Phase 1 gave an ROI (e.g., weak damping, long memory), verify by intervention that "the circulation follows the driving".

### 2.1 Driving-gain sweep (asymmetric term)

**1.** Under identical conditions, run with the **asymmetric gain $\lambda_{asym}$** set at several points (e.g., range 0–1.5).

**2.** Recompute the Phase-1 indicator (e.g., mean|I| or max|I|) at each $\lambda_{asym}$.

**3.** Test: statistically test whether |I| increases with $\lambda_{asym}$ (slope, elasticity, etc.).

### 2.2 Negative control (symmetric term)

**1.** Similarly sweep the **symmetric gain $\lambda_{sym}$**.

**2.** Expectation: the circulation indicator does not respond (p not significant).

### 2.3 Step intervention (same seed, same noise path)

**1.** With the same seed (same J, same noise path), switch $\lambda_{asym}$ from **OFF:0 → ON:1**.

**2.** Test whether max|I| (or the ROI flag) changes before/after the intervention, using a **paired t-test**.

**3.** Expectation: $\int \mathrm{Im}[S]\approx 0$ when OFF, and it increases significantly when ON.

### 2.4 Noise sweep (robustness)

**1.** Sweep the thermal-noise intensity **D** over a range (e.g., 0.01–0.2) and check that the ROI verdict is maintained.

**2.** Expectation: ROI=True over a wide D range (plateau-like robustness).

### 2.5 Phase-2 pass criteria (operational)

The indicator changes significantly with $\lambda_{asym}$ and does not change with $\lambda_{sym}$ (causality + control).

A clear switch appears under the step intervention under identical conditions.

Detection is maintained in the D sweep (not excessively noise-dependent).

## 3. Phase 3: Information-Geometric Verification (KDE–Fisher + Calibration)

### 3.1 Target pair and data

**1.** Choose a strong pair from Phase 1 (e.g., the pair with the largest max|I|) and build the stationary samples (x,y)=(x_i,x_j) (after burn-in removal).

**2.** Prepare several sample sizes n (e.g., **N∈{3000,…,20000}**) to examine the scaling.

### 3.2 Density estimation (2D Gaussian KDE)

**1.** Estimate $\hat{\rho}(x,y)$ with a 2D Gaussian KDE (bandwidth by **Scott's rule**, plus an h_scale sweep).

**2.** Obtain the score $\nabla \ln \hat{\rho}$ by numerical differentiation (central differences **δ=10⁻⁴**, density lower bound **ε=10⁻¹⁰**).

### 3.3 Fisher information metric estimation

**1.** Estimate the Fisher metric

$$g_{ij}=\mathbb{E}[\ \partial_i\ln\hat{\rho}\ \partial_j\ln\hat{\rho}\ ]$$

**2.** Compute the indicators: **det g**, **minimum eigenvalue λ_min**, **condition number κ=λ_max/λ_min**.

### 3.4 Degeneracy judgment logic (with false-positive countermeasures)

**1.** Declare degeneracy **only** when **det g→0 ∧ λ_min→0 ∧ κ→∞** hold simultaneously.

**2.** Non-degeneracy verdict: even at the most stringent point, **the bootstrap 95% CI of λ_min excludes zero (CI > 0)**, and κ remains of O(1).

**3.** Obtain CIs with **bootstrap** (e.g., B=12) and check the n-dependence by **subsampling**, removing finite-sample spurious singularities.

### 3.5 Calibration (matched Gaussian) and the "density sharpness" diagnostic

**1.** Build a **matched-Gaussian null** with the same covariance and compare its λ_min (matched-Gaussian ratio).

**2.** Use this as the **NEIP discriminator (density sharpness)**: ROI is "flat, nearly Gaussian", while equilibrium/overdamped regions are "needle-like", deviating by orders of magnitude.

### 3.6 Estimator cross-check (optional but recommended)

**1.** In addition to KDE, compute with a k-NN density estimator and check that the ordering and order-of-magnitude structure of the results agree (because estimator bias exists, **absolute thresholds are calibration-dependent**).

## 4. Final Report (Deliverables Checklist)

Phase 1: ROI presence/absence, list of significant pairs, (f1, f2), Welch settings, null-calibration procedure.

Phase 2: λ_asym-sweep response, λ_sym negative-control results, paired t-test of the step intervention, D-sweep robustness.

Phase 3: KDE settings (bandwidth, δ, ε), λ_min/det g/κ, bootstrap CI, subsample-n dependence, matched-Gaussian ratio (sharpness).

### Implementation cautions (common failure points)

**Skipping the continuous-limit checks (Δt convergence, clip statistics, stationarity)** can pick up artificial signals (a real case in the old implementation).

**Short series / coarse sampling** simultaneously degrade both the Welch resolution and the KDE accuracy, and an inoperable region can arise.

det g can decay exponentially with dimension, so comparisons should also use λ_min and the matched-Gaussian ratio (a lesson of the paper).