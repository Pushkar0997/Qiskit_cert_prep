# 5. Measurement, Sampling, and Shots

## 5.1 Where randomness actually enters

`Statevector` (file 4) gives you the *exact* quantum state — nothing random
about it. But that's not what a real quantum computer, or a shot-based
simulator standing in for one, gives you. A real device can only give you
**measurement outcomes**, sampled according to the Born-rule probabilities
described in file 1. You only learn the underlying probabilities themselves
indirectly, by repeating the circuit many times ("shots") and looking at the
resulting frequency distribution.

Qiskit v2.x's tool for this is the **Sampler primitive** (full treatment in
file 9). The exact-simulation reference implementation,
`qiskit.primitives.StatevectorSampler`, is enough to illustrate the idea —
it samples from the exact statevector distribution with no hardware noise
mixed in.

```python
from qiskit import QuantumCircuit
from qiskit.primitives import StatevectorSampler

qc = QuantumCircuit(1, 1)
qc.h(0)
qc.measure(0, 0)

sampler = StatevectorSampler()
job = sampler.run([qc], shots=4000)
counts = job.result()[0].data.c.get_counts()
print(counts)   # e.g. {'0': 2013, '1': 1987}
```

API reference: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/qiskit.primitives.StatevectorSampler>

## 5.2 A single shot tells you almost nothing

With `shots=1`, you get exactly one bit of information: `0` or `1`. That
outcome alone cannot distinguish "a perfectly fair 50/50 superposition" from
"a heavily biased state that happened to land on this outcome" from
"hardware noise flipped the result." Any probabilistic claim about a
circuit's behavior requires enough shots to be statistically meaningful.
This sounds obvious stated plainly, but it's the single most common
reasoning error in early Qiskit debugging: treating one anomalous run as
evidence of a bug, rather than ordinary statistical noise.

## 5.3 Statistical noise vs. hardware noise — a distinction worth being
precise about

- **Statistical (shot) noise**: even on a perfect, noiseless quantum
  computer, a finite number of shots gives you an *estimate* of the true
  probability, with an uncertainty that shrinks (roughly as `1/√shots`) as
  you take more shots. A 48%/52% result instead of a clean 50/50 split from
  4000 shots of `H|0⟩` is completely normal and expected — it is not a sign
  of anything wrong.
- **Hardware noise**: on a real device, gates are imperfect and qubits
  decohere over time, so the *true* underlying probability distribution
  itself is skewed away from the ideal one. More shots narrows your
  statistical uncertainty about whatever distribution you're sampling from,
  but does **not** remove hardware noise — that requires distinct
  techniques (error mitigation, error suppression, and eventually
  fault-tolerant error correction), which sit outside "basics" but are
  worth knowing exist.

Guide covering IBM's error mitigation/suppression tooling (for when you're
ready to go past "basics"):
<https://quantum.cloud.ibm.com/docs/en/guides/error-mitigation-and-suppression-techniques>

## 5.4 Counts, `Counts` objects, and visualization

The dictionary you get back from `.get_counts()` maps bitstrings to
observed frequencies. Qiskit provides dedicated visualization for this:

```python
from qiskit.visualization import plot_histogram
plot_histogram(counts, title="H|0> measured 4000 times")
```

Guide: <https://quantum.cloud.ibm.com/docs/en/guides/visualize-results>
API reference (all visualization functions):
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/visualization>

## 5.5 Where this sits relative to `Estimator`

`Sampler` gives you the bitstrings themselves — appropriate whenever the
bitstrings *are* the answer (e.g. Grover's algorithm, most sampling
problems, quantum simulation readout). `Estimator` instead gives you a
single expectation-value number computed from an observable, without ever
handing you raw bitstrings — appropriate when the number itself is the
answer (e.g. variational algorithms like VQE/QAOA, where the energy
expectation value is what you're optimizing). File 9 covers `Estimator` in
full; it's worth reading measurement/sampling (this file) before it, since
`Estimator`'s output can be understood as "what you'd get if you did the
Sampler-style measurement-and-average procedure, but computed directly and
exactly from the simulated or estimated state instead."
