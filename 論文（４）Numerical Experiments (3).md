---
作成日時: 2026-09-15T17:17:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Numerical Experiment (3): Causal Identification and Robustness Verification (Intervention & Noise Sweep)

> English version of the Japanese note [[論文（４）数値実験（３）]]. All numbers, tables, and outputs are reproduced faithfully.

## Record 3: Causal identification and robustness verification (intervention & noise sweep)

### 1. Objective

To prove that the circulation detected in the region of interest (ROI) extracted by the Phase-1 screening is not an apparent phenomenon caused by passive delay from the non-Markovian memory kernel or by non-Gaussian noise, and to establish the causal structure and topological robustness due to true solenoidal driving ($V_{\text{sol}}$).

### 2. Conditions

- **Target data:** Time-series data at parameter points inside the ROI extracted in Phase 1.
- **External intervention parameter:** Time-dependent step-like or periodic modulation $\lambda(t)$ applied to the coupling coefficients.
- **Thermal-noise sweep range:** Noise intensity $D \in [D_{\min}, D_{\text{max}}]$ sampled at multiple levels.

### 3. Methods

- **Driving-protocol intervention test:** An external modulation $\lambda(t)$ is applied to the coupling coefficients, and the response of the band-integral quantity $\mathcal{I}_{\text{Im}}(\lambda)$ is evaluated. Whether a clear positive correlation between driving and response (partial derivative $\partial \mathcal{I}_{\text{Im}} / \partial \lambda > 0$) is observed is tested.
- **Thermal-noise sweep and plateau verification:** The change of the circulation indicator with the thermal-noise intensity $D$ is tracked, and whether the indicator forms a stable plateau (stationary region, $\partial \mathcal{I}_{\text{Im}}(D) / \partial D \approx 0$) robust to statistical fluctuation in a specific noise band is checked.

### 4. Results

- The band-integral quantity showed a consistent positive response to the external modulation, proving that the detected circulation is a "causal phenomenon" attributable to structural driving.
- The thermal-noise sweep confirmed that the circulation indicator forms a stable plateau over a certain noise band, demonstrating topological robustness against thermal fluctuations.
- False positives due to passive delay and non-Gaussian noise were fully excluded, establishing a solid foundation for the next-stage information-geometric singularity evaluation.

---

# Results (Corrected Version · Updated 2026-09-16)

**Warning: The old results below (sign-flipping, non-monotonic λ sweep) are numerical artifacts of the inconsistent implementation (sign flip and missing dt), recorded from a single realization with no statistics. They are invalidated.** Using the corrected dynamics (see [[論文（４）Numerical Experiments (1)]]), the experiment was re-run at the ROI point established in Experiment 2 (γ=0.1, c_mem=5.0, cs=1.0, D=0.05, T=300, burn-in=100) as the baseline, with **6-seed mean ± SE**.

- Script: `研究/AI/コード/数値実験3_修正版_介入ノイズ掃引.py` (saved · reproducible · deterministic)

## Supplementary verification (2026-09-16): re-run of intervention & noise sweep (definitive version)

Indicator: cross-spectral imaginary-part band integral over all 435 pairs, $I_{ij}=\int_{[0.5,5]\text{Hz}}\mathrm{Im}[S_{ij}(f)]df$. I01 = signed pair-(0,1) integral, max|I| = maximum absolute value across all pairs; ROI via σ-matched null 99th percentile + sign consistency > 0.85 (significant pairs ≥ 1).

### A.1 Driving intervention: gain λ sweep of the asymmetric coupling J_asym (J_sym=1.0 fixed)

| λ | mean(I01) ± SE | mean|I01| ± SE | mean max|I| ± SE | ROI rate | clip total |
|---|---|---|---|---|---|---|
| 0.00 | 0.00000 ± 0.00000 | 0.00000 ± 0.00000 | 0.00000 ± 0.00000 | 0.000 | 0 |
| 0.25 | −0.00000 ± 0.00000 | 0.00001 ± 0.00000 | 0.00019 ± 0.00011 | 0.000 | 0 |
| 0.50 | −0.01785 ± 0.02985 | 0.04450 ± 0.02364 | 0.29591 ± 0.01675 | 1.000 | 0 |
| 1.00 | −0.05732 ± 0.05411 | 0.11818 ± 0.02814 | 1.73824 ± 0.30777 | 1.000 | 0 |
| 1.50 | −0.32626 ± 0.23617 | 0.38404 ± 0.21810 | 2.22098 ± 0.22837 | 1.000 | 0 |
| 2.00 | −1.01515 ± 1.16493 | 1.29097 ± 1.10899 | 22.08685 ± 7.53762 | 1.000 | 177,038 |

- **λ=2.0 is numerically invalid due to sticking at the clip boundary (total 177,038 hits).** Evaluation is restricted to the clip-free range λ∈[0, 1.5].
- Response tests (λ∈[0,1.5], 5 points):
  - mean|I01| regression slope = 0.249 ± 0.056, **p = 0.021**, r = 0.931
  - mean max|I| regression slope = 1.691 ± 0.251, **p = 0.007**, r = 0.968
  - log-log elasticity |I01| ∝ λ^1.9 (superlinear response)
- **The signed I01 has a negative mean, but for λ≤1.5 |mean| < 1.4×SE (not significant).** The circulation direction is almost random across quenched J_asym realizations (sign-stable realizations: 1/6). → **The correct causal-response statistic is the |I|-magnitude response**, and the old record's claim of "signed-integral responsiveness" is corrected to "positive magnitude response".

### A.2 Control intervention: gain λ sweep of the symmetric coupling J_sym (J_asym=1.0 fixed)

| λ | mean(I01) ± SE | mean|I01| ± SE | mean max|I| ± SE | ROI rate |
|---|---|---|---|---|---|---|
| 0.0 | −0.05316 ± 0.13115 | 0.25953 ± 0.06553 | 22.19789 ± 7.25013 | 1.000 |
| 0.5 | −0.02757 ± 0.17525 | 0.34314 ± 0.08553 | 1.56916 ± 0.16887 | 1.000 |
| 1.0 | −0.05732 ± 0.05411 | 0.11818 ± 0.02814 | 1.73824 ± 0.30777 | 1.000 |
| 1.5 | −0.00214 ± 0.08218 | 0.16142 ± 0.03927 | 1.10744 ± 0.15234 | 1.000 |
| 2.0 | −0.03243 ± 0.11262 | 0.19589 ± 0.07224 | 1.02058 ± 0.02841 | 1.000 |

- λ_sym=0 (complete removal of J_sym) degenerates numerically with clipping (max|I|=22.2 is a clipping artifact) → excluded from the analysis
- **No response over λ_sym∈[0.5, 2.0]** (mean|I01| slope n.s., p=0.47; max|I| slope n.s., p=0.16)
- → The circulation indicator **tracks only the asymmetric term**; it does not respond to the strength of the symmetric coupling (causal separation confirmed by the negative control).

### A.3 Step intervention (λ_asym OFF=0 vs ON=1.0, same seeds = same J, same noise path, paired)

- Δmax|I| (ON−OFF) = +1.738 ± 0.308, **t = 5.65, p = 0.0024** (paired t-test)
- ΔI01 (signed) = −0.057 ± 0.054, t = −1.06, p = 0.34 (because the sign is realization-dependent)
- **ROI flag: OFF → False for all 6 seeds, ON → True for all 6 seeds (Δ = +1.00)**
- Note: with OFF (J_asym=0), all-pair band integrals ≈ 0.00000, consistent with the equilibrium theoretical expectation ∫Im[S] ≈ 0 for a reversible system obeying detailed balance.

→ **The Phase-1 indicator switches with paired significance upon toggling the intervention, establishing a causal response (tracking of structural driving).**

### B Noise sweep (J_asym=1.0 fixed)

| D | mean(I01) ± SE | mean|I01| ± SE | mean max|I| ± SE | ROI rate |
|---|---|---|---|---|---|---|
| 0.01 | 0.01696 ± 0.07419 | 0.14888 ± 0.03359 | 1.21196 ± 0.15345 | 1.000 |
| 0.02 | −0.13337 ± 0.10667 | 0.16614 ± 0.09703 | 2.50210 ± 1.39838 | 1.000 |
| 0.05 | −0.05732 ± 0.05411 | 0.11818 ± 0.02814 | 1.73824 ± 0.30777 | 1.000 |
| 0.10 | 0.03116 ± 0.10272 | 0.17223 ± 0.06937 | 1.19262 ± 0.06792 | 1.000 |
| 0.20 | 0.03557 ± 0.11668 | 0.17908 ± 0.08633 | 1.41403 ± 0.11545 | 1.000 |

- **ROI=True for all D (0.01–0.2, a 20-fold change)**, mean max|I| ≈ 1.2–2.5 (nearly constant except the large-SE point at D=0.02)
- The mean max|I| is almost independent of D within sample SE; detection is robust against thermal-noise intensity
- Note: because of the D=0.02 outlier (2.50 ± 1.40), it cannot be called a "strict plateau (∂I/∂D ≈ 0)"; it is expressed as "plateau-like, robust detection".

### Conclusions (causal verification)

1. **Causality established:** A step intervention on J_asym raises the Phase-1 indicator with paired significance (p=0.0024), with the ROI 0→1 switch reproduced across all seeds. No response to the control (J_sym intervention).
2. **Correction of the old claim:** The previously recorded "consistent positive response $\partial \mathcal{I}_{\text{Im}}/\partial \lambda > 0$" holds as a **positive magnitude response** $\partial |\mathcal{I}_{\text{Im}}|/\partial \lambda > 0$ (p = 0.007–0.021). The direction of the signed integral is realization-dependent (determined by the quenched J_asym).
3. **Noise robustness:** ROI detection is maintained at all D (D-robustness of the detection). → Foundation established to proceed to the next stage (Experiment 4: information-geometric verification).

---

# Results (Old Implementation · 2026-09-15) — invalidated, since the supplementary verification showed they are artifacts

```
  Running Causal Intervention Protocol...
  lambda = 0.05 -> Integrated Im[S_AB]: -0.00003
  lambda = 0.10 -> Integrated Im[S_AB]: 0.00610
  lambda = 0.20 -> Integrated Im[S_AB]: -0.01245
  lambda = 0.30 -> Integrated Im[S_AB]: 0.09543
  lambda = 0.40 -> Integrated Im[S_AB]: -0.02288
  lambda = 0.50 -> Integrated Im[S_AB]: -0.01100
```

![[Figure 1 10 1.png]]

![[Figure 1 10.png]]