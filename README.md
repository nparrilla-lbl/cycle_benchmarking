# Quantum Circuit Bounding with RC + CB (Qiskit/Aer)

This repository implements an open-source workflow for **bounding circuit performance** using:

- **Randomized Compiling (RC)** / Pauli twirling around CZ gates  
- **Cycle Benchmarking (CB)** to estimate cycle process infidelity  
- **SPAM/readout characterization** via confusion matrix  
- **Bound propagation** to compare measured error metrics against predicted bounds

Primary target bound:
\[
d_{\mathrm{TV}}(q,p) \le 1 - F_{\mathrm{SPAM}}\,F_{\mathrm{cyc}}^n
\]
with \(F_{\mathrm{cyc}} = 1 - e_F\) estimated from CB.

---

## Overview

The workflow is:

1. Build a noise model (1q depolarizing + coherent/depolarizing CZ + readout error).
2. Apply RC around CZ gates (Pauli twirling + frame corrections).
3. Estimate cycle fidelity via CB:
   - fit \(s_P(m)\approx A_P f_P^m\) for each Pauli channel \(P\),
   - convert \(f_P\to e_{F,P}\),
   - average over \(P\) to obtain cycle-level \(e_F\), then \(F_{\mathrm{cyc}}=1-e_F\).
4. Estimate \(F_{\mathrm{SPAM}}\) from a readout confusion matrix.
5. Sweep circuit depth and compare measured TVD/fidelity to propagated bounds.

---

## Implemented experiment modes

### Approach A (single-CZ cycle baseline)
- Cycle = random 1q unitary on each qubit + one CZ
- Depth axis = number of CZ cycles
- Useful baseline and closest to proposal Fig. 1 style construction

### Approach B (structured QASM cycle)
- Load a CZ-dominant transpiled 2q QASM block
- Each repeat uses a fresh random 1q frame + fixed QASM CZ structure
- Depth axis = number of block repeats
- CB characterization is performed on the same resampled ensemble used in sweep

---
