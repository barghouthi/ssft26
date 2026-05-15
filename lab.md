# Lab

The goal of this lab is to get your hands dirty building three things:

1. an **interpreter** for quantum circuits,
2. a simple **verifier**, and
3. an **optimizer**.

## Step 1: Represent circuits

Pick a representation for quantum states and operations. You have (at least) two options:

- a **vector / matrix** representation — see [Barghouthi's blog post](https://barghouthi.github.io/2021/08/05/quantum/).
- a **set / stabilizer-style** representation — see [this paper](https://eprint.iacr.org/2025/1091.pdf).

Ideally, try **both** and see which one is more ergonomic for the rest of the lab.

## Step 2: Implement gates

Implement a reasonable set of standard gates, for example:

- Single-qubit: `I`, `H`, `X`, `Y`, `Z`, `S`, `T`
- Two-qubit: `CNOT`, `CZ`, `SWAP`
- Measurement (in the computational basis)

## Step 3: Build the interpreter

Write a function that, given a circuit on `n` qubits and an input state, produces the output state. Make sure you can compose gates and apply them to the right qubits.

## Step 4: Run some algorithms

Implement a few standard small algorithms and **observe their outputs**:

- creating an **EPR pair** (Bell state),
- **Deutsch's algorithm**,
- quantum **teleportation**.

Does the output match what you expect?

## Step 5: Build the verifier

Implement a verifier that takes two circuits and **proves their equivalence** — i.e., decides whether they compute the same unitary (up to global phase).

Sanity checks:

- `H · H ≡ I`
- `CNOT · CNOT ≡ I`
- your EPR-pair circuit ≡ any other circuit producing the same Bell state from `|00⟩`

## Step 6: Build an optimizer

Use your verifier to drive a simple rewrite-based optimizer: apply local rules (e.g., `H · H → ε`, `X · X → ε`, `CNOT · CNOT → ε`) until a fixed point, and check equivalence after each pass.

## Step 7: Implement the 3-qubit bit-flip code

A short detour into quantum error correction. The 3-qubit bit-flip code protects a single logical qubit against a single `X` (bit-flip) error by encoding it into three physical qubits.

### 7a. Encoder

Build a circuit that takes a single-qubit input `|ψ⟩ = α|0⟩ + β|1⟩` and two ancillas initialized to `|0⟩`, and produces the encoded state

```
α|000⟩ + β|111⟩
```

Hint: two `CNOT`s, both controlled on the data qubit.

### 7b. Noise channel

Implement a "noise" stage that applies an `X` to *exactly one* of the three qubits. For testing, parametrize it by which qubit flips (0, 1, 2, or none) so you can sweep all four cases.

### 7c. Syndrome extraction & decoder

Add two more ancillas and use `CNOT`s to compute parity bits that identify *which* qubit (if any) was flipped. Then apply a corrective `X` to the flagged qubit.

**Stretch:** what happens if *two* qubits flip? Convince yourself (and your verifier) that this code can detect a single error but not two.
