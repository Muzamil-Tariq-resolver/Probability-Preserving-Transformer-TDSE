# Probability-Preserving Transformer

A Transformer architecture for solving the **Time-Dependent Schrödinger Equation (TDSE)** while guaranteeing **exact probability conservation** through an architectural normalization layer.

---

## Overview

The Time-Dependent Schrödinger Equation (TDSE) is fundamental to quantum mechanics and describes the evolution of quantum wavefunctions over time. Traditional numerical solvers provide accurate solutions but may become computationally expensive for increasingly complex quantum systems.

This repository introduces the **Probability-Preserving Transformer (PPT)**, a Transformer-based deep learning model that learns quantum dynamics while enforcing the physical law of wavefunction normalization directly within the network architecture. Unlike conventional approaches that rely on probability-related loss penalties, the proposed method guarantees exact probability conservation during every forward pass.

---

## Key Features

- Numerical TDSE data generation
- Automatic dataset preprocessing
- Multi-token wavefunction representation
- Transformer-based quantum dynamics prediction
- Hard probability-preserving normalization layer
- End-to-end training pipeline
- Visualization of quantum evolution

---

## Methodology

The workflow consists of four stages:

1. Generate TDSE solutions using numerical methods.
2. Preprocess the wavefunction into sequential token representations.
3. Train the Probability-Preserving Transformer.
4. Enforce exact probability conservation using architectural normalization.

---

## Requirements

- Python 3.11+
- PyTorch
- NumPy
- SciPy
- Matplotlib
- Jupyter Notebook

Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Running the Project

### Step 1

Generate the dataset

```
Data_Generation_&_Reshaping.ipynb
```

### Step 2

Train the Transformer

```
Model_Architectures.ipynb
```

---

## Results

The proposed model is evaluated using numerical TDSE solutions as the reference.

Evaluation metrics include:

- Mean Squared Error (MSE)
- Relative L2 Error
- Probability Conservation Error
- Inference Time

---

## Author

Muzamil Tariq
Email: tariqjee1919gmail.com
