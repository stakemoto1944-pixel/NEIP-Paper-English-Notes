---
作成日時: 2026-09-15T17:20:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Numerical Experiment (5): Ablation Study and Failure-Case Verification

> English version of the Japanese note [[論文（４）数値実験（５）]]. All content is reproduced faithfully.

## Record 5: Ablation study and failure-case verification

### 1. Objective

To prove logically and quantitatively why each element of the proposed hybrid screening framework (spectral imaginary-part screening, external intervention & noise sweep, KDE & bootstrap) is indispensable, and to clarify typical failure cases (negative results) where the method reaches its applicability limits, thereby guaranteeing the reliability and falsifiability of the method.

### 2. Conditions

- **Ablation experiment conditions:** Reduced models are prepared by intentionally removing major components as follows:

    1. _Ablation 1:_ Remove the spectral imaginary-part screening; perform the first-pass judgment with ordinary variance or in-phase correlation alone.

    2. _Ablation 2:_ Omit the external intervention protocol and the thermal-noise sweep; send the screening results directly to the geometric verification.

    3. _Ablation 3:_ Remove KDE and bootstrap; compute the information-geometric metric with a conventional fixed-bin-width histogram and a single-sample value.

- **Stress-test conditions (failure-case verification):**

    - Extremely short time series (insufficient sample count $N$).

    - Data containing strong non-stationarity or excessive observation noise.

### 3. Methods

- **Individual-component invalidation test:** For each ablation condition, the jump in the false-positive rate and the proliferation of spurious singularities are measured and compared across an exhaustive parameter space.
- **Failure-mode stress test:** Data violating the stationarity or sample-count assumptions are deliberately fed in, and it is tracked at which pipeline stage (Welch band integration, plateau formation, KDE convergence, etc.) the judgment breaks down, or whether an incorrect phase boundary is output.

### 4. Results

- **Result of Ablation 1:** Without the spectral imaginary-part screening, a huge number of false positives due to equilibrium fluctuations or passive delay pass through the first stage, dramatically increasing computational cost and misdetection.
- **Result of Ablation 2:** Without intervention and the noise sweep, apparent circulation due to non-Gaussian noise cannot be distinguished from true solenoidal driving, and causal validity is lost.
- **Result of Ablation 3:** With the histogram method that excludes KDE and bootstrap, "spurious singularities" proliferate due to slight changes in bin width and sampling fluctuations, making reliable phase-boundary delineation impossible.
- **Failure-case findings:** For markedly short time series or epochs without guaranteed stationarity, the reduced frequency resolution of the Welch method and the failure of KDE density estimation define a clear limitation region (Scope & Limitations) where the protocol no longer works properly.

---

These complete the five key records/protocols of the numerical experiments. Together with the logical structure of the whole series, these experimental results form a strong backbone supporting the rigor and persuasiveness of the paper.

> Note on numbering: file numbering (（1）–（5）) follows the original notes; in the consolidated summary [[論文（４）Numerical Experiments: Summary]], "Experiment 5" refers to the phase-boundary sweep (recorded in the separate note [[論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction]]), and this ablation/limitations study corresponds to "Record 5" / part of Experiments 1–3's constructive verification.