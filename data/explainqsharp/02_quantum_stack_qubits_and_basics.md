## Q# and the Microsoft Quantum Stack

```
Microsoft Quantum Development Kit Architecture
──────────────────────────────────────────────────────────────────
┌──────────────────────────────────────────────────────────┐
│                Your Q# Code (.qs files)                   │
└────────────────────────────┬─────────────────────────────┘
                             │
┌────────────────────────────▼─────────────────────────────┐
│           Q# Compiler (QDK)                              │
│   Type checking, resource estimation, optimization        │
└────────────────────────────┬─────────────────────────────┘
                             │
            ┌────────────────┴────────────────┐
            │                                 │
┌───────────▼──────────┐          ┌───────────▼──────────┐
│  Quantum Simulators  │          │  Real Quantum Hardware│
│  ─────────────────── │          │  ─────────────────── │
│  • Full state vector │          │  Azure Quantum        │
│  • Sparse simulator  │          │  ─────────────────── │
│  • Noise simulator   │          │  • IonQ (trapped ion) │
│  • Toffoli simulator │          │  • Quantinuum         │
│  (runs on your CPU)  │          │  • Rigetti (supercond)│
└──────────────────────┘          └───────────────────────┘
```

---

## Qubits and Quantum State

```qsharp
// Q# uses namespaces like C#
namespace MyQuantumProgram {
    // Import standard quantum operations
    open Microsoft.Quantum.Canon;
    open Microsoft.Quantum.Intrinsic;
    open Microsoft.Quantum.Measurement;
    open Microsoft.Quantum.Diagnostics;

    @EntryPoint()
    operation HelloQuantum() : Unit {
        // Allocate a qubit — starts in |0⟩ state
        use q = Qubit();

        // Apply Hadamard gate — creates superposition
        H(q);  // Now q is |+⟩ = (|0⟩ + |1⟩)/√2

        // Dump the current quantum state (simulator only!)
        DumpMachine();

        // Measure — collapses to |0⟩ or |1⟩ (50/50)
        let result = M(q);
        Message($"Measured: {result}");

        // IMPORTANT: Reset qubit before releasing
        // (qubits must be in |0⟩ when returned to system)
        Reset(q);

    } // q is automatically released here

    // Allocating multiple qubits
    operation MultipleQubits() : Unit {
        use qubits = Qubit[5];    // array of 5 qubits

        // Or individually
        use (q1, q2, q3) = (Qubit(), Qubit(), Qubit());

        // Operations on qubit arrays
        ApplyToEach(H, qubits);    // Apply H to each qubit
        ResetAll(qubits);
        ResetAll([q1, q2, q3]);
    }
}
```

---

## Q# Language Basics

### Types

```qsharp
// Primitive types
let b   : Bool   = true;
let i   : Int    = 42;           // 64-bit signed integer
let big : BigInt = 100000L;      // arbitrary precision (L suffix)
let d   : Double = 3.14159;      // 64-bit float
let p   : Pauli  = PauliX;       // PauliX, PauliY, PauliZ, PauliI
let r   : Result = One;          // Zero or One (measurement result)
let rng : Range  = 0..2..10;     // start..step..end = 0,2,4,6,8,10

// Compound types
let tup : (Int, Bool, Double) = (1, true, 2.5);
let arr : Int[]  = [1, 2, 3, 4, 5];
let 2d  : Int[][] = [[1,2],[3,4]];

// String
let msg : String = $"Pi ≈ {d}";  // interpolated strings

// Unit — like void
let nothing : Unit = ();

// Qubit — cannot be in superpositions in classical variables!
// use q = Qubit();  // allocate inside use statement

// Operation type (quantum operations)
let op : (Qubit => Unit) = H;

// Function type (classical, pure)
let fn : (Int -> Int) = (x -> x * x);
```

### Variables and Mutability

```qsharp
// Immutable binding (default)
let x = 42;
// set x = 43;  ❌ Error: x is not mutable

// Mutable binding
mutable count = 0;
set count += 1;   // must use 'set' keyword

// Deconstruction
let (a, b) = (10, 20);
let [head, ...tail] = [1, 2, 3, 4];  // head=1, tail=[2,3,4]
```

---

