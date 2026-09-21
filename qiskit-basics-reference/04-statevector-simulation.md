# 4. `Statevector` and Exact Simulation

## 4.1 What `Statevector` gives you that a real computer can't

`qiskit.quantum_info.Statevector` computes the **exact** amplitudes of a
circuit's final state by direct matrix multiplication — no randomness, no
"shots," no hardware noise. This is only possible in simulation: a real
quantum computer cannot hand you its amplitudes directly (doing so would
require both violating the no-cloning theorem and getting more information
out of a measurement than the Born rule allows). `Statevector` is,
correspondingly, a **debugging tool** — when a circuit isn't behaving as
expected, check its exact state before you even think about running it
on hardware or a shot-based simulator.

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(2)
qc.h(0)
qc.cx(0, 1)

sv = Statevector(qc)
print(sv.data)                 # raw complex amplitudes
print(sv.probabilities_dict()) # Born-rule probabilities per basis state
```

API reference: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Statevector>

**Critical constraint:** a circuit passed to `Statevector` must **not**
contain measurements. Measurement is a random, classical operation;
`Statevector` only simulates the deterministic unitary evolution part of
quantum mechanics. If you need both an exact-state check and a
shot-based histogram, build two versions of the circuit (or copy one and
strip/add the measurement).

## 4.2 `Operator`: the matrix behind any circuit or gate

Where `Statevector` gives you a state (a vector), `Operator` gives you the
full unitary matrix of a circuit or gate — useful for verifying gate
identities, checking two circuits are equivalent up to phase, or just
building intuition about what a sequence of gates actually does as a single
combined operation.

```python
from qiskit.quantum_info import Operator
print(Operator(qc).data)
```

API reference: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Operator>

## 4.3 The `quantum_info` module more broadly

`Statevector` and `Operator` are two members of a much larger toolkit under
`qiskit.quantum_info`, which also includes `DensityMatrix` (for mixed
states — necessary once you're modeling noise or partial/reduced systems),
`SparsePauliOp` (for observables, file 9), `partial_trace` (for reduced
states, file 8), and `Clifford`/`Pauli` (efficient representations for
special, restricted classes of circuits used heavily in error-correction
research). Full module index:
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/quantum_info>

## 4.4 Exact simulation vs. `AerSimulator` vs. real hardware

It's worth being precise about three different things that are easy to
conflate:

| Tool | What it computes | Randomness? | Hardware noise? |
|---|---|---|---|
| `Statevector` | Exact final amplitudes | No | No |
| `AerSimulator` (ideal) | Sampled bitstrings from the exact distribution | Yes (shot noise) | No |
| `AerSimulator` (with a noise model) | Sampled bitstrings from a *noisy* distribution | Yes | Yes (simulated) |
| Real IBM Quantum hardware | Sampled bitstrings | Yes | Yes (real) |

Guides:
- Exact simulation with Qiskit SDK primitives (i.e. `StatevectorSampler` /
  `StatevectorEstimator`, no Aer involved):
  <https://quantum.cloud.ibm.com/docs/en/guides/simulate-with-qiskit-sdk-primitives>
- Exact *and* noisy simulation with Qiskit Aer:
  <https://quantum.cloud.ibm.com/docs/en/guides/simulate-with-qiskit-aer>

## 4.5 Global phase — the thing `Statevector.equiv()` exists for

Two states differing only by an overall factor `e^(iγ)` (a **global**
phase, applied equally to every amplitude) are physically indistinguishable
— every measurement probability and every expectation value comes out
identical. This is different from a *relative* phase between amplitudes
(which is very much observable, per file 1 and file 3). Because of this,
`Statevector` provides `.equiv(other)` — equality up to global phase — as
the physically meaningful notion of "these two states are the same,"
distinct from `==` (exact array equality), which is usually too strict a
check for anything you'd want to test in practice.

```python
from qiskit.quantum_info import Statevector
sv1 = Statevector.from_label("1")
sv2 = Statevector([0, -1])   # differs from sv1 by a global phase of -1
print(sv1.equiv(sv2))        # True
print((sv1.data == sv2.data).all())  # False
```
