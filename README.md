# Qiskit Basics — A Deep-Dive Reference Series

## 🚀 Live Interactive Mock Exam & Study Hub

**[Open the Qiskit v2.x Certification Mock Exam & Study Hub](https://pushkar0997.github.io/Qiskit_cert_prep/)**

Prepare for the C1000-179 exam with 100 length-balanced questions, timed 10-, 20-, 40-, or 68-question exams, and multi-section practice filters. The hub also includes 32 flashcards, interactive Pauli, Estimator Precision, and Q-Sphere labs, plus a Qiskit Advocate exam directory.

A companion reference to the `qiskit_basics_deep_dive.ipynb` notebook, written for
certification prep on **Qiskit v2.x**. Where the notebook is optimized for
"run this and see," these files are optimized for "read this and understand
the why," with a lot more surrounding context, the underlying math spelled out,
and links to primary sources (official Qiskit/IBM Quantum documentation,
arXiv papers, and standard references) so you can go as deep as you want on
any topic.

All external links below were checked against the live documentation at the
time of writing (September 2026). IBM's docs occasionally reorganize URLs;
if a link ever 404s, search the same page title on
[quantum.cloud.ibm.com/docs](https://quantum.cloud.ibm.com/docs) — the content
almost always still exists, just possibly at a new path.

## Reading order

| # | File | Covers |
|---|------|--------|
| 1 | [`01-quantum-computing-primer.md`](01-quantum-computing-primer.md) | Qubits, amplitudes, superposition, the Bloch sphere, measurement, no-cloning |
| 2 | [`02-circuits-registers-and-gates-overview.md`](02-circuits-registers-and-gates-overview.md) | `QuantumCircuit`, quantum/classical registers, circuit composition, drawing |
| 3 | [`03-single-qubit-gates-deep-dive.md`](03-single-qubit-gates-deep-dive.md) | Pauli gates, Hadamard, phase gates, rotation gates, universality |
| 4 | [`04-statevector-simulation.md`](04-statevector-simulation.md) | `Statevector`, `Operator`, exact vs. shot-based simulation |
| 5 | [`05-measurement-sampling-and-shots.md`](05-measurement-sampling-and-shots.md) | The Born rule in code, shots, statistical noise vs. hardware noise |
| 6 | [`06-multi-qubit-systems-and-qubit-ordering.md`](06-multi-qubit-systems-and-qubit-ordering.md) | Tensor products, Qiskit's little-endian convention |
| 7 | [`07-two-qubit-gates.md`](07-two-qubit-gates.md) | CNOT, CZ, SWAP, controlled gates, Toffoli |
| 8 | [`08-entanglement-bell-and-ghz-states.md`](08-entanglement-bell-and-ghz-states.md) | Bell states, GHZ states, detecting entanglement, teleportation/superdense coding pointers |
| 9 | [`09-primitives-sampler-and-estimator.md`](09-primitives-sampler-and-estimator.md) | `Sampler`, `Estimator`, PUBs, why primitives replaced `backend.run()` |
| 10 | [`10-transpilation.md`](10-transpilation.md) | Basis gates, coupling maps, optimization levels, transpiler stages |
| 11 | [`11-certification-prep-and-resources.md`](11-certification-prep-and-resources.md) | Exam structure, official study resources, a consolidated link index |

## How to use this with the notebook

Each file roughly mirrors a section of `qiskit_basics_deep_dive.ipynb`. A good
workflow: read the markdown file for the conceptual depth and references,
then go run the matching notebook section to see it in code, then come back
if anything didn't click. The notebook is where you *do*; this series is
where you *understand why*.

## License / attribution note for your GitHub repo

These notes summarize and link to publicly available documentation (IBM
Quantum / Qiskit docs, arXiv papers, Wikipedia). They are original written
explanations, not reproductions of any single source — treat the linked
pages as the authoritative reference and these files as your own study
notes built on top of them.
