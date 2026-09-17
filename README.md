# NEIP Paper (4) — English Documentation & Numerical Experiments

**Author**: Satoshi Takemoto  
**Repository**: [NEIP-Paper-English-Notes](https://github.com/stakemoto1944-pixel/NEIP-Paper-English-Notes)

---

## Overview

This repository contains the comprehensive English documentation, experimental protocols, numerical simulation records, and theoretical verification notes for **Paper (4)**:

> **"Data-Driven Identification Protocol for Non-equilibrium Integrated Phases (NEIP): Resolution of Identifiability, Geometric Singularities, and Computational Bottlenecks"**  
> *(Target: Physical Review E)*

The research demonstrates the physical reality and geometric characteristics of **Nonequilibrium Integrated Phases (NEIP)** in stochastic partial differential equations (SPDEs) with asymmetric spin-glass couplings, non-Markovian memory kernels, and topological solenoid drives. A central theoretical prediction verified here is the **degeneracy of the Fisher Information Metric (FIM)** ($\det g \to 0 \iff \det C \to \infty$) at the phase boundary.

---

## Table of Contents

### 1. Protocols & Executive Summaries
* **[Experimental Protocol](<論文（４）Experimental Protocol.md>)**:  
  Standard operating procedures, numerical integration parameters, SPDE specifications, and metrics.
* **[Numerical Experiments: Summary](<論文（４）Numerical Experiments: Summary.md>)**:  
  Integrated executive summary of numerical experiments 1 through 8 (corrected dynamics, artifact-free validation).
* **[Significance, Value, and Applications](<論文（４）Significance, Value, and Applications.md>)**:  
  Conceptual positioning, technological superiority over conventional nonequilibrium physics, and broader applications.

### 2. Numerical Experiments (1–5)
* **[Numerical Experiments (1)](<論文（４）Numerical Experiments (1).md>)**:  
  Phase-1 screening, time-step ($dt$) convergence, stationarity verification, and clip-free stability testing.
* **[Numerical Experiments (2)](<論文（４）Numerical Experiments (2).md>)**:  
  Parameter exploration, ROI identification, and robustness tests across four conditions (ROI, Null, Default, i.i.d.).
* **[Numerical Experiments (3)](<論文（４）Numerical Experiments (3).md>)**:  
  Intervention testing, causal identification via noise-intensity sweeps, and spectral imaginary component analysis.
* **[Numerical Experiments (4)](<論文（４）Numerical Experiments (4).md>)**:  
  Information-geometric verification, KDE estimation of Fisher Information Metric, block bootstrap confidence intervals, and condition number ($\kappa$).
* **[Numerical Experiments (5)](<論文（４）Numerical Experiments (5).md>)**:  
  Phase-boundary sweeps and verification of the metric-degeneracy prediction across critical boundaries.

### 3. Advanced Verifications & Analyses
* **[Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction](<論文（４）Phase-Boundary Sweep Test of the Metric-Degeneracy Prediction.md>)**:  
  Systematic parameter sweep ($c_s$, $D$, $\gamma$, $c_{\text{mem}}$) tracking $\det g$ contraction towards the boundary.
* **[Metric-Degeneracy Test on High-Dimensional Submanifolds](<論文（４）Metric-Degeneracy Test on High-Dimensional Submanifolds.md>)**:  
  PCA-reduced manifold analysis and confirmation of metric degeneracy beyond 2D projections.
* **[Boundary Axes and Phase Variables: Verification](<論文（４）Boundary Axes and Phase Variables: Verification.md>)**:  
  Coupling structure analysis, boundary normal vectors, and order-parameter phase dynamics.
* **[Supplementary Verification of the Numerical Experiments](<論文（４）Supplementary Verification of the Numerical Experiments.md>)**:  
  Artifact remediation records, resolution of initial implementation discrepancies, and verification logs.

---

## Key Theoretical Results

1. **Information-Geometric Phase Boundary**:  
   At the boundary of NEIP, the Fisher Information Metric satisfies the singularity condition:
   $$\det g(\theta) \to 0 \quad \iff \quad \det C \to \infty$$
   reflecting critical divergence of macro-observable fluctuations.
2. **Two-Stage Identification Framework**:
   * **Phase-1**: Fast screening via imaginary spectral components (breaking of time-reversal symmetry / solenoid circulation).
   * **Phase-2**: Causal intervention via noise sweeps and non-parametric KDE Fisher metric validation.
3. **Artifact Elimination**:  
   Comprehensive re-execution of SPDE dynamics ensuring clip-free stationary distributions, verifying that observed coherence is an intrinsic nonequilibrium property rather than a boundary artifact.

---

## License

Copyright © 2026 Satoshi Takemoto. All rights reserved.
