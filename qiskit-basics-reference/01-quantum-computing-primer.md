# 1. Quantum Computing Primer

## 1.1 The qubit as a vector

A classical bit is an element of `{0, 1}`. A qubit is a unit vector in a
2-dimensional complex Hilbert space, ℂ²:

```
|ψ⟩ = α|0⟩ + β|1⟩,      α, β ∈ ℂ,      |α|² + |β|² = 1
```

- `|0⟩ = (1, 0)ᵀ` and `|1⟩ = (0, 1)ᵀ` are the **computational basis states**.
- `α`, `β` are **probability amplitudes** — complex numbers, not probabilities
  themselves.
- The **Born rule** says: measuring `|ψ⟩` in the computational basis returns
  `0` with probability `|α|²` and `1` with probability `|β|²`. The
  normalization condition is exactly "these probabilities sum to 1."

This bra-ket ("Dirac") notation is standard across all of quantum mechanics,
not just quantum computing. `⟨ψ|` denotes the conjugate transpose (a "bra"),
`|ψ⟩` is the "ket," and `⟨φ|ψ⟩` is their inner product.

Further reading:
- [Qubit — Wikipedia](https://en.wikipedia.org/wiki/Qubit)
- [Bra–ket notation — Wikipedia](https://en.wikipedia.org/wiki/Bra%E2%80%93ket_notation)
- IBM Quantum Learning, *Basics of Quantum Information* — the "Single systems"
  lesson covers exactly this material rigorously, with video lectures by
  John Watrous: <https://learning.quantum.ibm.com/course/basics-of-quantum-information>

## 1.2 Why amplitudes and not just probabilities?

The entire reason a qubit is *not* "just a probabilistic bit" is that
amplitudes are complex numbers that can **interfere**. Two computational
paths leading to the same measurement outcome can add constructively
(reinforcing) or destructively (cancelling) depending on their relative
phase — something a plain probability distribution (which only has
non-negative real numbers) can never do. Interference is the mechanism
behind essentially every quantum algorithmic speedup (Grover's search,
Shor's factoring, the quantum Fourier transform, etc.). If you remember one
sentence from this whole series, make it this one.

## 1.3 The Bloch sphere

Every single-qubit **pure state** can be written, up to an unobservable
global phase, as:

```
|ψ⟩ = cos(θ/2)|0⟩ + e^(iφ) sin(θ/2)|1⟩
```

and visualized as a point on the surface of a unit sphere — the **Bloch
sphere** — with polar angle `θ` and azimuthal angle `φ`.

- `θ` controls the 0-vs-1 probability split (`θ = 0` → certainly `|0⟩`;
  `θ = π` → certainly `|1⟩`; `θ = π/2` → 50/50).
- `φ` is the **relative phase** between the `|0⟩` and `|1⟩` components. It's
  invisible if you only ever measure in the computational (Z) basis, but it's
  exactly what distinguishes states like `|+⟩ = (|0⟩+|1⟩)/√2` from
  `|−⟩ = (|0⟩−|1⟩)/√2` — both have a 50/50 split in Z-basis measurement
  probability, but they are very different states, and gates downstream will
  treat them very differently.
- The north pole is `|0⟩`, the south pole is `|1⟩`, the `+X` axis is `|+⟩`,
  the `+Y` axis is `|+i⟩ = (|0⟩+i|1⟩)/√2`.

Further reading:
- [Bloch sphere — Wikipedia](https://en.wikipedia.org/wiki/Bloch_sphere)
- Qiskit's `plot_bloch_vector` / `plot_bloch_multivector` functions render
  exactly this picture from a `Statevector` — see
  <https://quantum.cloud.ibm.com/docs/en/guides/plot-quantum-states> and the
  API reference at
  <https://quantum.cloud.ibm.com/docs/en/api/qiskit/visualization>

## 1.4 Measurement is destructive and probabilistic

Measuring `|ψ⟩ = α|0⟩ + β|1⟩` in the computational basis:

1. Returns the classical outcome `0` (probability `|α|²`) or `1`
   (probability `|β|²`).
2. **Collapses** the state to whichever basis state was observed. Any
   superposition information is gone — you cannot "measure a qubit a little
   bit" and keep the rest of the state intact.

This is also why the **no-cloning theorem** exists: it is provably
impossible to build a general-purpose device that takes an arbitrary unknown
quantum state and produces two independent copies of it. If cloning were
possible, you could sidestep the measurement collapse problem entirely (copy
the state many times, measure each copy, and reconstruct the amplitudes
exactly) — which would violate the uncertainty principle and allow
faster-than-light signalling, among other problems. No-cloning is one of the
deepest structural facts about quantum information, and it's why quantum
error correction and quantum key distribution look so different from their
classical counterparts.

Further reading:
- [No-cloning theorem — Wikipedia](https://en.wikipedia.org/wiki/No-cloning_theorem)
- [Measurement in quantum mechanics — Wikipedia](https://en.wikipedia.org/wiki/Measurement_in_quantum_mechanics)

## 1.5 Multi-qubit systems, in brief (full treatment in file 6)

An `n`-qubit system lives in a `2ⁿ`-dimensional space, built as the tensor
product of the individual qubit spaces. This exponential scaling
(`2ⁿ` grows extremely fast) is simultaneously the source of quantum
computing's potential power and the reason classical simulation of quantum
circuits becomes intractable past a few dozen qubits — which is exactly why
tools like Qiskit's `Statevector` and `AerSimulator` are only useful for
small systems, and why real quantum hardware exists at all.

## 1.6 Where this fits in the certification

The Qiskit v2.X developer certification exam's published competency areas
include "Understanding single-qubit gates and their rotations on the Bloch
sphere" as an explicit topic — so the primer above isn't optional
background, it's directly examinable material. See file 11 of this series
for the full breakdown of exam topics and where to study each one.
