---
作成日時: 2026-09-16T19:00:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Supplementary Verification of the Numerical Experiments

> English version of the Japanese note [[論文（４）の数値実験の追補検証]]. All numbers, tables, and outputs are reproduced faithfully. This note records the discovery that the old implementation's results were artifacts, the correction of Experiment 1, and the definitive re-runs of Experiments 2 and 3.

# What can be said from Numerical Experiment (1)

Based on the results of [[論文（４）Numerical Experiments (1)]] (`Simulation completed successfully without divergence!`, trajectory shape (2000, 30)), the following conclusions can be organized. *Note: this section records the original analysis written before the supplementary verification; the subsequent sections report the verification that largely overturned it.*

## 1. What this result "can say"

**(a) The numerical implementation is established as a test-bed**
- With $T=20$, $dt=0.0005$, a total of 40,000 Euler–Maruyama steps complete without NaN/Inf. It is demonstrated that an SPDE simultaneously containing asymmetric coupling ($\mathcal{J}^{\text{asym}}$: an antisymmetric matrix, so its eigenvalues are purely imaginary) + non-Markovian memory (Markov embedding via the auxiliary variable $Q$) + thermal noise $D=0.02$ can be integrated without divergence.
- `coupling_scale=0.2` combined with $1/\sqrt{N}$ scaling ($N=30$) proved sufficient to avoid divergence in this model.

**(b) The quality is sufficient as input to Phase 1**
- Save interval $\Delta t_{\text{eff}} = 0.01$ ($f_s = 100$ Hz), record length $T=20$. The Welch frequency resolution $\Delta f \approx 1/T = 0.05$ Hz is guaranteed, and the record length is sufficient for evaluation over $f \in [0.5, 5]$ Hz.
- In fact, this trajectory was used in the Phase-1 screening of [[論文（４）Numerical Experiments (2)]] and judged ROI with band integral −0.00890 ($|\cdot| > \epsilon_{th}=10^{-3}$). *(Later shown to be an artifact — see below.)*

## 2. What this result alone "cannot say" (important)

"Not diverging" is a **necessary condition, not evidence of non-equilibrium (solenoidal flow, broken detailed balance)**. By itself this result only confirms the stability of the numerical foundation. Before treating it as a result in the paper, the following unverified points must be addressed:

- **Clipping impact:** how often `np.clip(psi, -10, 10)` actually fired was not recorded. If it fired frequently, "stability" is artificial and the physical meaning of the trajectory is compromised. Clip frequency should be logged and reported numerically.
- **Single seed, single realization:** only `seed=42`. Reproducibility across multiple seeds and ensemble statistics are required.
- **Stationarity / burn-in:** whether the transient (relaxation from the initial condition $\psi \sim \mathcal{N}(0, 0.01)$) is discarded before spectral analysis is unclear. Spectral evaluation without a stationarity test is a breeding ground for false positives.
- **Time-step convergence:** Euler–Maruyama is first-order in the weak sense. A convergence test with dt halved (0.00025) should be done at least once.

**Physical concern (rough estimate):** in the fast limit of the memory kernel the effective friction is $\mu = c_{\text{mem}}/\gamma = 0.5$, so the effective noise intensity is $D_{\text{eff}} = D/\mu = 0.04$. The barrier of the double-well potential $\psi^4/4 - \psi^2/2$ is $\Delta V = 0.25$, and the Kramers-type transition rate is $\sim \exp(-\Delta V/D_{\text{eff}}) = \exp(-6.25) \approx 2\times10^{-3}$ → the expected number of inter-well transitions during $T=20$ is **≈ 0.04**. That is, at these parameters the system remains almost "fluctuations inside one well", and there is a risk that the nonlinearity degenerates into weak linear oscillations. **The signal (−0.0089) exceeded the threshold but is "weak", and whether its content is in-well fluctuation or genuinely $\mathcal{J}^{\text{asym}}$-driven circulation cannot be judged until the intervention test (Experiment 3).** It is advisable to check once the trajectory histogram (unimodal vs bimodal) and the number of transition events.

## 3. Proposed positioning in the paper

This result suffices as one paragraph at the opening of the experimental section (or in an appendix) as "validation of the numerical setup's appropriateness and stability", with the scientific claims carried by Experiments 2–5. Two caveats were found beyond that (and are shared here):

- The results of [[論文（４）Numerical Experiments (4)]] were obtained on `dummy_traj = np.random.randn(1000, 30) * 0.5` (Gaussian noise), **not on SPDE trajectories**. The $\det g = 9.95$ "no singularity" result was not a verification on real data and must be re-run on real trajectories.
- The intervention results of [[論文（４）Numerical Experiments (3)]] showed the integral flipping sign and behaving non-monotonically with $\lambda$ ($0.30 \to +0.095,\ 0.40 \to -0.023$), **disagreeing numerically** with the record's claim of "a consistent positive response $\partial \mathcal{I}_{\text{Im}}/\partial \lambda > 0$". Published as-is it would be attacked by reviewers; it should be re-displayed together with statistics (standard errors).

---

# Supplementary verification results

The supplementary verification was implemented (environment OK: numpy 2.4.6 / scipy 1.18.0). Reproduction succeeded perfectly, but **the diagnostics revealed a serious problem**, so confirmatory tests were run: ① comparison with a null distribution of appropriate amplitude, ② phase-randomized surrogates, ③ whether the corrected dynamics truly stabilize.

The result of the completed verification: **reproduction was a complete success, but what the result represents is different physics than assumed.** The report follows.

## 1. Reproducibility ✓

Verbatim reproduction of the original note's code fully reproduced the trajectory shape (2000, 30) and the band integral **−0.00890 · ROI=True**. The record itself is accurate.

## 2. However, the state diagnostics detect a serious anomaly (seed 42)

| Indicator | Value | Meaning |
| --- | --- | --- |
| Clipping activations | **1,136,999** (38,065 of 40,000 steps) | Stuck at the ±10 boundary in ≈**95%** of steps |
| Pre-clip maximum | $\lvert\psi_{\text{raw}}\rvert = 507$ | Violently trying to exceed the boundary |
| Saved-trajectory range | $\psi \in [-10, +10]$ (σ=9.65) | Living at the ±10 boundary, not in the double well (±1) |
| Fraction of time with $\lvert\psi\rvert < 0.3$ | 4.3% | Well (±1) occupancy ≈ 0 |
| First-half (0-10s) / second-half (10-20s) integrals | **−0.01926 / +0.00000** | Second half is a constant series stuck at the boundary → non-stationary |
| Inter-well transitions (±0.5 crossing) | ≈ 0 (contaminated by ±10-boundary reversals) | No physical well hopping |

## 3. Multi-seed · pair distribution · null comparison

| Verification | Result | Conclusion |
| --- | --- | --- |
| ROI verdict across 6 seeds | **ROI=True for all seeds**, but integrals range −0.185 to +0.190, **sign is a coin toss** | The "direction" of detection is not reproducible |
| Threshold excess across all 435 pairs | **99.5%** of pairs exceed per seed | The choice of pair (0,1) is arbitrary yet the result is unchanged = zero discriminative power |
| Same-amplitude (σ=9.65) iid noise | **100%** of pairs exceed the threshold (mean ≈ 0.50) | At this trajectory amplitude the $10^{-3}$ threshold cannot exclude false positives at all |
| 200 phase surrogates vs measured 0.0089 | **92%** exceed the measured value | The measured signal is below "a series with only its phase destroyed" |

## 4. dt convergence (shared common noise path)

| dt | integral(0,1) | σ(ψ) |
| --- | --- | --- |
| 0.001 | **−0.113** | 9.630 |
| 0.0005 | **−0.235** | 9.557 |
| 0.00025 | **+0.072** | 8.992 |

The integral **flips sign with the choice of dt**. This is not a regular discretization of a continuous-time SPDE; **the result depends on the numerical scheme**.

## 5. Ablation and initial-condition probe

- Full (asymmetric + memory): +0.180 (stuck at boundary) ／ No-asym: **0.00000** (σ=0.10, stable) ／ No-mem: −0.0004 (σ=0.65)
- Starting from $\psi_0 = +1$ (inside a well) → **sticks at the ±10 boundary within t≤1 and never returns**
- ⇒ in the current implementation the **"well" is not attractive**; with the asymmetric coupling present, numerical instability grows and sticks at the boundary

---

## Conclusion: what can be said

**1. The essence of the problem is a mathematical inconsistency in the implementation.** The note's update equations have two defects:

```python
# Original code
Xi = psi - psi**3 + J@psi            # ← opposite sign to the paper's bracket (ψ³ − ψ − Jψ)
psi += (-Q + noise)                   # ← ▲dt missing (+sign also flipped) → no continuous limit
```

**2. The reported "no divergence" is a product of clipping.** 95% of the trajectory is stuck at the ±10 boundary, and the −0.00890 "signal" is an artificial cross-spectrum created by ±10 rectangular-wave switching. **It is non-stationary and is not evidence of physical solenoidal flow or broken detailed balance.**

**3. The Phase-1 verdict is powerless in its current form.** Since 100% of pairs are judged ROI even for same-amplitude pure noise, every "ROI" flowing into Phase 2 and beyond is based on this artifact.

**4. Meanwhile, the model itself is sound and recovers with the correction (verified).** With update equations consistent with the paper's SPDE:

```python
xi  = psi**3 - psi - np.dot(J_sym, psi) - np.dot(J_asym, psi)  # matches the paper's bracket
Q  += (-gamma*Q + c_mem*xi) * dt
psi += (-Q + noise) * dt                                        # dt multiplied & sign consistent
```

one obtains:

- 0 clips · confined to $\psi \in [-1.19, 1.20]$ (**actually explores the double well ±1**; occupancy left 24.7% / barrier 51.8% / right 23.5% → transitions occur)
- The $\psi_0=+1$ probe **stays in the well** (well occupancy 1.000)
- dt convergence test: 0.001/0.0005/0.00025 give integral **+0.00001 (agreement)** · σ=0.6996/0.6992/0.6990 → **converges to a regular continuous-time system**

---

## Proposed next actions

1. **Replace Experiment 1 with the corrected version** and update this note's code and "results" (or append the corrected version).
2. Re-run **[Numerical Experiments (2)–(5)] with the corrected version.** In particular, Experiment (4) also needed fixing because it remained at `dummy_traj = randn` (Gaussian noise), not a verification on real trajectories.
   - ✅ **Experiment (4) already re-run with the corrected version** (`研究/AI/コード/数値実験4_修正版_情報幾何検証.py`, 2026-09-16). Fully reproducing the old dummy (det g=9.9534 / λmin=2.9292), the real-trajectory conclusion was updated to "no singularity fires at the operating point; density sharpness (λmin) is effective for condition discrimination". Details in [[論文（４）Numerical Experiments (4)]].
3. In the corrected version the pair-(0,1) integral was $+0.00001$ (below threshold). Since **the signal may be weak at current parameters**, a Phase-1 redesign based on the pair distribution / multiple indicators (mean absolute value, FDR) should be considered.

---

# Experiment 1 corrected version

Experiment 1 was replaced with the corrected version. The recorded corrected code was executed as-is so that the record and the executed result agree exactly.

The corrected version was confirmed stable and convergent (0 clips, $\psi \in [-1.14, +1.17]$). A discrepancy in the stationarity diagnostics (early-half σ=0.20 vs late-half σ=0.93) prompted confirmation with a long run (T=200) before updating the note.

The corrected results were fixed ($\psi \in [-1.1357, +1.1691]$ · 0 clips · stationarity confirmed at T=200 · with the important implication that the corrected version gives band integrals ≈ 0 for all pairs = **no ROI detection at the default parameters**). The note was then updated.

## Updates to [[論文（４）Numerical Experiments (1)]]

**1. Frontmatter** — `最終更新日時` updated to 2026-09-16

**2. `# 結果` → `# 結果（修正版・2026-09-16更新）`**
- The actual execution output of the corrected code is recorded verbatim (full agreement of record and output):
  ```
  Simulating Asymmetric Non-Markovian Spin Glass SPDE (Corrected Version)...
  Simulation completed successfully without divergence!
  Saved time steps: 2000, State dimensions: 30
  Trajectory shape: (2000, 30)
  Safety-clip activations: 0
  psi range: [-1.1357, 1.1691]
  ```
- `診断サマリ` appended: actual double-well exploration (left 22.2% / barrier 54.8% / right 23.0%) · burn-in ≈ 10 s · dt convergence (σ=0.669/0.671/0.672) · initial-condition probe (well maintained from ψ0=+1)

**3. `# コード` → `# コード（修正版）`** — only two places in the dynamics are corrected
- `Xi = psi - psi**3 + J@psi` → `xi = psi**3 - psi - J_sym@psi - J_asym@psi` (sign consistent with the paper's bracket $\delta H/\delta\psi - \mathcal{J}^{\text{asym}}\psi$)
- `d_psi = (-Q + noise)` → `d_psi = (-Q + noise) * dt` (dt made explicit = regular continuous-time discretization)
- recording of the clip activation count added

**4. `## 追補検証の記録（2026-09-16）` appended** — the old implementation's two inconsistencies, a 7-item measured comparison table of old vs corrected, and the conclusions.

## Implications for the next steps (important)

In the corrected version, **at the default parameters (coupling_scale=0.2, D=0.02) the Phase-1 indicator ≈ 0 for all 435 pairs** (p90=0.00000). This corroborates that the old −0.00890 was an artifact, and at the same time **means that simply re-running Numerical Experiment (2) with the corrected version is insufficient**. Experiment 2 requires:

1. Re-run with the corrected dynamics (T=200, burn-in truncation)
2. **Parameter exploration** (coupling_scale / D / gamma grid) to find the ROI
3. Pair-distribution-based indicators (all-pair statistics + FDR, not the single pair (0,1))

---

# Experiment 2 supplementary verification (2026-09-16 afternoon · restored version): parameter exploration with the corrected dynamics (definitive version)

Script: `研究/AI/コード/数値実験2_修正版_パラメータ探索.py` (saved · reproducible · deterministic)

## Scan 1: coupling_scale × D (γ=2.0, c_mem=1.0, T=150, burn-in=50)
- ROI=False at all 30 points, max|I| ≈ 0.0000, σ(ψ)=1.005–1.288, clip=0
- Conclusion: at γ=2.0 (overdamped), even strengthening the coupling does not lift the signal above the noise floor

## Scan 2: γ × c_mem (coupling_scale=1.0, D=0.05, T=300, burn-in=100)
- γ=0.1: ROI=True for all c_mem (max|I| = 0.400–1.578, significant-pair rate 8–10%)
- γ=0.2: ROI=True for all c_mem (max|I| = 0.100–0.428)
- γ=0.5: ROI=False for all c_mem (max|I| = 0.009–0.041) → corrected from the previous record's True (effective D=0.02)
- γ=1.0, 2.0: ROI=False
- Conclusion: **ROI = γ ≲ 0.2. Best = γ=0.1, c_mem=5–10, cs=1.0, D=0.05 (max|I| ≈ 1.3–1.6)**

## Robustness (γ=0.1, c_mem=5.0, cs=1.0, D=0.05, 6 seeds)
- ROI=True for all seeds (max|I| = 1.12–1.93, significant-pair rate 8.0–8.6%)

## Cause of the discrepancy with the morning record (established)
- The morning record was effectively run at D=0.02 (σ=1.39)
- The afternoon definitive version uses D=0.05 (σ=2.21). Single-point check: D=0.05→σ=2.210, D=0.02→σ=1.391 (seed=42, γ=0.1, c_mem=5.0)
- σ ratio 2.21/1.39 ≈ √(0.05/0.02), consistent → **the D=0.05 value is the official record**

※ Note: on the evening of 2026-09-16, a Google Drive synchronization problem rolled the record back once, and it was restored; [[論文（４）Numerical Experiments (2)]] was also rebuilt with the same content.

---

# Experiment 3 supplementary verification (2026-09-16 evening): intervention & noise sweep, corrected re-run (definitive version)

Script: `研究/AI/コード/数値実験3_修正版_介入ノイズ掃引.py` (saved · reproducible)
Baseline point: Experiment-2 ROI (γ=0.1, c_mem=5.0, cs=1.0, D=0.05, T=300, burn-in=100) · 6 seeds · mean ± SE display

## A.1 Driving intervention (J_asym gain λ sweep)
- |I01| and max|I| increase significantly and monotonically with λ (clip-free range λ∈[0,1.5]: mean|I01| slope=0.249±0.056 p=0.021 / max|I| slope=1.691±0.251 p=0.007)
- log-log elasticity ≈ λ^1.9 (superlinear)
- λ=2.0 is invalid due to clipping (177k hits)
- The direction of signed I01 is realization-dependent (sign-stable in 1/6) → the correct response statistic is the **magnitude** |I|

## A.2 Control intervention (J_sym gain λ sweep)
- No response over λ_sym∈[0.5,2.0] (slope n.s.: p=0.47)
- λ_sym=0 degenerates (clipping) and is excluded from analysis
- → the circulation indicator tracks only the asymmetric driving (causal separation via the negative control)

## A.3 Step intervention (same noise path, paired)
- Δmax|I| = +1.738±0.308, t=5.65, **p=0.0024**
- ROI: OFF → False for all seeds; ON → True for all seeds (causal switch reproduced in every realization)

## B Noise sweep
- ROI=True for all D∈[0.01,0.2]; max|I|≈1.2–2.5 → detection is robust to thermal noise (because of the D=0.02 outlier, expressed as "plateau-like & robust", not "strict plateau")

## Correction of the old record
- The old results (single realization, sign-flipping non-monotonic, "∂I/∂λ > 0") are invalidated as artifacts of the inconsistent implementation
- Corrected conclusion: **∂|I_Im|/∂λ > 0 (positive magnitude response)** holds statistically, and causality is established by the paired step-intervention test (p=0.0024)