# Q# (Q-sharp) ⚛️

> *"Quantum computing will change everything — Q# gives you the tools to program that change."*
> — Microsoft Quantum

---

## Table of Contents

1. [Overview](#overview)
2. [Quantum Computing Fundamentals](#quantum-computing-fundamentals)
3. [Q# and the Microsoft Quantum Stack](#q-and-the-microsoft-quantum-stack)
4. [Qubits and Quantum State](#qubits-and-quantum-state)
5. [Q# Language Basics](#q-language-basics)
6. [Operations and Functions](#operations-and-functions)
7. [Quantum Gates](#quantum-gates)
8. [Measurements](#measurements)
9. [Control Flow](#control-flow)
10. [Quantum Algorithms](#quantum-algorithms)
11. [Error Correction Concepts](#error-correction-concepts)
12. [Quantum Libraries](#quantum-libraries)
13. [Running Q# Programs](#running-q-programs)

---

## Overview

**Q#** (pronounced "Q-sharp") is a **domain-specific programming language** developed by Microsoft for expressing quantum algorithms. It is part of the **Microsoft Quantum Development Kit (QDK)** and integrates deeply with the classical computing world through Python and .NET interoperability.

```
Classical Computing vs Quantum Computing
─────────────────────────────────────────────────────────────────
CLASSICAL                           QUANTUM
─────────────────────────────────────────────────────────────────
Bit: 0 OR 1                        Qubit: 0 AND 1 (superposition)
Deterministic                      Probabilistic
Sequential logic gates             Quantum gates (unitary matrices)
NOT, AND, OR, XOR                  H, X, Y, Z, CNOT, T, S gates
Data cannot be copied easily       No-cloning theorem (cannot copy)
Reads don't disturb state          Measurement collapses state
Best for: most computation         Best for: specific hard problems
─────────────────────────────────────────────────────────────────
```

**Key Stats:**
- Created: Microsoft Research (~2017, publicly launched 2019)
- Paradigm: Functional + Quantum-specific constructs
- Integration: Python, C#, Jupyter Notebooks
- Runs on: Simulators and real quantum hardware (Azure Quantum)
- File extension: `.qs`

---

## Quantum Computing Fundamentals

Before diving into Q#, understanding the physics is essential.

### The Qubit

```
Classical Bit          Quantum Bit (Qubit)
──────────────         ──────────────────────────────────────
   ┌───┐                        │0⟩
   │ 0 │                       /│\
   └───┘                      / │ \
   ┌───┐              α│0⟩ + β│1⟩   ← superposition
   │ 1 │                      \ │ /
   └───┘                       \│/
                                │1⟩

Classical: definitely 0 or 1
Quantum: α and β are complex amplitudes
|α|² + |β|² = 1 (probabilities must sum to 1)
```

### Bloch Sphere — Visual Qubit Representation

```
                   |0⟩ (north pole)
                    │
                    │  ← Pure |0⟩ state
                    │
         ───────────┼─────────── equator (superpositions)
                    │
                    │  ← Pure |1⟩ state
                    │
                   |1⟩ (south pole)

Points on the sphere surface = pure quantum states
Points inside = mixed states
|+⟩ = (|0⟩ + |1⟩)/√2  lives on the equator (equal superposition)
|−⟩ = (|0⟩ - |1⟩)/√2  also on equator, opposite side
```

### Dirac Notation (Bra-Ket)

```
|ψ⟩  "ket"   — a quantum state (column vector)
⟨ψ|  "bra"   — conjugate transpose (row vector)
⟨φ|ψ⟩       — inner product (probability amplitude)

|0⟩ = [1, 0]ᵀ   (computational basis state)
|1⟩ = [0, 1]ᵀ   (computational basis state)

General qubit: |ψ⟩ = α|0⟩ + β|1⟩ = [α, β]ᵀ
```

### Entanglement

```
Entangled Bell State (Φ⁺):
|Φ⁺⟩ = (|00⟩ + |11⟩) / √2

Meaning: If you measure qubit 1 and get 0, qubit 2 is INSTANTLY 0.
         If you measure qubit 1 and get 1, qubit 2 is INSTANTLY 1.
         No matter how far apart they are — "spooky action at a distance"
         (Einstein's words)

This cannot be explained classically — it's genuinely quantum!
```

---

