# 8. Entanglement: Bell States and GHZ States

## 8.1 What entanglement actually means

A multi-qubit state is **entangled** if it *cannot* be written as a tensor
product of individual single-qubit states — i.e. there is no way to assign
"qubit A's state" and "qubit B's state" separately; the description only
makes sense for the pair (or larger group) as a whole. This is a purely
quantum phenomenon with no classical analogue, and it's the resource
underlying quantum teleportation, superdense coding, and much of the
advantage claimed by quantum algorithms over classical ones.

## 8.2 The four Bell states

The canonical maximally-entangled 2-qubit states:

```
|Φ+⟩ = (|00⟩ + |11⟩) / √2
|Φ-⟩ = (|00⟩ - |11⟩) / √2
|Ψ+⟩ = (|01⟩ + |10⟩) / √2
|Ψ-⟩ = (|01⟩ - |10⟩) / √2     (the "singlet state")
```

Standard recipe for `|Φ+⟩`: Hadamard on qubit 0, then CNOT(control=0,
target=1).

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(2)
qc.h(0)
qc.cx(0, 1)
print(Statevector(qc).probabilities_dict())   # {'00': 0.5, '11': 0.5}
```

## 8.3 Proving entanglement with the reduced density matrix

If `|Φ+⟩` truly factored as `(a|0⟩+b|1⟩) ⊗ (c|0⟩+d|1⟩)`, then the reduced
state of either qubit alone (obtained by "tracing out" the other qubit)
would be a **pure** state. It isn't:

```python
from qiskit.quantum_info import partial_trace

sv = Statevector(qc)
reduced_q0 = partial_trace(sv, [1])   # trace out qubit 1
print(reduced_q0.data)      # [[0.5, 0], [0, 0.5]]  -- the maximally mixed state I/2
print(reduced_q0.purity())  # 0.5  (1.0 = pure, 0.5 = maximally mixed = maximally entangled partner)
```

The reduced state of qubit 0 alone is the **maximally mixed state**
`I/2` — purity `0.5`, the lowest possible for a single qubit. This is the
precise mathematical signature of entanglement: qubit 0 *on its own* looks
like a completely random classical bit, even though the full two-qubit
system is in a perfectly well-defined pure state. All the information lives
in the *correlation* between the qubits, not in either qubit individually —
which is also, informally, why entanglement can't be used to send
information faster than light (measuring your qubit alone gives you
nothing but a random bit; the correlation is only visible once someone
compares both measurement outcomes together, which requires a classical
communication channel).

`partial_trace` API reference (part of the `quantum_info` module):
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/quantum_info#qiskit.quantum_info.partial_trace>

## 8.4 Proving entanglement a second way: correlated observables

An independent check, using the `Estimator` primitive (full treatment in
file 9): for `|Φ+⟩`, the **joint** observable `ZZ` (Z on both qubits) has
expectation value exactly `+1` (the qubits are always found in the *same*
state, `00` or `11`), while **either qubit's own** `Z` expectation value is
`0` (each qubit alone is 50/50 between `+1` and `-1` outcomes):

```python
from qiskit.quantum_info import SparsePauliOp
from qiskit.primitives import StatevectorEstimator

qc_noMeasure = QuantumCircuit(2)
qc_noMeasure.h(0)
qc_noMeasure.cx(0, 1)

estimator = StatevectorEstimator()
print(estimator.run([(qc_noMeasure, SparsePauliOp("ZZ"))]).result()[0].data.evs)  # ~1.0
print(estimator.run([(qc_noMeasure, SparsePauliOp("IZ"))]).result()[0].data.evs)  # ~0.0
```

Seeing "individually random, jointly perfectly correlated" show up from two
completely different calculations (reduced-state purity, and joint vs.
individual expectation values) is a good way to build real confidence you
understand entanglement, rather than having memorized one code path that
happens to produce the right numbers.

## 8.5 GHZ states: entanglement across 3+ qubits

The Greenberger–Horne–Zeilinger (GHZ) state generalizes the Bell-state idea
to `n` qubits:

```
|GHZ⟩ = (|00…0⟩ + |11…1⟩) / √2
```

Built the same way, just fanning the entanglement out from one qubit to all
the others via a chain of CNOTs:

```python
def ghz_circuit(n_qubits):
    qc = QuantumCircuit(n_qubits)
    qc.h(0)
    for i in range(n_qubits - 1):
        qc.cx(i, i + 1)
    return qc
```

GHZ states are the standard benchmark for testing how well a real quantum
device preserves multi-qubit coherence — IBM Quantum's own hardware
benchmarking regularly reports "largest GHZ state fidelity achieved" as a
headline metric, precisely because GHZ states are maximally sensitive to
any single qubit in the chain decohering.

Reference: [Greenberger–Horne–Zeilinger state — Wikipedia](https://en.wikipedia.org/wiki/Greenberger%E2%80%93Horne%E2%80%93Zeilinger_state)

## 8.6 Where entanglement goes next (pointers, not covered in depth here)

Once "basics" feels solid, entanglement is the direct gateway to two
canonical protocols worth reading about even before you implement them:

- **Quantum teleportation** — using one shared entangled pair plus two
  classical bits to transmit an arbitrary unknown qubit state:
  [Quantum teleportation — Wikipedia](https://en.wikipedia.org/wiki/Quantum_teleportation)
- **Superdense coding** — using one shared entangled pair plus one
  transmitted qubit to send two classical bits of information:
  [Superdense coding — Wikipedia](https://en.wikipedia.org/wiki/Superdense_coding)

Both are standard "first non-trivial algorithm" exercises once the material
in this file feels solid, and both are built entirely from the gates
covered in files 3 and 7 plus the Bell-state recipe above.

## 8.7 A note on Bell's theorem (context, not exam material)

Historically, the reason physicists became certain entanglement is a real,
non-classical phenomenon (rather than just "hidden" classical information we
don't have access to yet) is Bell's theorem and its experimental violation
via the CHSH inequality. This is genuinely fascinating physics but sits
outside the Qiskit certification's scope — flagged here only because it's
the natural next question ("but how do we *know* this isn't just classical
correlation we don't understand yet?") once you've internalized 8.3–8.4.

Reference: [CHSH inequality — Wikipedia](https://en.wikipedia.org/wiki/CHSH_inequality)
