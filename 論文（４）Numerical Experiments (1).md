---
作成日時: 2026-09-15T17:13:00
最終更新日時: 2026-09-17T00:00:00
タグ:
  - NEIP
ステータス: 英語版
カテゴリー:
---

# Numerical Experiment (1): Mathematical Formulation of the Target Model

> English version of the Japanese note [[論文（４）数値実験（１）]]. All numbers, tables, and outputs are reproduced faithfully; code blocks are verbatim execution records.

## Record 1: Mathematical formulation of the target model (asymmetric non-Markovian spin-glass SPDE)

### 1. Objective

For the data-driven hybrid screening verification of non-equilibrium integrated phases (NEIP), to mathematically define a high-dimensional stochastic partial differential equation (SPDE) test-bed model that simultaneously describes "asymmetric driving (solenoidal flow)" — clearly distinguishable from equilibrium fluctuations — "non-Markovian memory" that generates history dependence, and "thermal noise".

### 2. Conditions

- **State variables:** Continuum local spin (or coupled-field) variables $\boldsymbol{\psi}(\mathbf{r}, t) = \{\psi_i(\mathbf{r}, t)\}_{i=1}^d$ (spatial dimension $\mathbf{r} \in \Omega$).

- **Decomposition of the interaction:** The coupling matrix $\mathcal{J}(\mathbf{r}, \mathbf{r}')$ is separated into a symmetric component (potential-driven) and an asymmetric component (solenoidal-flow-driven):

    $$\mathcal{J}(\mathbf{r}, \mathbf{r}') = \mathcal{J}^{\text{sym}}(\mathbf{r}, \mathbf{r}') + \mathcal{J}^{\text{asym}}(\mathbf{r}, \mathbf{r}')$$

    with $\mathcal{J}^{\text{sym}}(\mathbf{r}, \mathbf{r}') = \mathcal{J}^{\text{sym}}(\mathbf{r}', \mathbf{r})$ and $\mathcal{J}^{\text{asym}}(\mathbf{r}, \mathbf{r}') = -\mathcal{J}^{\text{asym}}(\mathbf{r}', \mathbf{r})$.

### 3. Methods (Construction of the SPDE)

We construct the following generalized non-Markovian asymmetric Langevin spin-glass SPDE, introducing a gradient-flow term from the potential $H[\boldsymbol{\psi}]$, a circulatory flow term from the asymmetric coupling, and a non-Markovian memory kernel $K(t-t')$ that incorporates past history:

$$\frac{\partial \boldsymbol{\psi}(\mathbf{r}, t)}{\partial t} = -\int_0^t K(t-t') \left[ \frac{\delta H[\boldsymbol{\psi}]}{\delta \boldsymbol{\psi}(\mathbf{r}, t')} - \int_{\Omega} \mathcal{J}^{\text{asym}}(\mathbf{r}, \mathbf{r}') \boldsymbol{\psi}(\mathbf{r}', t') d\mathbf{r}' \right] dt' + \boldsymbol{\eta}(\mathbf{r}, t)$$

where the potential energy $H[\boldsymbol{\psi}]$ is

$$H[\boldsymbol{\psi}] = \int_{\Omega} \left[ \frac{1}{2} (\nabla \boldsymbol{\psi})^2 + V(\boldsymbol{\psi}) \right] d\mathbf{r} - \frac{1}{2} \int_{\Omega} \int_{\Omega} \boldsymbol{\psi}(\mathbf{r}) \mathcal{J}^{\text{sym}}(\mathbf{r}, \mathbf{r}') \boldsymbol{\psi}(\mathbf{r}') d\mathbf{r}d\mathbf{r}'$$

$V(\boldsymbol{\psi})$ is a local multi-stable potential (e.g., the Ginzburg–Landau double-well potential $\frac{1}{4}|\boldsymbol{\psi}|^4 - \frac{1}{2}|\boldsymbol{\psi}|^2$).

$\boldsymbol{\eta}(\mathbf{r}, t)$ is Gaussian white noise with thermal-noise intensity $D$ that embeds the violation of the fluctuation–dissipation theorem (non-equilibrium):

$$\langle \eta_i(\mathbf{r}, t) \eta_j(\mathbf{r}', t') \rangle = 2 D \delta_{ij} \delta(\mathbf{r}-\mathbf{r}') \delta(t-t')$$

### 4. Results / Validation of the formulation

- For $\mathcal{J}^{\text{asym}} \neq 0$, a rotational probability flow in phase space (the solenoidal flow $\mathbf{V}_{\text{sol}}$) is generated persistently in the stationary state, guaranteeing the breaking of detailed balance.

- The presence of the non-Markovian memory kernel $K(t)$ produces an "apparent delay / circulation" distinct from a merely Markovian dissipative system, so the model satisfies the requirements for a rigorous test-bed of the identification protocol proposed in this paper (spectral imaginary part, intervention, noise sweep).

---

# Results (Corrected Version · Updated 2026-09-16)

```
Simulating Asymmetric Non-Markovian Spin Glass SPDE (Corrected Version)...
Simulation completed successfully without divergence!
Saved time steps: 2000, State dimensions: 30
Trajectory shape: (2000, 30)
Safety-clip activations: 0
psi range: [-1.1357, 1.1691]
```

### Diagnostics summary (from the supplementary verification)

- **State space:** $\psi \in [-1.14, +1.17]$ ($\sigma=0.67$). The double well (±1) is actually explored, with inter-well transitions occurring (left well 22.2% / barrier 54.8% / right well 23.0%). The safety clip (±10) never activates.
- **Stationarity:** At $T=20$ the first ≈10 s corresponds to a transient (burn-in). In a long run with $T=200$, statistics (mean, σ, well-occupancy) after 50 s are consistent across four quarters → burn-in truncation plus long runs are mandatory for spectral analysis.
- **dt convergence:** With a common noise path, statistics agree across dt = 0.001 / 0.0005 / 0.00025 (σ = 0.669 / 0.671 / 0.672) → converges to a regular continuous-time discretization.
- **Initial-condition probe:** Starting from $\psi_0=+1$ (inside a well), the system remains in the right well (occupancy 1.000).
- **Implication for Phase 1:** Under the corrected version, all 435 pairs at the default parameters give band integrals ≈ 0 (below the $10^{-3}$ threshold; p90 = 0.00000) → **no ROI is detected at these parameters**. The old −0.00890 was a clipping artifact; genuine signal detection requires parameter exploration (Numerical Experiment 2).

# Code (Corrected Version)

```python
## Reproduced verbatim from the original note (execution record).
import numpy as np

def simulate_asymmetric_spinglass(

N=30, # スピン（ノード）数

T=20.0, # 総シミュレーション時間

dt=0.0005, # 時間刻みをさらに細かく設定

D=0.02, # 熱雑音強度

gamma=2.0, # 記憶カーネルの減衰率

c_mem=1.0, # 記憶カーネルの振幅係数

coupling_scale=0.2, # 結合の強さの全体スケール（発散防止用）

seed=42

):

np.random.seed(seed)

steps = int(T / dt)

time_axis = np.linspace(0, T, steps)

## 1. 結合行列の作成（スケーリング調整済み）

J_raw_sym = np.random.randn(N, N) / np.sqrt(N)

J_sym = coupling_scale * 0.5 * (J_raw_sym + J_raw_sym.T)

np.fill_diagonal(J_sym, 0.0)

J_raw_asym = np.random.randn(N, N) / np.sqrt(N)

J_asym = coupling_scale * 0.5 * (J_raw_asym - J_raw_asym.T) # 反対称化（ソレノイド流成分）

## 2. 状態変数 psi と補助変数 Q の初期化（小さめの値）

psi = np.random.randn(N) * 0.01

Q = np.zeros(N)

subsample = 20 # 保存時の間引き間隔

saved_steps = steps // subsample

history = np.zeros((saved_steps, N))

saved_time = np.zeros(saved_steps)

save_idx = 0

diverged = False

clip_hits = 0  # 安全クリップの発動回数（記録用）

## 3. 数値積分ループ (Euler-Maruyama法)

for t_idx in range(steps):

## 駆動項 xi（論文の括弧内 (δH/δψ − J_asym ψ) = ψ³ − ψ − Jψ に一致）

xi = psi**3 - psi - np.dot(J_sym, psi) - np.dot(J_asym, psi)

## 補助変数 Q の更新（マルコフ埋め込み）

dQ = (-gamma * Q + c_mem * xi) * dt

Q += dQ

## 熱雑音の生成

noise = np.sqrt(2.0 * D * dt) * np.random.randn(N)

## 状態変数 psi の更新（dt を明示的に掛ける＝正則な連続時間離散化）

d_psi = (-Q + noise) * dt

psi += d_psi

## 爆発防止の安全クリップ（発動回数を記録）

clip_hits += int(np.sum(np.abs(psi) > 10.0))

psi = np.clip(psi, -10.0, 10.0)

if np.any(np.isnan(psi)) or np.any(np.isinf(psi)):

print(f"Warning: Numerical divergence detected at step {t_idx}!")

diverged = True

break

## 一定間隔でデータを保存

if t_idx % subsample == 0 and save_idx < saved_steps:

history[save_idx] = psi

saved_time[save_idx] = time_axis[t_idx]

save_idx += 1

if not diverged:

print("Simulation completed successfully without divergence!")

return saved_time[:save_idx], history[:save_idx], clip_hits

## --- 実行確認 ---

if __name__ == "__main__":

print("Simulating Asymmetric Non-Markovian Spin Glass SPDE (Corrected Version)...")

t, traj, clip_hits = simulate_asymmetric_spinglass()

print(f"Saved time steps: {len(t)}, State dimensions: {traj.shape[1]}")
print(f"Trajectory shape: {traj.shape}")
print(f"Safety-clip activations: {clip_hits}")
print(f"psi range: [{traj.min():.4f}, {traj.max():.4f}]")
```

## Record of the Supplementary Verification (2026-09-16)

Verbatim reproduction of the original implementation ("Stable Version") matched the numbers exactly (band integral −0.00890), but diagnostics performed at the same time revealed the defects below, so the note was replaced by the corrected version above.

### Two inconsistencies of the old implementation

1. **Missing dt in the update equation:** `d_psi = (-Q + noise)` (no dt on the $Q$ term). This is not a regular discretization of the continuous-time SPDE, so **the results depend on the choice of dt** (with a common noise path, band integrals flip sign with dt: −0.113 / −0.235 / +0.072 for dt = 0.001 / 0.0005 / 0.00025).
2. **Sign flip in the driving term:** `Xi = psi - psi**3 + J@psi` has the opposite sign to the paper's bracket $\delta H/\delta\psi - \mathcal{J}^{\text{asym}}\psi = \psi^3 - \psi - \mathcal{J}\psi$. The wells become repulsive, so starting from $\psi_0=+1$ (inside a well) the trajectory sticks at the ±10 boundary for t ≤ 1 and never returns.

### Old implementation vs corrected version (measured comparison)

| Verification item | Old implementation (clipping artifact) | Corrected version |
| --- | --- | --- |
| Safety-clip activations | 1,136,999 (38,065 of 40,000 steps) | 0 |
| Saved-trajectory range | Stuck at ±10 boundary (σ=9.65) | [-1.14, +1.17] (σ=0.67) |
| Double-well exploration | ≈0% (switching between ±10 boundaries) | left 22.2% / barrier 54.8% / right 23.0% (with transitions) |
| Stationarity | Stuck at boundary in last 10 s (band integral = 0) | Stationary after removing burn-in (≈10 s), confirmed at T=200 |
| dt convergence | Band integral flips sign (non-convergent) | Statistics agree across all dt |
| Phase-1 verdict | ROI=True for all 6 seeds, sign is a coin toss, 99.5% of all pairs exceed threshold | Band integral ≈ 0 for all 435 pairs → no ROI at default parameters |
| Null comparison | 100% false detection even for same-amplitude iid noise; 92% of surrogates exceed the measured value | (Signal assessment deferred to Numerical Experiments 2+) |

### Conclusions

- The old implementation's "no divergence" and "ROI detection −0.00890" are **clipping artifacts** and constitute no physical evidence (non-stationary, non-convergent, sign-unstable).
- The corrected version is a regular continuous-time discretization that actually reproduces the double-well dynamics (stable, convergent, stationarizing, with inter-well transitions).
- However, at the corrected defaults (coupling_scale=0.2, D=0.02) the Phase-1 indicator (band integral of the cross-spectral imaginary part) is below threshold for all pairs → **parameter exploration is mandatory for solenoidal-flow signal detection**, which becomes the task of Numerical Experiment (2).