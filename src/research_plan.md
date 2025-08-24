# Research Plan

## 1. Background on Encoding Methods

### Angle Encoding (AE)
- **Concept**: Encodes each feature of the classical data into the rotation angle of a single-qubit gate (e.g., RY, RZ).
- **Pros**: Shallow circuits, low gate count, hardware-efficient.
- **Cons**: Limited expressive power, often requires entangling layers or repetition.
- **Use Case**: Lightweight baseline for low-dimensional (PCA-16/32) tasks.

### Data Re-Uploading (DRU)
- **Concept**: Re-injects classical data multiple times across different layers of parameterized quantum circuits.
- **Pros**: Significantly boosts expressive capacity; proven universal with enough repetitions.
- **Cons**: Parameter-heavy, more sensitive to noise as depth grows.
- **Use Case**: Mid-scale datasets (PCA-32/64), where richer decision boundaries are needed.

### Amplitude Encoding (AMP)
- **Concept**: Maps a normalized vector directly to the amplitude of a quantum state.
- **Pros**: Compresses high-dimensional data efficiently, expressive.
- **Cons**: Exact preparation is circuit-depth heavy; approximate methods (e.g., EnQode) reduce overhead but introduce approximation error.
- **Use Case**: PCA-32 (exact encoding), PCA-64 (approximate EnQode-style).

### Hybrid / Multiple Embeddings
- **Concept**: Combines multiple encoding schemes, e.g., Angle + Amplitude.
- **Pros**: Balances resource efficiency and expressivity.
- **Cons**: More complex circuit design, risk of increasing depth.
- **Use Case**: Multi-class classification (4-class, 10-class) with balanced requirements.

### Kernel Feature Maps (ZZFeatureMap / IQP)
- **Concept**: Uses fixed quantum feature maps to transform classical inputs, followed by classical kernel methods like SVM.
- **Pros**: No trainable quantum parameters, robust to barren plateaus.
- **Cons**: Requires computation of kernel matrices; shot-intensive.
- **Use Case**: Baseline comparison, binary or 4-class tasks.

### Quantum Kitchen Sinks (QKS)
- **Concept**: Random quantum circuits generate nonlinear embeddings, which are then classified classically.
- **Pros**: Simple, hardware-friendly, requires no optimization.
- **Cons**: Needs many random circuits to be effective.
- **Use Case**: Binary classification as a quick baseline.

---
<br>
## 2. Training Environment Setup

- **Dataset**: Fashion-MNIST (10 classes).
- **Preprocessing**:  
  - Downsampled 8×8 (64D).  
  - PCA-32 and PCA-64 variants.
- **Tasks**:  
  - T2: Binary classification (e.g., T-shirt vs Pullover).  
  - T4: 4-class classification (T-shirt, Pullover, Coat, Shirt).  
  - T10: Reduced 10-class task (balanced subset).
- **Splits**: Train 80%, Validation 10%, Test 10%.
- **Frameworks**: PennyLane (preferred), Qiskit/Cirq for IonQ submission.
- **Optimization**:  
  - Loss: Cross-Entropy.  
  - Simulations: Adam optimizer, learning rate 5e-3 with decay.  
  - Hardware runs: SPSA or finite-difference for noise-friendly updates.
- **Training Schedule**:  
  - Simulation: 20–40 epochs, batch size 32.  
  - Hardware: 3–8 epochs, batch size 32.  
- **Evaluation Metrics**: Accuracy, F1, gate count, circuit depth, shots, runtime, hardware queue/wait logs.
- **Error Mitigation**: Measurement calibration, Zero-Noise Extrapolation (ZNE), readout error mitigation where possible.

---
<br>

## 3. Step-by-Step Experimental Plan

### Week 3 — Baseline Encoding Verification
- Implement AE, DRU, AMP (exact/approximate), Hybrid, and Kernel circuits.
- Train on noiseless simulators with PCA-16/32/64 datasets.
- Record baseline performance and resource consumption (accuracy, depth, gates).

### Week 4 — Noise Modeling and Structure Search
- Incorporate realistic noise models (IonQ gate fidelities, depolarizing/readout error).
- Run noisy simulations with shots sweep (256, 512, 1000).
- Evaluate robustness of AE, DRU, AMP, Hybrid under noise.
- Conduct circuit structure search using Evolutionary algorithms (Robust Ansatz) and ML-based methods (NACL).

### Week 5 — IonQ Hardware First Run
- Select representative circuits (AE, DRU, AMP-Approx, Hybrid, Kernel).
- Submit binary classification (T2, PCA-16D) jobs with shots = 500, 1000.
- Apply error mitigation strategies (ZNE, calibration).
- Compare results with simulator outputs.

### Week 6 — Hardware-Guided Optimization
- Analyze first-run results, select top 2–3 encodings based on accuracy vs. resource efficiency.
- Re-run on IonQ with shots = 2000.
- Expand to 4-class classification (T4, PCA-32).
- Generate plots of accuracy vs. depth/gates and noise sensitivity.
- Perform comprehensive comparison across AE, DRU, AMP, Hybrid, Kernel.
- Run final experiments with shots 2000–4000 on selected encodings.
- Produce final figures:  
  - Accuracy vs. shots.  
  - Gate count vs. accuracy Pareto fronts.  
  - Simulator vs. hardware scatter plots.  
- Write final survey: encoding definitions, experimental setup, results, IonQ-specific guidelines (optimal encoding per dataset size, recommended depth/shots).

---

## 4. Expected Deliverables
1. **Survey Paper**: Detailed methodology, results, trade-offs, and IonQ-specific encoding recommendations.  
2. **Figures & Tables**: Performance vs. resource trade-off, noise robustness comparison, error mitigation impact.  
3. **Supplementary**: Code repository with all encodings, IonQ submission logs, preprocessing scripts.  
