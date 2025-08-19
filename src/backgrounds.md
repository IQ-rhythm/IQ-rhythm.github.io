# Backgrounds


## 1. IonQ Hardware (via IonQ Cloud)

### Available Targets
- **Simulator (`ionq.simulator`)**  
  - GPU-accelerated, up to 29 qubits.  
  - Uses the **same gate set** as IonQ hardware → suitable for pre-validation of encoding circuits.  

- **IonQ Aria (`ionq.qpu.aria-1`, `aria-2`)**  
  - 25-qubit trapped-ion system.  
  - **Debiasing enabled by default** to reduce systematic errors.  
  - Provides commercial-grade performance metrics.  

- **IonQ Forte (`ionq.qpu.forte`)**  
  - 32-qubit system (currently in private preview).  
  - Designed for higher algorithmic qubit (#AQ) performance.  

### Performance Characteristics (IonQ Aria benchmark)
- **Coherence**: T₁ = 10–100 s, T₂ ≈ 1 s  
- **Gate times**: 1Q ≈ 135 µs, 2Q ≈ 600 µs  
- **Fidelity**:  
  - 1Q gates ≈ 99.95%  
  - 2Q gates ≈ 99.6%  
  - SPAM (state prep & measurement) ≈ 99.61%  

### Practical Implications for Encoding Optimization
- **Fully connected qubits**: any pair can be entangled → eliminates SWAP overhead.  
- **Workflow**: design and tune circuits on `ionq.simulator` → deploy on Aria/Forte hardware.  
- **Noise considerations**:  
  - Coherence and gate times impose **circuit depth constraints**.  
  - Debiasing must be accounted for when analyzing output distributions.  

### Ion-Q-Thruster Project (Qiskit-based optimizer)
- Custom transpiler tailored to IonQ’s native gate set (MS, GPi, GPi2).  
- Reduces gate count and depth compared to Qiskit defaults.  
- **Research relevance**: encoding circuits should integrate IonQ-native optimization passes for efficiency and noise robustness.  

<br>

## 2. Noise-Aware & Robust Circuit Design

### QuantumNAT (DAC 2022)
- Models noise as **scale + shift transformation** on measurement results.  
- Combines **Normalization**, **Noise Injection Training**, and **Post-measurement Quantization**.  
- **For IonQ**:  
  - Inject real hardware error rates into training.  
  - Normalize measurement outputs to counteract bias.  
  - Quantize readout to increase robustness.  

### QuantumNAS (HPCA 2022)
- Defines a **SuperCircuit** and selects SubCircuits under hardware noise models.  
- Uses **evolutionary search + pruning** to find robust circuits.  
- **For IonQ**:  
  - Full connectivity simplifies search space (no SWAP constraints).  
  - Circuit depth and entanglement levels must be tuned against T₂ limitations.  

### Noise-Resilience in VQAs (2021)
- Shows trade-off between expressivity and noise robustness.  
- **For IonQ**:  
  - Despite high fidelities, slower gates mean fewer shots available.  
  - Shallow, noise-resilient encodings outperform deeper expressive ones in practice.  

<br>

## 3. Embedding Methods & Search

### Quantum Embedding Search (2021)
- Represents encoding circuits as graph/genotype structures.  
- Uses **SMBO-TPE** for efficient automated search.  
- Restricts entanglement to avoid excessive noise.  
- **For IonQ**:  
  - Replace CNOT edges with MS gates.  
  - Impose entanglement-level constraints to avoid error accumulation.  
  - Integrate IonQ simulator in the search loop.  

### EnQode
- Proposes **fast amplitude embedding** via approximation.  
- Preserves accuracy while reducing circuit depth.  
- **For IonQ**:  
  - Mitigates long gate execution times.  
  - Suitable for large datasets requiring repeated embedding.  

### Multiple Embeddings
- Combines different encodings (Angle + Amplitude, etc.) to overcome single-encoding limitations.  
- **For IonQ**:  
  - Full connectivity enables multi-layer embeddings without SWAP overhead.  
  - Allows higher expressivity with manageable circuit depth.  

### Benchmarking Data Encoding Methods (2022)
- **Angle encoding**: shallow, low expressivity.  
- **Amplitude encoding**: high expressivity, deep circuits.  
- **Basis encoding**: impractical for limited qubit counts.  
- **For IonQ**:  
  - Basis encoding infeasible due to qubit count limits.  
  - Amplitude encoding limited by execution time → requires EnQode or hybrid approaches.  
  - Angle encoding lightweight but should be combined with others (e.g., Multiple Embeddings).  
