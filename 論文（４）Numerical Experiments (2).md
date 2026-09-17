---
作成日時: 2026-09-15T17:15:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Numerical Experiment (2): Phase 1 Fast Screening (Spectral Imaginary-Part Indicator)

> English version of the Japanese note [[論文（４）数値実験（２）]]. All numbers, tables, and outputs are reproduced faithfully; code blocks are verbatim execution records.

## Record 2: Phase 1 — fast screening (spectral imaginary-part indicator)

### 1. Objective

To perform a fast first-pass extraction, at low computational cost, of true solenoidal-flow driving (NEIP candidate region: ROI) from a vast parameter space, excluding false positives due to equilibrium states or passive delays.

### 2. Conditions

- **Input data:** Multivariate time series $\psi_A(t), \psi_B(t)$ obtained from the asymmetric spin-glass SPDE defined in Record 1.
- **Welch-method settings:** The observed time series is divided into $M$ overlapping segments with time window $T_{\text{win}}$.
- **Decision criterion (threshold):** In the continuous frequency band $f \in [f_1, f_2]$, the integral of the cross-spectral imaginary part (quad-spectrum) exceeds the threshold $\epsilon_{\text{th}}$ and the sign is constant:

    $$\left\vert{} \int_{f_1}^{f_2} \text{Im}[\overline{S}_{AB}(f)] df \right\vert{} > \epsilon_{\text{th}}, \quad \text{sgn}(\text{Im}[\overline{S}_{AB}(f)]) = \text{const.}$$

### 3. Methods

- **FFT-based fast processing:** Fast Fourier Transform (FFT) is applied to the real-space non-local coupling computation, reducing the operation order from $\mathcal{O}(N^2)$ to $\mathcal{O}(N \log N)$, and $\overline{S}_{AB}(f)$ is computed in bulk at each parameter point.
- **FDR control:** For the multiple-testing problem in exhaustive scans over many parameter grids, false-discovery-rate (FDR) control via the Benjamini–Hochberg procedure is applied, screening while preserving statistical significance.
- **Automatic ROI extraction:** Parameter regions satisfying the decision criterion are automatically flagged as "regions of interest (ROI)" and forwarded to the next stage (Phase 2) for verification.

### 4. Results

- Most equilibrium parameter regions and regions showing merely passive non-Markovian delay were rapidly rejected (Pruned) by this first-pass screening (band evaluation of the spectral imaginary-part indicator).
- Computation time was kept extremely small compared with full simulation or geometric analysis, demonstrating a major relief of the bottleneck of exhaustive exploration in high-dimensional systems.

---

# Results (Corrected Version · Updated 2026-09-16)

**Warning: The old implementation's "−0.00890 / ROI=True" is a numerical artifact.** The original update equation had two inconsistencies — a sign flip in the driving term and a missing dt — and sticking at the clip boundary ±10 produced an artificial cross-spectrum (the full verification record is in [[論文（４）Supplementary Verification of the Numerical Experiments]], original: [[論文（４）の数値実験の追補検証]]). The following are the results of the re-run and parameter exploration with the corrected version (consistent with the paper's SPDE).

- Corrected script: `研究/AI/コード/数値実験2_修正版_パラメータ探索.py` (saved · reproducible)
- At default parameters (coupling_scale=0.2, D=0.02, γ=2.0, c_mem=1.0), band integrals ≈ 0 for all 435 pairs → **no ROI under simple re-run**
- Parameter exploration was therefore performed, identifying **ROI = weak-damping, long-memory region γ ≲ 0.2**

## Supplementary verification (2026-09-16): parameter exploration with the corrected dynamics

Evaluation metric: cross-spectral imaginary-part band integral over all 435 pairs, $I_{ij} = \int_{[0.5,5]\text{Hz}} \mathrm{Im}[S_{ij}(f)]\,df$. The 99th percentile of |I| of a σ-matched iid white-noise null (null averaged over 5 trials) is used as the threshold, and pairs with "|I| > q99 and sign consistency > 0.85" are deemed significant. **ROI=True if significant pairs ≥ 1.**

### Scan 1: coupling_scale × D (γ=2.0, c_mem=1.0, T=150, burn-in=50)

- **ROI=False at all 30 points** (max|I| ≈ 0.0000, σ(ψ)=1.005–1.288, clip=0)
- Conclusion: at γ=2.0 (overdamped), even strengthening the coupling does not push the signal above the noise floor

### Scan 2: γ × c_mem (coupling_scale=1.0, D=0.05, T=300, burn-in=100)

| γ | c_mem | max|I| | significant-pair rate | ROI |
|---|---|---|---|---|
| 0.1 | 0.5 | 0.400 | 10.2% | True |
| 0.1 | 1.0 | 0.738 | 9.4% | True |
| 0.1 | 2.0 | 1.112 | 9.2% | True |
| 0.1 | 5.0 | 1.333 | 8.0% | True |
| 0.1 | 10.0 | 1.578 | 7.8% | True |
| 0.2 | 0.5 | 0.100 | 10.5% | True |
| 0.2 | 1.0 | 0.173 | 10.0% | True |
| 0.2 | 2.0 | 0.278 | 9.0% | True |
| 0.2 | 5.0 | 0.363 | 8.3% | True |
| 0.2 | 10.0 | 0.428 | 7.8% | True |
| 0.5 | 0.5 | 0.009 | 0.7% | False |
| 0.5 | 1.0 | 0.014 | 4.1% | False |
| 0.5 | 2.0 | 0.020 | 6.0% | False |
| 0.5 | 5.0 | 0.034 | 5.5% | False |
| 0.5 | 10.0 | 0.041 | 5.3% | False |
| 1.0 | 0.5 | 0.0005 | 0.0% | False |
| 1.0 | 1.0 | 0.0006 | 0.0% | False |
| 1.0 | 2.0 | 0.0007 | 0.0% | False |
| 1.0 | 5.0 | 0.0006 | 0.0% | False |
| 1.0 | 10.0 | 0.0007 | 0.0% | False |
| 2.0 | 0.5 | 0.0000 | 0.0% | False |
| 2.0 | 1.0 | 0.0000 | 0.0% | False |
| 2.0 | 2.0 | 0.0000 | 0.0% | False |
| 2.0 | 5.0 | 0.0000 | 0.0% | False |
| 2.0 | 10.0 | 0.0000 | 0.0% | False |

→ **ROI = γ ≲ 0.2. Best = γ=0.1, c_mem=5–10 (max|I| ≈ 1.3–1.6, significant-pair rate ≈ 8%).** The weaker the damping (smaller γ), the longer the memory persists and the more strongly the asymmetric-coupling-driven signal accumulates.

### Robustness (γ=0.1, c_mem=5.0, cs=1.0, D=0.05, T=300, burn-in=100, 6 seeds)

| seed | max|I| | significant-pair rate | ROI |
|---|---|---|---|---|
| 0 | 1.116 | 8.5% | True |
| 1 | 1.917 | 8.5% | True |
| 2 | 1.926 | 8.6% | True |
| 3 | 1.745 | 8.6% | True |
| 42 | 1.333 | 8.0% | True |
| 100 | 1.904 | 8.5% | True |

→ **ROI=True for all seeds** (max|I| = 1.12–1.93). The detection is a robust phenomenon independent of initial conditions and coupling randomness (seed).

### Correction of the record (important: cause of the discrepancy with previous values)

- The 2026-09-16 morning record (significant-pair rate ≈ 56%, ROI=True up to γ=0.5) was **effectively run at D=0.02** (σ=1.39)
- In the definitive version (D=0.05), σ ≈ 2.21. Single-point check (seed=42, γ=0.1, c_mem=5.0): **D=0.05 → σ=2.210 / D=0.02 → σ=1.391**
- The σ ratio 2.21/1.39 ≈ √(0.05/0.02) is consistent → the difference in noise intensity D is the cause
- **The D=0.05 results (table in this section) are the official record.** The γ=0.5 ROI verdict is corrected to False

## Code (corrected version)

See `研究/AI/コード/数値実験2_修正版_パラメータ探索.py` (reproduces all values of this supplementary verification). The dynamics corrections are common with [[論文（４）Numerical Experiments (1)]]:

```python
xi  = psi**3 - psi - np.dot(J_sym, psi) - np.dot(J_asym, psi)  # 論文の括弧内に符号整合
Q  += (-gamma*Q + c_mem*xi) * DT
psi += (-Q + np.sqrt(2.0*D*DT)*np.random.randn(N)) * DT         # dt を明示的に掛ける（正則な連続時間離散化）
```

---

# Results (Old Implementation · 2026-09-15) — reference record only, since the supplementary verification showed it to be an artifact

```
1. Running SPDE Simulation...
2. Running Phase 1 Screening...
Cross-spectrum imaginary band integral: -0.00890
Passed Phase 1 Screening (Is ROI?): True
```

# Code (Old Implementation · Invalid)

The update equation has a sign flip and a missing dt, so the output of this code must not be used.

```python
import numpy as np
from scipy.signal import csd
from scipy.integrate import trapezoid  # 追加：Scipyの積分関数を使用

def simulate_asymmetric_spinglass(
    N=30,            # スピン（ノード）数
    T=20.0,          # 総シミュレーション時間
    dt=0.0005,       # 時間刻み
    D=0.02,          # 熱雑音強度
    gamma=2.0,       # 記憶カーネルの減衰率
    c_mem=1.0,       # 記憶カーネルの振幅係数
    coupling_scale=0.2, # 結合の強さの全体スケール
    seed=42
):
    np.random.seed(seed)
    steps = int(T / dt)
    time_axis = np.linspace(0, T, steps)
    
    J_raw_sym = np.random.randn(N, N) / np.sqrt(N)
    J_sym = coupling_scale * 0.5 * (J_raw_sym + J_raw_sym.T)
    np.fill_diagonal(J_sym, 0.0)
    
    J_raw_asym = np.random.randn(N, N) / np.sqrt(N)
    J_asym = coupling_scale * 0.5 * (J_raw_asym - J_raw_asym.T)
    
    psi = np.random.randn(N) * 0.01
    Q = np.zeros(N)
    
    subsample = 20  # 保存時の間引き間隔
    saved_steps = steps // subsample
    history = np.zeros((saved_steps, N))
    saved_time = np.zeros(saved_steps)
    
    save_idx = 0
    diverged = False
    
    for t_idx in range(steps):
        force_potential = psi - psi**3
        interaction = np.dot(J_sym, psi) + np.dot(J_asym, psi)
        Xi = force_potential + interaction
        
        dQ = (-gamma * Q + c_mem * Xi) * dt
        Q += dQ
        
        noise = np.sqrt(2.0 * D * dt) * np.random.randn(N)
        psi += (-Q + noise)
        
        psi = np.clip(psi, -10.0, 10.0)
        
        if np.any(np.isnan(psi)) or np.any(np.isinf(psi)):
            diverged = True
            break
            
        if t_idx % subsample == 0 and save_idx < saved_steps:
            history[save_idx] = psi
            saved_time[save_idx] = time_axis[t_idx]
            save_idx += 1
            
    return saved_time[:save_idx], history[:save_idx]

def run_phase1_screening(traj, dt=0.005, f_band=(0.5, 5.0), epsilon_th=1e-3):
    """
    フェーズ1: Welch法によるクロススペクトル虚部の帯域積分スクリーニング
    """
    steps, N = traj.shape
    fs = 1.0 / dt
    
    x = traj[:, 0]
    y = traj[:, 1]
    
    f, Pxy = csd(x, y, fs=fs, nperseg=min(256, steps // 4))
    imag_Pxy = np.imag(Pxy)
    
    band_mask = (f >= f_band[0]) & (f <= f_band[1])
    
    if np.sum(band_mask) == 0:
        return 0.0, False
        
    # 修正: np.trapz から scipy.integrate.trapezoid へ変更
    band_integral = trapezoid(imag_Pxy[band_mask], f[band_mask])
    passed = np.abs(band_integral) > epsilon_th
    
    return band_integral, passed
```

![[Pasted image 20260916075707.png]]![[Figure 1 9 1.png]]