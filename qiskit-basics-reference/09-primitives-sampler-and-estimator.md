# 9. Primitives: `Sampler` and `Estimator`

If you've seen older Qiskit tutorials (or studied for the v1.x
certification), you may have encountered `backend.run(circuit)`. **That
pattern is deprecated in the current API.** Qiskit v2.x — and the current
certification — is built around **Primitives**, which is now the
recommended entry point for actually executing circuits. Understanding
*why* this changed is worth more than just memorizing the new call syntax.

## 9.1 Why primitives instead of `backend.run()`?

`backend.run()` just returns raw counts and leaves you to compute anything
derived (expectation values, error mitigation corrections, etc.) yourself,
by hand, every time. Primitives instead model the **two things people
actually want from a quantum computer**, as first-class objects:

- **`Sampler`** — give me the probability distribution over measurement
  outcomes (bitstrings). Use this when the bitstrings themselves are the
  answer (Grover's algorithm, general sampling problems, quantum
  simulation readout).
- **`Estimator`** — give me the expectation value of an observable (e.g. a
  Hamiltonian's energy). Use this in variational algorithms like VQE and
  QAOA, where the number you actually care about is `⟨ψ|H|ψ⟩`, not the raw
  bitstrings that would, in principle, let you reconstruct it yourself.

This separation lets Qiskit — and whichever backend is actually running the
job, simulator or real hardware — optimize execution and apply appropriate
error mitigation for the *kind* of answer being requested, instead of every
user reconstructing derived quantities from raw counts by hand each time.

Conceptual overview guide: <https://quantum.cloud.ibm.com/docs/en/guides/primitives>

## 9.2 `Sampler`

We used `StatevectorSampler` in file 5 — Qiskit's own ideal (noiseless)
reference implementation of the Sampler interface.
`qiskit_aer.primitives.SamplerV2` is the Aer-backed equivalent, letting you
plug in noise models and different simulation methods
(statevector / matrix-product-state / stabilizer, etc.):

```python
from qiskit_aer.primitives import SamplerV2 as AerSampler

aer_sampler = AerSampler()
job = aer_sampler.run([qc], shots=2000)
counts = job.result()[0].data.c.get_counts()
```

The *interface* — `.run([circuit], shots=...)` → job → `.result()` — stays
the same regardless of which implementation is underneath. That consistency
across simulators and real hardware is the entire point of the primitives
design: code written against the primitives interface doesn't need to
change when you swap a noiseless reference simulator for a noisy one, or
for a real IBM Quantum backend.

API references:
- Primitives module index: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/primitives>
- `StatevectorSampler`: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.primitives.StatevectorSampler>

## 9.3 `Estimator` and observables

`Estimator` needs two things per circuit: the circuit itself (**without**
measurements — expectation values are computed differently from sampling)
and an **observable**, expressed as a `SparsePauliOp` — a weighted sum of
tensor products of Pauli operators (I, X, Y, Z).

```python
from qiskit.quantum_info import SparsePauliOp
from qiskit.primitives import StatevectorEstimator

qc = QuantumCircuit(2)
qc.h(0)
qc.cx(0, 1)   # Bell state, no measurement

observable = SparsePauliOp("ZZ")
estimator = StatevectorEstimator()
job = estimator.run([(qc, observable)])
print(job.result()[0].data.evs)   # ~1.0
```

`SparsePauliOp` API reference:
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.quantum_info.SparsePauliOp>

## 9.4 PUBs — Primitive Unified Blocs

The list you pass to `.run()` is a list of **PUBs** (Primitive Unified
Blocs) — the standard input unit for both primitives:

- Sampler PUB: a circuit (with measurements), optionally with parameter
  values if the circuit is parameterized.
- Estimator PUB: a `(circuit, observable[, parameter_values])` tuple.

Batching multiple PUBs into a single `.run()` call (rather than issuing many
separate calls) is the recommended pattern for efficiency, especially
against real hardware, where each separate job submission carries queueing
overhead.

## 9.5 Where primitives fit relative to backends

Historically, `backend.run()` targeted a specific `Backend` object directly.
Primitives instead wrap a backend (or simulator) behind a stable interface;
you generally construct a primitive by pointing it at a `Backend` (for real
hardware access via IBM Quantum) or use one of the pure-software reference
implementations (`StatevectorSampler`/`StatevectorEstimator`, or Aer's
`SamplerV2`/`EstimatorV2`) when you don't need real hardware. Getting set up
with real-hardware primitives:
<https://quantum.cloud.ibm.com/docs/en/guides/get-started-with-backend-primitives>

## Further reading

- Primitives conceptual guide (start here if you want the "why," not just
  the "how"): <https://quantum.cloud.ibm.com/docs/en/guides/primitives>
- Exact simulation with the pure-Qiskit primitives (no Aer dependency):
  <https://quantum.cloud.ibm.com/docs/en/guides/simulate-with-qiskit-sdk-primitives>
- Full primitives module API: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/primitives>
