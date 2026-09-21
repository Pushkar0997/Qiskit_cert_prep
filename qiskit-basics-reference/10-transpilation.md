# 10. Transpilation

## 10.1 Why transpilation exists

A circuit you write using `h`, `cx`, `ry`, etc. is usually **not** directly
executable on a given real backend. Real hardware supports only a small
**basis gate set** (e.g. maybe just `{RZ, SX, X, CX}` on IBM superconducting
devices) and has a fixed **connectivity graph** — not every qubit can
interact directly with every other qubit. **Transpilation** rewrites your
circuit into an equivalent one that respects both constraints, while trying
to keep the resulting circuit as small and shallow as possible.

```python
from qiskit import QuantumCircuit, transpile
from qiskit_aer import AerSimulator

qc = QuantumCircuit(3)
qc.h(0)
qc.cx(0, 1)
qc.cx(1, 2)
qc.ry(0.7, 2)

backend = AerSimulator()
transpiled = transpile(qc, backend=backend, optimization_level=3)

print(qc.count_ops(), qc.depth())
print(transpiled.count_ops(), transpiled.depth())
```

API reference for `transpile`:
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/compiler>

Full transpiler module (the underlying `PassManager`/`PassManagerConfig`
machinery, if you want to go deeper than the single `transpile()`
convenience function):
<https://quantum.cloud.ibm.com/docs/en/api/qiskit/transpiler>

Conceptual how-to guide: <https://quantum.cloud.ibm.com/docs/en/guides/transpile>

## 10.2 Two numbers worth caring about after transpiling

- **Depth** — the number of sequential gate "layers"; roughly, how long the
  circuit takes to run and how much noise/decoherence accumulates on real
  hardware before you get to measure.
- **Gate counts, especially two-qubit gate count** — two-qubit gates
  (typically CX or a hardware-native equivalent) are usually far noisier
  than single-qubit gates on real devices, so two-qubit gate count is often
  the single best proxy for "how much will this circuit suffer from
  hardware noise" when comparing two candidate circuits for the same task.

## 10.3 Optimization levels

`transpile(..., optimization_level=N)` for `N` in `{0, 1, 2, 3}`:

- `0` — no optimization; fastest to transpile, output circuit closely
  mirrors the input structurally (just gate-mapped and routed).
- `1`–`2` — progressively more aggressive gate cancellation, commutation
  analysis, and re-synthesis.
- `3` — the most aggressive optimization; slowest to transpile, but often
  produces meaningfully fewer gates / less depth in the output, especially
  for circuits with a lot of redundant or cancellable structure.

Guide specifically on choosing/tuning this:
<https://quantum.cloud.ibm.com/docs/en/guides/set-optimization>

## 10.4 Routing and SWAP insertion

On a simulator like `AerSimulator` with no coupling-map restriction (the
default, "fully connected" case), transpilation mostly just optimizes gate
counts. On real backends — or a simulator explicitly configured with a
specific `coupling_map` to mimic one — transpilation will also insert
**SWAP** gates (file 7, section 7.4) wherever a two-qubit gate is requested
between qubits that aren't directly connected on the hardware's topology.
This is exactly why connectivity, and not just raw qubit count, is a major
factor in how well a given real device suits a given circuit: a circuit
that looks small in your source code can transpile into something much
larger once routed onto a sparsely-connected device.

Guide on representing device topology/constraints explicitly:
<https://quantum.cloud.ibm.com/docs/en/guides/represent-quantum-computers>

## 10.5 Transpiler stages, briefly

Under the hood, `transpile()` runs your circuit through a sequence of
named stages (init → layout → routing → translation → optimization →
scheduling), each a `PassManager` you can inspect or customize individually
for advanced use cases (custom backends, research on new transpiler
passes, etc.). Full breakdown of the stages:
<https://quantum.cloud.ibm.com/docs/en/guides/transpiler-stages>

This level of detail is beyond "basics," but knowing the stages exist
demystifies what `transpile()` is actually doing rather than treating it as
an opaque black box, and it's directly relevant if the certification asks
about transpiler *behavior* (e.g. "which stage handles qubit routing")
rather than just the top-level function call.

## Further reading

- `transpile()` API: <https://quantum.cloud.ibm.com/docs/en/api/qiskit/compiler>
- Transpiler module and `PassManager` machinery:
  <https://quantum.cloud.ibm.com/docs/en/api/qiskit/transpiler>
- Conceptual guide: <https://quantum.cloud.ibm.com/docs/en/guides/transpile>
- Optimization levels guide: <https://quantum.cloud.ibm.com/docs/en/guides/set-optimization>
- Transpiler stages guide: <https://quantum.cloud.ibm.com/docs/en/guides/transpiler-stages>
