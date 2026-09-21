# 2. Circuits, Registers, and Gates — Overview

## 2.1 `QuantumCircuit`: the central object

Everything in Qiskit revolves around `qiskit.circuit.QuantumCircuit` — an
ordered list of instructions (gates, measurements, barriers, resets) acting
on qubits and classical bits.

```python
from qiskit import QuantumCircuit

qc = QuantumCircuit(2)   # 2 qubits, all initialized to |0>
qc.h(0)                  # Hadamard on qubit 0
qc.cx(0, 1)              # CNOT: control=0, target=1
qc.draw()
```

Two constructor calling conventions matter:
- `QuantumCircuit(n)` — `n` qubits, no classical bits, no measurement possible
  (useful for `Statevector`/`Estimator` work where you never measure).
- `QuantumCircuit(n, m)` — `n` qubits and `m` classical bits, so you can
  `.measure()` into them.

Official API reference (this is the single most important page to bookmark
for the whole certification):
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.circuit.QuantumCircuit>

Official how-to guide for circuit construction:
<https://quantum.cloud.ibm.com/docs/en/guides/construct-circuits>

## 2.2 Quantum wires vs. classical wires

A drawn circuit has two kinds of horizontal lines:
- **Quantum wires** (single line) carry qubits through gates. Information on
  a quantum wire can be in superposition, can be entangled with other
  wires, and is destroyed by measurement.
- **Classical wires** (double line) carry ordinary classical bits, produced
  only by `.measure()`. Once a value is on a classical wire, it behaves
  exactly like a normal classical bit forever after.

This distinction matters conceptually and matters to the exam: gates act
only on quantum wires; only `measure` (and, in dynamic circuits, classical
control flow) can move information *from* a quantum wire *to* a classical
one, and that move is one-directional and irreversible.

## 2.3 Registers — `QuantumRegister` and `ClassicalRegister`

`QuantumCircuit(2, 2)` implicitly creates one anonymous quantum register and
one anonymous classical register. For anything beyond a toy example, name
your registers explicitly:

```python
from qiskit import QuantumRegister, ClassicalRegister, QuantumCircuit

qr = QuantumRegister(2, name="q")
cr = ClassicalRegister(2, name="c")
qc = QuantumCircuit(qr, cr)

qc.h(qr[0])
qc.cx(qr[0], qr[1])
qc.measure(qr, cr)   # measures the whole register at once
```

A single circuit can carry **multiple** quantum and/or classical registers
(e.g. separate "data" and "ancilla" registers), which Qiskit will draw as
visually separated blocks. This is common in error-correction and
algorithm-building contexts where you want to reason about different groups
of qubits separately, even though physically they're all just qubits in one
circuit.

API references:
- `QuantumRegister`: part of `qiskit.circuit` — see the module index at
  <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit>
- `ClassicalRegister`: same module.

## 2.4 Gates as instructions, circuits as data

An important mental model for later (transpilation, primitives): a
`QuantumCircuit` is an abstract, hardware-agnostic *description* of a
computation. Nothing about writing `qc.h(0)` requires that qubit 0 on some
real chip literally supports a Hadamard gate natively — the circuit is a
convenient, high-level intermediate representation. It becomes executable
only after **transpilation** rewrites it in terms of a specific backend's
supported basis gates and qubit connectivity (file 10 covers this in full).

Qiskit's circuit library — pre-built circuits and gate templates you can
compose into your own circuits (adders, QFT, `TwoLocal` ansätze, etc.) — is
documented here: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit_library>

## 2.5 Drawing circuits

```python
qc.draw()                 # ASCII-art, no dependencies, always works
qc.draw(output="mpl")     # matplotlib rendering, nicer for sharing/screenshots
qc.draw(output="latex")   # LaTeX/qcircuit rendering
```

Full guide: <https://quantum.cloud.ibm.com/docs/en/guides/visualize-circuits>
API reference for the whole visualization module:
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/visualization>

## 2.6 Circuit composition

Real projects rarely build one monolithic circuit by hand; they compose
smaller circuits into larger ones via `QuantumCircuit.compose()`, or turn a
sub-circuit into a reusable custom gate via `.to_gate()` /
`.to_instruction()`. This is worth knowing exists even if you don't need it
for the basics — it's how you'll structure anything beyond a few dozen lines
of circuit-building code, and the certification's "Creating quantum
circuits" competency area does test composition patterns, not just
single-gate calls.
