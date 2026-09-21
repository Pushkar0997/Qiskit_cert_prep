# 3. Single-Qubit Gates — Deep Dive

Every quantum gate is represented by a **unitary matrix** `U`, meaning
`U†U = I` (its conjugate transpose is its inverse). Unitarity is exactly what
makes quantum gates reversible and probability-preserving — it guarantees
that if you started with a normalized state (`|α|²+|β|²=1`), you still have
a normalized state after applying `U`.

You can always inspect the actual matrix behind a Qiskit gate with
`qiskit.quantum_info.Operator`:

```python
from qiskit import QuantumCircuit
from qiskit.quantum_info import Operator

qc = QuantumCircuit(1)
qc.h(0)
print(Operator(qc).data)
```

API reference: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Operator>

## 3.1 The Pauli gates: X, Y, Z

```
X = [[0, 1],       Y = [[0, -i],      Z = [[1,  0],
     [1, 0]]            [i,  0]]           [0, -1]]
```

- **X** (`σₓ`): the quantum NOT / bit-flip. `X|0⟩ = |1⟩`, `X|1⟩ = |0⟩`.
- **Z** (`σ_z`): a **phase flip**. `Z|0⟩ = |0⟩`, `Z|1⟩ = -|1⟩`. Invisible on
  a computational-basis measurement of `|0⟩`/`|1⟩` alone, but very visible
  once a qubit is in superposition — it's exactly the kind of relative-phase
  effect described in file 1, section 1.3.
- **Y** (`σ_y`): a combined bit-and-phase flip; `Y = iXZ`.

Together with the identity `I`, the Paulis form a basis for all 2×2
Hermitian matrices, which is why they show up constantly as **observables**
in the `Estimator` primitive (`SparsePauliOp`, file 9) — "measure the
expectation value of Z" or "of XX on qubits 0 and 1" is a completely
standard thing to ask a quantum computer, distinct from sampling bitstrings.

## 3.2 Hadamard (H)

```
H = (1/√2) [[1,  1],
            [1, -1]]
```

`H|0⟩ = |+⟩ = (|0⟩+|1⟩)/√2`, `H|1⟩ = |−⟩ = (|0⟩−|1⟩)/√2`. `H` is the
standard superposition-creating gate, and `H² = I` (applying it twice
returns you to the start) — a good sanity check to run yourself.

Geometrically, `H` is a 180° rotation of the Bloch sphere about the axis
halfway between X and Z (i.e. the `(X+Z)/√2` axis).

## 3.3 Phase gates: S and T

```
S = [[1, 0],       T = [[1, 0        ],
     [0, i]]            [0, e^(iπ/4)]]
```

Both leave `|0⟩` untouched and apply a phase to `|1⟩` only — `S` is a 90°
rotation about the Z axis, `T` is a 45° rotation. Relationship: `S = T²`,
`S² = Z`. The `S†` (`Sdg`) and `T†` (`Tdg`) gates are their inverses.

The set `{H, T, CNOT}` is a **universal gate set** — meaning any quantum
computation whatsoever can be approximated to arbitrary precision using only
these three gates (this is the quantum analogue of NAND being universal for
classical logic). This is why `T` shows up so much in the fault-tolerant
quantum computing literature specifically ("T-count" is a standard resource
metric for a circuit).

## 3.4 Rotation gates: RX, RY, RZ

Continuous, parameterized versions of the Pauli gates — rotations by an
arbitrary angle `θ` about the X, Y, or Z axis of the Bloch sphere:

```
RZ(θ) = [[e^(-iθ/2), 0        ],
         [0,         e^(iθ/2)]]
```

(`RX`, `RY` are the analogous rotations mixing the other pairs of axes.)
Every single-qubit unitary can be written as a product of rotations about at
most two distinct axes, up to a global phase (this decomposition —
frequently `RZ-RY-RZ` or the general `U(θ,φ,λ)` gate — is exactly what the
transpiler uses when it needs to express an arbitrary single-qubit operation
in terms of a real device's native rotation gates; see file 10).

## 3.5 The general single-qubit gate: U

Qiskit exposes the fully general single-qubit unitary as `U(θ, φ, λ)`:

```
U(θ,φ,λ) = [[cos(θ/2),            -e^(iλ)sin(θ/2)     ],
            [e^(iφ)sin(θ/2),       e^(i(φ+λ))cos(θ/2) ]]
```

Every single-qubit gate you'll ever use — X, Y, Z, H, S, T, RX, RY, RZ — is
a special case of `U` for particular `(θ,φ,λ)`. This is the form the
transpiler often normalizes single-qubit operations to before mapping onto
hardware-native gates.

Full gate-by-gate API listing (every standard gate Qiskit ships, with exact
matrices):
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit_library>

## 3.6 A worked identity worth memorizing: H·Z·H = X

Starting from `|0⟩`: `H` takes you to `|+⟩`; `Z` flips the sign of the
`|1⟩` component, turning `|+⟩` into `|−⟩`; `H` then takes `|−⟩` to `|1⟩`.
Net effect on `|0⟩`: same as applying `X` directly. This identity
(`HZH = X`, and by symmetry `HXH = Z`) is a small, concrete example of a
much bigger idea: conjugating one gate by Hadamards swaps its role between
the computational (Z) basis and the Hadamard (X) basis. It comes up often
enough in circuit-simplification and error-correction contexts that it's
worth being able to derive on the spot rather than memorizing as a fact in
isolation.

## Further reading

- [Quantum logic gate — Wikipedia](https://en.wikipedia.org/wiki/Quantum_logic_gate)
  — matrix forms for essentially every named gate, useful as a
  cross-reference against Qiskit's own conventions.
- IBM Quantum Learning, *Basics of Quantum Information*, "Single systems" →
  "Qiskit implementations" lesson, which walks through exactly these gates
  in Qiskit code with the accompanying math:
  <https://learning.quantum.ibm.com/course/basics-of-quantum-information>
- API reference for the standard gate library:
  <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit_library>
