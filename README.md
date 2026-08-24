# Probability-Preserving Transformer

A Transformer-based framework for learning the temporal evolution of quantum wavefunctions governed by the **Time-Dependent Schrödinger Equation (TDSE)** while enforcing probability conservation directly through a hard architectural constraint.

---

## Overview

The **Probability-Preserving Transformer (PPT)** is designed to learn quantum wavefunction evolution while maintaining the fundamental normalization condition of quantum mechanics.

The reference quantum trajectories are generated numerically using the **Crank--Nicolson (CN) method** for a one-dimensional infinite square well. The Transformer then learns to predict future wavefunctions from a short temporal history of previously evolved states.

Unlike approaches that enforce physical properties only through additional loss penalties, the proposed PPT applies a **hard probability-preserving normalization layer** to its output. The predicted complex wavefunction is explicitly normalized during every forward pass, ensuring that its discrete probability remains approximately unity independent of the prediction loss.

The extended version of the study further evaluates whether the same architecture can **generalize across multiple physically distinct initial-condition families**, rather than being trained and evaluated on a single prescribed quantum state.

---

## Key Features

- Numerical TDSE trajectory generation using the **Crank--Nicolson method**
- One-dimensional infinite square-well quantum system
- Multi-token temporal wavefunction representation
- Transformer-based temporal sequence modeling
- Hard probability-preserving output normalization
- Generalization across multiple initial-condition families
- Trajectory-level train/test splitting to prevent temporal leakage
- Wavefunction accuracy evaluation
- Probability conservation analysis
- Energy consistency analysis
- Quantum-state fidelity evaluation
- Spatiotemporal probability-density visualization
- Computational performance and inference-time benchmarking
- Jupyter/Colab-based research workflow

---

## Initial-Condition Families

The extended dataset contains **60 independent quantum trajectories** covering four physically distinct classes:

| Initial condition | Number of trajectories | Description |
|---|---:|---|
| Ground state | 10 | First infinite-well eigenstate |
| First excited state | 10 | Second infinite-well eigenstate |
| Gaussian wavepacket | 20 | Localized, dynamically evolving states |
| Superposition | 20 | Multi-mode quantum states |

The larger number of Gaussian and superposition trajectories provides additional variation in localized and multi-component quantum dynamics.

Each trajectory contains:

- Spatial coordinate $x$
- Time $t$
- Real component of the wavefunction
- Imaginary component of the wavefunction
- Trajectory identifier
- Initial-condition family

The complete dataset contains:

**60 × 100 × 200 = 1,200,000 space-time samples**

---

## Methodology

The overall research workflow consists of the following stages:

### 1. TDSE Data Generation

Quantum trajectories are generated for the one-dimensional infinite square well using the **Crank--Nicolson numerical method**.

The simulations use:

- $N_x = 100$ spatial grid points
- $\Delta x = 1/99$
- $N_t = 200$ temporal steps
- $\Delta t = 0.001$
- Final simulation time $t=0.200$

The numerical trajectories are validated using probability conservation, energy stability, and boundary-condition checks.

### 2. Dataset Construction

The generated trajectories are organized into temporal sequences using a **five-state history window**.

For each prediction step, the model receives:

```text
ψ(t_i), ψ(t_{i+1}), ψ(t_{i+2}), ψ(t_{i+3}), ψ(t_{i+4})
