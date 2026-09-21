# 6. Multi-Qubit Systems and Qiskit's Ordering Convention

This file covers the single most common source of confusion for people
learning Qiskit — including experienced programmers coming from textbooks
that use the opposite convention. Read it carefully; it is also explicitly
the kind of thing certification exams like to test, because it's easy to
get subtly wrong without noticing.

## 6.1 Tensor products

An `n`-qubit state lives in a `2ⁿ`-dimensional space, built as the
**tensor product** of the individual qubit spaces. For 2 qubits:

```
|q1⟩ ⊗ |q0⟩ = |q1 q0⟩
```

More concretely, if qubit A is in state `(a0, a1)` and qubit B is in state
`(b0, b1)`, their combined (unentangled) state is the 4-dimensional vector
`(a0b0, a0b1, a1b0, a1b1)`. This is standard linear algebra — the Kronecker
product of the two vectors — and it generalizes to any number of qubits,
which is exactly why simulating `n` qubits classically requires tracking
`2ⁿ` complex numbers and becomes intractable past a few dozen qubits.

## 6.2 Qiskit uses **little-endian**: qubit 0 is the rightmost bit

Most textbooks (including Nielsen & Chuang, the standard reference text)
write `|q0 q1 … q(n-1)⟩` with qubit 0 **leftmost**. **Qiskit does the
opposite**: qubit 0 is the **rightmost** character in a ket label, and the
**least significant bit** in the integer/binary interpretation of a basis
state string. This matches ordinary binary-number notation (bit 0 = ones
place, the rightmost digit), which is why Qiskit chose it — but it is the
opposite of a lot of foundational quantum computing literature, so
translating between "what a textbook draws" and "what Qiskit prints" is a
real, recurring task, not a one-time thing to memorize and forget.

Concretely:

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Statevector

qc = QuantumCircuit(3)
qc.x(0)   # flip ONLY qubit 0
print(Statevector(qc).probabilities_dict())
# -> {'001': 1.0}   i.e. qubit2=0, qubit1=0, qubit0=1
#    read the string LEFT TO RIGHT as (qubit n-1, ..., qubit 1, qubit 0)

qc = QuantumCircuit(3)
qc.x(2)   # flip ONLY qubit 2
print(Statevector(qc).probabilities_dict())
# -> {'100': 1.0}   i.e. qubit2=1, qubit1=0, qubit0=0
```

So reading a Qiskit bitstring like `'100'` for 3 qubits: that's qubit 2 = 1,
qubit 1 = 0, qubit 0 = 0 — **the rightmost character is qubit 0**.

## 6.3 Why this matters in practice, beyond "the exam might ask"

- If you build a circuit computing a binary number where qubit 0 is your
  "ones place," Qiskit's classical-bit interpretation of the output already
  lines up with ordinary binary notation, with no translation needed.
- If you're porting a circuit diagram from a textbook (or a paper) that
  draws qubit 0 at the top and writes states as `|q0 q1 q2⟩`, you need to
  either reverse the bitstring when interpreting Qiskit's output, or
  reverse your qubit-to-register mapping when building the circuit — pick
  one, be consistent, and document which convention you're using anywhere
  you write results down (README, paper, comment) for anyone (including
  future you) reading them later.
- Multi-qubit gate matrices (CNOT, Toffoli, etc.) are likewise written in
  this convention by `Operator` — see file 7 for a worked CNOT matrix
  example that only makes sense once you have this convention straight.

## 6.4 Reduced states and subsystems (preview of file 8)

Because multi-qubit states are built via tensor products, it's meaningful
to ask "what does the state of just qubit 0 look like, ignoring the
others?" — this is the **partial trace** / **reduced density matrix**
operation, covered in depth in file 8 in the context of detecting
entanglement. The short version: for an entangled state, the individual
qubit's reduced state is *mixed* (not a pure ket at all), which is the
precise mathematical sense in which "the qubits can't be described
separately."

## Further reading

- Official guide on constructing and reasoning about multi-qubit circuits:
  <https://quantum.cloud.ibm.com/docs/en/guides/construct-circuits>
- `Statevector` API reference (see `probabilities_dict`, which is the
  method used above):
  <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Statevector>
- [Tensor product — Wikipedia](https://en.wikipedia.org/wiki/Tensor_product)
  for the underlying linear-algebra definition, if you want the fully
  general treatment rather than just the qubit special case above.
