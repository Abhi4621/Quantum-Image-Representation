# Quantum Image Representation (QIR) Benchmarking Framework

This repository provides a systematic framework for implementing and comparing various **Quantum Image Representation (QIR)** techniques. The goal is to evaluate the trade-offs between space complexity (qubits), time complexity (circuit depth), and reconstruction fidelity across continuous, discrete, and higher-dimensional paradigms.

## 1. Repository Structure

The project is organized into modular components to ensure reproducible benchmarking:

* **representations**: Contains the core quantum circuit logic for each model:
    * `frqi.py`: Continuous amplitude/angle encoding.
    * `neqr.py`: Discrete basis state/binary encoding.
    * `qutrit_representations.py`: Ternary-based encoding using 3-level systems.
* **benchmarking**: Scripts to automate the calculation of gate counts, CNOT depth, and Fidelity.
* **utils**: Classical-to-Quantum Interface (C2QI) tools for image downsampling ($16 \times 16$, $32 \times 32$) and pixel normalization.
* **results**: Storage for output matrices, comparative CSV tables, and visualization plots (MSE vs. Shots).

---

## 2. Implementation Methodology

### Step 1: Classical Signal Preparation
Before quantum encoding, classical images must be transformed. 
- **For FRQI:** Pixel intensities (0-255) are mapped to angles $\theta \in [0, \pi/2]$.
- **For NEQR:** Intensities are converted to 8-bit binary sequences.

### Step 2: Circuit Construction and Encoding
This stage involves building the quantum state $|\Psi\rangle$ using specific gate logic:



1.  **State Initialization:** Apply Hadamard ($H$) gates to position qubits to create a uniform superposition of all pixel coordinates.
2.  **Color Mapping (FRQI):** Multi-controlled $R_y(\theta)$ gates bind intensity values to coordinate states.
3.  **Bit-String Encoding (NEQR):** Multi-controlled NOT (MCX) gates flip color qubits to represent discrete binary values.
4.  **Qutrit Logic (FQR3I):** Implementation of ternary gates ($X^{(01)}$, $R_y^{(01)}$) to utilize higher state-spaces, reducing physical unit requirements.

### Step 3: Simulation and Noise Modelling
Circuits are executed using the **Qiskit Aer Simulator**. To reflect real-world constraints, we apply:
- **Thermal Relaxation Error:** Simulating decoherence in NISQ-era hardware.
- **Quantum Volume Calculation:** Evaluated as $V_Q = (\min(n, d))^2$ where $n$ is qubits and $d$ is depth.

### Step 4: Measurement and Reconstruction
The quantum state is sampled via 8192+ shots. A **Brute Force Reconstruction** approach is used to reverse-engineer the probability distribution back into classical pixel intensities.

---

## 3. Comparison Metrics (Benchmarking Parameters)

The framework compares models based on the following established parameters:

| Parameter | FRQI (Le et al.) | NEQR (Zhang et al.) | Novel/Hybrid Model |
| :--- | :--- | :--- | :--- |
| **Space Complexity** | $2n + 1$ qubits | $2n + q$ qubits | **Optimized** |
| **Circuit Depth** | High ($O(2^{4n})$) | Moderate ($O(qn2^{2n})$) | **Low Depth** |
| **Retrieval Type** | Probabilistic | Deterministic | **Deterministic** |
| **Fidelity ($F$)** | ~0.99 (Sim) | 1.0 (Ideal) | **Target > 0.96** |
| **Mean Square Error** | High (Sampling noise) | 0 (Theoretical) | **Minimal** |



---

## 4. Key References
This implementation is derived from the following core research:
1. **Lisnichenko & Protasov (2022):** "Quantum Image Representation: A Review" (Innopolis University).
2. **Khandelwal & Chandra (2022):** "Quantum Image Representation Methods Using Qutrits" (TCS Research).
3. **Zhang et al. (2013):** "NEQR: A novel enhanced quantum representation" (Quantum Information Processing).
4. **Su et al. (2020):** "A New Trend of Quantum Image Representations" (IEEE Access).
