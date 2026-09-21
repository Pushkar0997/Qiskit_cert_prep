# 7. Two-Qubit Gates

## 7.1 CNOT (controlled-X)

The workhorse two-qubit gate. `qc.cx(control, target)`: if the control qubit
is `|1⟩`, apply `X` to the target; otherwise, do nothing.

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Operator

qc = QuantumCircuit(2)
qc.cx(0, 1)   # control=qubit0, target=qubit1
print(Operator(qc).data.real)
```

With Qiskit's little-endian convention (file 6) and basis ordering
`|q1 q0⟩` = `00, 01, 10, 11`, the CNOT(control=0, target=1) matrix is:

```
      |00⟩ |01⟩ |10⟩ |11⟩
|00⟩ [ 1    0    0    0 ]
|01⟩ [ 0    0    0    1 ]
|10⟩ [ 0    0    1    0 ]
|11⟩ [ 0    1    0    0 ]
```

Reading this: `|10⟩` means qubit1=1, qubit0=0 — control (qubit 0) is `0`, so
nothing happens, and indeed row/column 2 (`|10⟩`) maps to itself. `|01⟩`
means qubit1=0, qubit0=1 — control is `1`, so the target (qubit 1) flips,
landing on `|11⟩` — and indeed the matrix swaps rows/columns 1 and 3
(`|01⟩` ↔ `|11⟩`). This is exactly the kind of matrix-reading exercise
worth doing by hand once, so that "little-endian" stops being an abstract
warning and becomes something you can verify for yourself.

CNOT is also its own inverse (`CNOT² = I`) and, notably, is the **only**
non-classical ingredient needed (alongside single-qubit gates) to reach a
universal gate set (file 3, section 3.3) — it's the simplest gate that can
create entanglement, which single-qubit gates alone can never do (since a
tensor product of single-qubit operations can never turn a product state
into an entangled one).

## 7.2 CZ (controlled-Z)

Applies `Z` instead of `X` to the target when the control is `|1⟩`. Notably,
CZ is **symmetric** in its two qubits — `cz(0, 1)` and `cz(1, 0)` produce the
identical operation, unlike CNOT where control and target are clearly
distinguishable. This symmetry falls out of the fact that `Z` only ever
introduces a phase (never flips a computational basis label), so "control"
and "target" become interchangeable labels for the same physical
interaction.

## 7.3 The general pattern: `.control()`

Qiskit lets you turn *any* single-qubit gate into a controlled version via
`.control()`, and lets you build multi-controlled versions (2+ control
qubits) the same way:

```python
from qiskit.circuit.library import XGate
toffoli = XGate().control(2)   # a doubly-controlled X, i.e. the Toffoli/CCX gate
```

The **Toffoli gate** (CCX — doubly-controlled X) is worth knowing by name:
it's classically universal on its own (any classical Boolean circuit can be
built from Toffoli gates alone) and shows up constantly in quantum
arithmetic circuits and oracle constructions (e.g. inside Grover's
algorithm).

## 7.4 SWAP

Exchanges the states of two qubits entirely: `|q1 q0⟩ → |q0 q1⟩`.

```python
qc = QuantumCircuit(2)
qc.x(0)        # qubit0=1, qubit1=0
qc.swap(0, 1)  # now qubit0=0, qubit1=1
```

SWAP matters in practice, not just as a toy operation: when a hardware
topology doesn't allow a two-qubit gate directly between the qubits you
actually need to interact, the **transpiler** inserts SWAP gates to move
qubit values around the connectivity graph until they're adjacent — see
file 10. Every inserted SWAP costs extra two-qubit gates (a SWAP itself
decomposes into 3 CNOTs), which is a big part of why hardware connectivity
matters so much for real-device performance.

## Further reading

- Full standard-gate library API (every named gate Qiskit ships, exact
  matrix definitions): <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit_library>
- [Quantum logic gate — Wikipedia](https://en.wikipedia.org/wiki/Quantum_logic_gate),
  section on multi-qubit gates, for cross-referencing matrix conventions
  against other sources.
- [Toffoli gate — Wikipedia](https://en.wikipedia.org/wiki/Toffoli_gate)
