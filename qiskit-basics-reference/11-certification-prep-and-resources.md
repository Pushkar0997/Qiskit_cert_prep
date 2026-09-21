# 11. Certification Prep and Consolidated Resources

## 11.1 What's officially confirmed about the Qiskit v2.X certification

Per IBM's own announcement, the certification was updated to match Qiskit
SDK v2.X, replacing the older "v0.2X" exam (which IBM Training retired on
30 September 2025 — anyone who already holds the old badge keeps it
permanently; Qiskit developer certifications don't expire). The v2.X
certification is a single exam; passing it earns the **"IBM Certified
Quantum Computation using Qiskit v2.X Developer – Associate"** credential
plus a digital badge issued via Credly.

Primary source (read this first, and check it periodically since IBM may
update logistics): <https://ibm.com/quantum/blog/qiskit-v2x-developer-certification>

For the previous (v0.2X) exam, IBM's own published format was: 60 questions,
delivered on the Pearson VUE platform, available globally in English. I
could not independently confirm the exact question count / format for the
new v2.X exam from an official IBM page as of this writing — **verify
current logistics (question count, time limit, cost, delivery platform)
directly on IBM Training before you schedule**, since these details are
exactly the kind of thing that can change between when this note was
written and when you read it.

## 11.2 Officially stated competency areas (v0.2X exam; largely still relevant conceptually)

IBM's own description of the legacy exam listed:

- Defining, executing, and visualizing results of quantum circuits
- Understanding single-qubit gates and their rotations on the Bloch sphere
- Understanding various multi-qubit gates and their effects in quantum circuits
- Leveraging fundamental Qiskit SDK features in commonly-used classes and functions

These map directly onto files 1–8 of this series. The v2.X exam updates the
*implementation* details being tested (primitives instead of `backend.run`,
current SDK APIs) but the underlying *conceptual* competency areas — circuit
construction, single- and multi-qubit gate behavior, visualization,
core SDK fluency — are a reasonable guide to what "basics mastery" needs to
cover, and match what this reference series and its companion notebook
focus on.

**A caveat on third-party breakdowns:** several third-party exam-prep
courses (Udemy and similar) circulate more granular percentage breakdowns
for the v2.X exam (e.g. specific weightings for "Perform Quantum
Operations," "Visualizing Circuits and States," "Creating Quantum
Circuits," etc.). These may well be accurate reproductions of IBM's
published exam blueprint, but I was not able to verify them directly
against an official IBM page, so treat them as directional rather than
authoritative, and prefer whatever IBM Training's own current exam guide
says if the two ever disagree.

## 11.3 Primary study resources, ranked roughly by how directly they map to the exam

1. **IBM Quantum Learning** — the official, current learning platform,
   including a full interactive course on quantum information fundamentals:
   - Catalog: <https://learning.quantum.ibm.com/catalog>
   - *Basics of Quantum Information* (John Watrous's course — this is the
     single best resource for truly understanding, not just memorizing, the
     material in file 1 of this series):
     <https://learning.quantum.ibm.com/course/basics-of-quantum-information>
2. **Qiskit's own documentation** — the current, actively-maintained
   reference for every API mentioned throughout this series:
   - Docs home: <https://quantum.cloud.ibm.com/docs>
   - Guides (conceptual, how-to): <https://quantum.cloud.ibm.com/docs/en/guides>
   - Full API reference: <https://quantum.cloud.ibm.com/docs/en/api/qiskit>
3. **The Qiskit SDK GitHub repository** — for anything the docs don't cover
   explicitly, the source and its docstrings are authoritative:
   <https://github.com/Qiskit/qiskit>
4. **The official Qiskit documentation repository** (useful if you ever find
   a docs page that seems stale or want to see exactly how an API changed
   between versions via its changelog):
   <https://github.com/Qiskit/documentation>
5. **The original Qiskit citation paper** — not exam material per se, but
   useful context on the project's own account of its architecture and
   goals: Javadi-Abhari et al., *"Quantum computing with Qiskit,"*
   arXiv:2405.08810: <https://arxiv.org/abs/2405.08810>

## 11.4 Consolidated link index (every link used across this series)

**Qiskit / IBM Quantum official documentation**
- Docs home: <https://quantum.cloud.ibm.com/docs>
- Guides index: <https://quantum.cloud.ibm.com/docs/en/guides>
- Full API index: <https://quantum.cloud.ibm.com/docs/en/api/qiskit>
- `QuantumCircuit`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.circuit.QuantumCircuit>
- `circuit` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit>
- `circuit_library` (standard gates): <https://quantum.cloud.ibm.com/docs/en/api/qiskit/circuit_library>
- Construct circuits guide: <https://quantum.cloud.ibm.com/docs/en/guides/construct-circuits>
- `quantum_info` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/quantum_info>
- `Statevector`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Statevector>
- `Operator`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.Operator>
- `SparsePauliOp`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.SparsePauliOp>
- `partial_trace`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/quantum_info#qiskit.quantum_info.partial_trace>
- `primitives` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/primitives>
- `StatevectorSampler`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.primitives.StatevectorSampler>
- Primitives guide: <https://quantum.cloud.ibm.com/docs/en/guides/primitives>
- Get started with backend primitives: <https://quantum.cloud.ibm.com/docs/en/guides/get-started-with-backend-primitives>
- Simulate with Qiskit SDK primitives: <https://quantum.cloud.ibm.com/docs/en/guides/simulate-with-qiskit-sdk-primitives>
- Simulate with Qiskit Aer: <https://quantum.cloud.ibm.com/docs/en/guides/simulate-with-qiskit-aer>
- Error mitigation and suppression: <https://quantum.cloud.ibm.com/docs/en/guides/error-mitigation-and-suppression-techniques>
- `compiler.transpile`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/compiler>
- `transpiler` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/transpiler>
- Transpile guide: <https://quantum.cloud.ibm.com/docs/en/guides/transpile>
- Set optimization level guide: <https://quantum.cloud.ibm.com/docs/en/guides/set-optimization>
- Transpiler stages guide: <https://quantum.cloud.ibm.com/docs/en/guides/transpiler-stages>
- Represent quantum computers guide: <https://quantum.cloud.ibm.com/docs/en/guides/represent-quantum-computers>
- `visualization` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/visualization>
- Visualize circuits guide: <https://quantum.cloud.ibm.com/docs/en/guides/visualize-circuits>
- Plot quantum states guide: <https://quantum.cloud.ibm.com/docs/en/guides/plot-quantum-states>
- Visualize results guide: <https://quantum.cloud.ibm.com/docs/en/guides/visualize-results>
- `qasm3` module: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qasm3>

**Learning platforms**
- IBM Quantum Learning catalog: <https://learning.quantum.ibm.com/catalog>
- Basics of Quantum Information course: <https://learning.quantum.ibm.com/course/basics-of-quantum-information>

**Certification**
- v2.X certification announcement: <https://ibm.com/quantum/blog/qiskit-v2x-developer-certification>

**Standards**
- OpenQASM (the intermediate representation Qiskit circuits can export to /
  import from): <https://openqasm.com>

**Source code**
- Qiskit SDK: <https://github.com/Qiskit/qiskit>
- Qiskit documentation source: <https://github.com/Qiskit/documentation>

**Papers**
- Javadi-Abhari et al., *Quantum computing with Qiskit* (arXiv:2405.08810):
  <https://arxiv.org/abs/2405.08810>

**Background reading (Wikipedia — good for cross-referencing conventions/definitions, not a substitute for the docs above)**
- Qubit: <https://en.wikipedia.org/wiki/Qubit>
- Bra–ket notation: <https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation>
- Bloch sphere: <https://en.wikipedia.org/wiki/Bloch_sphere>
- No-cloning theorem: <https://en.wikipedia.org/wiki/No-cloning_theorem>
- Measurement in quantum mechanics: <https://en.wikipedia.org/wiki/Measurement_in_quantum_mechanics>
- Quantum logic gate: <https://en.wikipedia.org/wiki/Quantum_logic_gate>
- Toffoli gate: <https://en.wikipedia.org/wiki/Toffoli_gate>
- Tensor product: <https://en.wikipedia.org/wiki/Tensor_product>
- Greenberger–Horne–Zeilinger state: <https://en.wikipedia.org/wiki/Greenberger%E2%80%93Horne%E2%80%93Zeilinger_state>
- Quantum teleportation: <https://en.wikipedia.org/wiki/Quantum_teleportation>
- Superdense coding: <https://en.wikipedia.org/wiki/Superdense_coding>
- CHSH inequality: <https://en.wikipedia.org/wiki/CHSH_inequality>

## 11.5 A study plan, if you want one

1. Read files 1–4 of this series alongside notebook sections 1–5; make sure
   you can derive (not just recall) the `H·Z·H = X` identity and similar
   small gate-algebra facts by hand.
2. Read files 5–8 alongside notebook sections 6–9; do every exercise in the
   notebook without looking at the solutions first, including the
   capstone.
3. Read files 9–10 alongside notebook sections 10–11; make sure you could
   explain, to someone else, *why* primitives replaced `backend.run()` —
   if you can only recite the new syntax, that's a sign to re-read
   section 9.1.
4. Work through IBM Quantum Learning's *Basics of Quantum Information*
   course in parallel — it's more mathematically rigorous than this
   series and will fill in anything left thin here.
5. Once basics feel solid, move to parameterized circuits, `TwoLocal`
   ansätze, and a from-scratch VQE loop built on the `Estimator` primitive
   — a natural "next notebook" this series intentionally doesn't cover, so
   that this one stays focused on fundamentals.
