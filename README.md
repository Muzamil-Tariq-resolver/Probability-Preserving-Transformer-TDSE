# Probability-Preserving Transformer

A Transformer-based framework for learning the temporal evolution of quantum wavefunctions governed by the **Time-Dependent Schrödinger Equation (TDSE)** while enforcing probability conservation directly through a hard architectural constraint.

---

## Overview

The **Probability-Preserving Transformer (PPT)** is designed to learn quantum wavefunction evolution while maintaining the fundamental normalization condition of quantum mechanics.

The reference quantum trajectories are generated numerically using the **Crank--Nicolson (CN) method** for a one-dimensional infinite square well. The Transformer learns to predict future wavefunctions from a short temporal history of previously evolved states.

Unlike approaches that enforce physical properties only through additional loss penalties, the proposed PPT applies a hard probability-preserving normalization layer to its output. The predicted complex wavefunction is explicitly normalized during every forward pass, ensuring that its discrete probability remains approximately unity independently of the prediction loss.

Tested whether the same PPT architecture can generalize across multiple physically distinct initial-condition families, rather than being trained and evaluated on a single prescribed quantum state.

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
- Jupyter/Google Colab-based research workflow

---

## Physical System

The current implementation considers a particle in a one-dimensional infinite square well:

$$
V(x)=
\begin{cases}
0, & 0 < x < L,\\
\infty, & x \leq 0 \ \text{or}\ x \geq L.
\end{cases}
$$

The corresponding boundary conditions are

$$
\psi(0,t)=\psi(L,t)=0.
$$

The TDSE is solved numerically to generate high-fidelity reference trajectories for training and evaluation.

---

## Initial-Condition Families

The extended dataset contains 60 independent quantum trajectories covering four physically distinct initial-condition classes:

| Initial Condition | Number of Trajectories | Description |
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

$$
60 \times 100 \times 200 = 1,200,000
$$

space-time samples.

---

## Methodology

The research workflow consists of the following stages:

1. Generate TDSE trajectories using the Crank--Nicolson method.
2. Validate the numerical reference solutions.
3. Construct temporal wavefunction sequences.
4. Train the Probability-Preserving Transformer.
5. Enforce probability conservation through hard architectural normalization.
6. Evaluate prediction accuracy and physical consistency.
7. Test generalization on previously unseen trajectories.
8. Benchmark computational performance against sequential Crank--Nicolson propagation.

---

## 1. TDSE Data Generation

Quantum trajectories are generated for the one-dimensional infinite square well using the Crank--Nicolson numerical method.

The simulations use:

- $N_x=100$ spatial grid points
- $\Delta x=1/99$
- $N_t=200$ temporal steps
- $\Delta t=0.001$
- $t_{\mathrm{final}}=0.200$

Each initial-condition family is propagated independently to generate multiple quantum trajectories.

The numerical trajectories are subsequently validated using probability conservation, energy stability, and boundary-condition checks.

---

## 2. Dataset Construction

The model uses a five-state temporal history window.

For each prediction step, the model receives:

$$
[\psi(t_i),\psi(t_{i+1}),\psi(t_{i+2}),
\psi(t_{i+3}),\psi(t_{i+4})]
$$

together with the corresponding time coordinates and predicts:

$$
\hat{\psi}(t_{i+5}).
$$

Each discretized wavefunction contains 100 real and 100 imaginary components, resulting in a 200-dimensional state representation:

$$
\psi(t_i)=
[
\mathrm{Re}(\psi_1),\ldots,\mathrm{Re}(\psi_{100}),
\mathrm{Im}(\psi_1),\ldots,\mathrm{Im}(\psi_{100})
].
$$

---

## 3. Probability-Preserving Transformer

The temporal sequence is processed using a Transformer encoder.

The main model configuration includes:

| Parameter | Value |
|---|---:|
| Temporal history | 5 states |
| State dimension | 200 |
| $d_{\mathrm{model}}$ | 64 |
| Transformer layers | 2 |
| Attention heads | 4 |
| Feed-forward dimension | 128 |
| Output dimension | 200 |

The final temporal token is used to reconstruct the predicted quantum state.

---

## 4. Hard Probability-Preserving Constraint

The unconstrained network output is separated into real and imaginary components and its discrete probability is calculated as:

$$
P(t) = \Delta x \sum_{i=1}^{N_x}
\left[
(\mathrm{Re}\,\psi_i(t))^2 +
(\mathrm{Im}\,\psi_i(t))^2
\right]
$$

The predicted wavefunction is then normalized according to:

$$
\hat{\psi}
\leftarrow
\frac{\hat{\psi}}
{\sqrt{P+\epsilon}},
$$

where:

$$
\epsilon=10^{-12}.
$$

Therefore, probability conservation is incorporated directly into the network architecture rather than being enforced only through an additional loss penalty.

---

## Generalization Experiment

The extended experiment investigates whether the same PPT architecture can generalize its learned quantum temporal evolution to **previously unseen trajectories across multiple initial-condition families**.

The model is evaluated on:

- Ground-state trajectories
- First-excited-state trajectories
- Gaussian-wavepacket trajectories
- Superposition trajectories

The architecture and hard probability-preserving constraint remain unchanged across all families.

### Split Strategy

The split is performed at the **trajectory level**, rather than at individual time steps. Therefore, all temporal samples belonging to a given trajectory remain exclusively in either the training or testing subset.

This prevents temporal information leakage and provides a stricter evaluation of generalization to previously unseen quantum trajectories.

---

## Training Configuration

The PPT is trained using the following configuration:

| Parameter | Value |
|---|---:|
| Optimizer | AdamW |
| Learning rate | $10^{-4}$ |
| Weight decay | $10^{-4}$ |
| Batch size | 64 |
| Maximum epochs | 200 |
| Gradient clipping | 1.0 |
| Boundary-loss weight $\alpha$ | 0.3 |
| Probability stabilization $\epsilon$ | $10^{-12}$ |

The training objective combines the standard mean squared error with a boundary-weighted contribution:

$$
\mathcal{L} = \mathcal{L}_{MSE} + \alpha \mathcal{L}_{boundary}
$$

The probability-preserving normalization is applied architecturally and is therefore **not introduced as an additional probability penalty** in the loss function.

---

## Numerical Validation

The Crank--Nicolson trajectories serve as the numerical reference for evaluating the learned PPT.

The reference trajectories are validated using:

- Discrete probability conservation
- Relative energy drift
- Infinite-well boundary conditions

For the complete 60-trajectory dataset, the numerical validation produced:

| Quantity | Maximum Deviation |
|---|---:|
| Probability | $5.9508\times10^{-14}$ |
| Relative energy drift | $7.5163\times10^{-15}$ |
| Boundary error | 0 |

These results establish the numerical consistency of the reference trajectories before they are used for Transformer training and evaluation.

---

## Evaluation Metrics

The trained PPT is evaluated using both numerical accuracy and physically motivated metrics.

### Wavefunction Accuracy

Prediction accuracy is evaluated using:

- Mean Squared Error (MSE)
- Root Mean Squared Error (RMSE)
- Relative $L_2$ error
- Maximum absolute error

### Probability Conservation

The predicted probability is evaluated over the held-out trajectories using:

$$
P(t) =
\Delta x
\sum_{i=1}^{N_x}
\left[
(\mathrm{Re}\,\psi_i(t))^2 +
(\mathrm{Im}\,\psi_i(t))^2
\right]
$$

The evaluation records:

- Minimum probability
- Maximum probability
- Mean probability
- Probability standard deviation
- Maximum deviation from unity

### Energy Consistency

The energy expectation value is calculated from the predicted and reference wavefunctions using the finite-difference representation of the Hamiltonian.

The relative energy error is then used to assess whether the learned evolution reproduces the energy behavior of the reference solution.

### Quantum-State Fidelity

The similarity between predicted and reference quantum states is evaluated using:

$$
F(t) =
\left|
\left\langle
\psi_{\mathrm{true}}(t)
\middle|
\psi_{\mathrm{pred}}(t)
\right\rangle
\right|^2
$$

Fidelity provides a state-level comparison that complements component-wise wavefunction error metrics.

---

## Generalization Analysis

Generalization is evaluated at multiple levels:

1. **Global test-set evaluation**  
   Measures overall predictive accuracy across all held-out trajectories.

2. **Initial-condition family evaluation**  
   Compares performance across ground states, first-excited states, Gaussian wavepackets, and superposition states.

3. **Trajectory-level evaluation**  
   Examines individual unseen trajectories to identify challenging cases and localized prediction errors.

4. **Spatiotemporal evaluation**  
   Analyzes prediction errors as functions of spatial position and time.

5. **Physical consistency evaluation**  
   Examines probability conservation, energy behavior, and quantum-state fidelity.

This evaluation separates two important aspects of the learned model: **predictive accuracy** and **physical consistency**.

---

## Spatiotemporal Analysis

The repository includes visualizations of the reference and predicted probability densities over the complete spatial and temporal domain.

The analysis includes:

- Crank--Nicolson probability density $|\psi(x,t)|^2$
- PPT-predicted probability density $|\hat{\psi}(x,t)|^2$
- Probability-density absolute error
- Spatial error distribution
- Temporal error behavior

For representative held-out trajectories, these visualizations provide a qualitative assessment of whether the PPT reproduces the spatial structure and temporal evolution of the reference solution.

---

## Computational Performance

The PPT is benchmarked against sequential Crank--Nicolson propagation under the same spatial resolution and computational conditions.

The benchmark records:

- Sequential CN runtime
- Batched PPT inference time
- Single-state PPT inference time
- Prediction horizon
- Relative speedup

The purpose of this comparison is to assess the computational potential of the trained model for repeated predictions.

The Crank--Nicolson method requires sequential propagation through intermediate time steps, whereas the trained PPT performs prediction through a forward pass using its fixed temporal history.

---

## Author
Muzamil Tariq (tariqjee1919@gmail.com)
