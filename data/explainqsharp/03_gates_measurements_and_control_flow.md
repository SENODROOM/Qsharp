## Operations and Functions

Q# distinguishes between **operations** (quantum, can have side effects) and **functions** (classical, pure, no quantum operations).

```qsharp
// OPERATION — can use quantum operations, allocate qubits
operation PrepareAndMeasure(theta : Double) : Result {
    use q = Qubit();

    // Rotation gate around Y axis by theta radians
    Ry(theta, q);

    // Measure and return
    return M(q);
}

// FUNCTION — purely classical, deterministic, no side effects
function Factorial(n : Int) : Int {
    if n <= 1 {
        return 1;
    }
    return n * Factorial(n - 1);
}

// Adjoint — the "inverse" of an operation (dagger †)
// Q# can auto-generate adjoints with 'is Adj'
operation PhaseKickback() : Unit is Adj + Ctl {
    use (control, target) = (Qubit(), Qubit());
    H(target);
    CNOT(control, target);
    // Adjoint.PhaseKickback() would undo this
    Adjoint CNOT(control, target);  // undo CNOT
    ResetAll([control, target]);
}

// Controlled — run operation conditioned on control qubit(s)
operation ApplyIfOne(q : Qubit) : Unit is Ctl {
    X(q);  // Flip qubit
}

// Usage:
// Controlled ApplyIfOne([control], target);
// = CNOT(control, target)

// Higher-order operations
operation ApplyTwice(op : (Qubit => Unit), q : Qubit) : Unit {
    op(q);
    op(q);
}
// ApplyTwice(H, qubit); // apply H twice → back to |0⟩ or |1⟩
```

---

## Quantum Gates

```
Common Quantum Gates (single qubit)
──────────────────────────────────────────────────────────
Gate  │ Symbol │ Effect                        │ Matrix
──────┼────────┼───────────────────────────────┼──────────────────
I     │  I     │ Identity (do nothing)          │ [[1,0],[0,1]]
X     │  ─■─   │ Pauli-X (quantum NOT gate)    │ [[0,1],[1,0]]
Y     │  ─Y─   │ Pauli-Y (bit+phase flip)      │ [[0,-i],[i,0]]
Z     │  ─Z─   │ Pauli-Z (phase flip)          │ [[1,0],[0,-1]]
H     │  ─H─   │ Hadamard (superposition)      │ [[1,1],[1,-1]]/√2
S     │  ─S─   │ Phase gate (T²)               │ [[1,0],[0,i]]
T     │  ─T─   │ π/8 gate                      │ [[1,0],[0,e^iπ/4]]
Rx(θ) │  ─Rx─  │ Rotation around X by θ        │ cos,sin matrix
Ry(θ) │  ─Ry─  │ Rotation around Y by θ        │ cos,sin matrix
Rz(θ) │  ─Rz─  │ Rotation around Z by θ        │ phase matrix
──────────────────────────────────────────────────────────

Two-Qubit Gates:
CNOT  │ Control──●──Target  │ Flip target if control=|1⟩
SWAP  │ ─×──×─              │ Swap two qubits
CZ    │ Control──●──Z       │ Phase flip if both are |1⟩
```

```qsharp
// Gate application in Q#
operation DemoGates() : Unit {
    use (q1, q2, q3) = (Qubit(), Qubit(), Qubit());

    // Single-qubit gates
    X(q1);           // NOT gate: |0⟩ → |1⟩
    H(q1);           // Hadamard: |1⟩ → |−⟩
    Z(q1);           // Phase flip
    S(q1);           // π/2 phase
    T(q1);           // π/4 phase
    Y(q1);           // Pauli Y

    // Rotation gates
    Rx(0.5, q1);     // Rotate around X by 0.5 radians
    Ry(1.57, q1);    // Rotate around Y by π/2
    Rz(3.14, q1);    // Rotate around Z by π

    // Two-qubit gates
    CNOT(q1, q2);    // Controlled-NOT
    CZ(q1, q2);      // Controlled-Z
    SWAP(q1, q2);    // Swap states

    // Three-qubit gate
    CCNOT(q1, q2, q3);  // Toffoli gate (AND gate!)

    ResetAll([q1, q2, q3]);
}
```

---

## Measurements

```qsharp
open Microsoft.Quantum.Measurement;

operation MeasurementDemo() : Unit {
    use q = Qubit();
    H(q);  // superposition

    // Standard measurement in computational basis (Z basis)
    let result = M(q);  // returns Zero or One
    if result == One {
        Message("Got |1⟩");
    }

    // Measure in different bases
    use q2 = Qubit();
    H(q2);
    let xResult = Measure([PauliX], [q2]);  // X basis
    let yResult = Measure([PauliY], [q2]);  // Y basis

    // Measure multiple qubits at once
    use register = Qubit[3];
    ApplyToEach(H, register);
    let results = MultiM(register);  // Result[]

    // Convert Result[] to Int
    let value = ResultArrayAsInt(results);
    Message($"Measured integer: {value}");

    ResetAll(register);
}

// Non-destructive measurement via ancilla (advanced)
operation MeasureWithAncilla(q : Qubit) : Result {
    use ancilla = Qubit();
    CNOT(q, ancilla);             // entangle
    let result = M(ancilla);      // measure ancilla
    Reset(ancilla);               // reset ancilla
    return result;                // q is NOT collapsed!
}
```

---

