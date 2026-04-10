## Error Correction Concepts

Real quantum computers are noisy — qubits decohere and gates have errors.

```
Quantum Error Sources
──────────────────────────────────────────────────────
Bit flip error:     |0⟩ → |1⟩  (like classical bit flip)
Phase flip error:   |+⟩ → |−⟩  (no classical analogue)
Depolarization:     random X, Y, or Z error
Decoherence:        entanglement with environment
Gate errors:        imperfect gate implementation
Measurement errors: wrong result read out
──────────────────────────────────────────────────────
```

```qsharp
/// 3-qubit bit-flip code (simplest quantum error correction)
operation EncodeLogicalQubit(data : Qubit, ancilla1 : Qubit, ancilla2 : Qubit) : Unit {
    // |0⟩ → |000⟩,  |1⟩ → |111⟩
    CNOT(data, ancilla1);
    CNOT(data, ancilla2);
}

operation CorrectBitFlip(q0 : Qubit, q1 : Qubit, q2 : Qubit) : Unit {
    use (anc1, anc2) = (Qubit(), Qubit());

    // Syndrome measurement (doesn't disturb logical state)
    CNOT(q0, anc1); CNOT(q1, anc1);
    CNOT(q1, anc2); CNOT(q2, anc2);

    let s1 = M(anc1);
    let s2 = M(anc2);

    // Correct based on syndrome
    if s1 == One and s2 == Zero { X(q0); }  // q0 flipped
    if s1 == One and s2 == One  { X(q1); }  // q1 flipped
    if s1 == Zero and s2 == One { X(q2); }  // q2 flipped

    ResetAll([anc1, anc2]);
}
```

---

## Quantum Libraries

```qsharp
// Available namespaces in QDK
Microsoft.Quantum.Intrinsic     // Core gates: H, X, Y, Z, CNOT, M
Microsoft.Quantum.Canon         // Algorithm building blocks
Microsoft.Quantum.Arithmetic    // Quantum arithmetic circuits
Microsoft.Quantum.Chemistry     // Molecular simulation
Microsoft.Quantum.MachineLearning // Quantum ML
Microsoft.Quantum.Cryptography  // Quantum key distribution
Microsoft.Quantum.Simulation    // Hamiltonian simulation
Microsoft.Quantum.Diagnostics   // DumpMachine, Assert
Microsoft.Quantum.Math          // PI(), Sqrt(), Log(), etc.
Microsoft.Quantum.Convert       // Type conversion utilities
Microsoft.Quantum.Arrays        // Array manipulation
Microsoft.Quantum.Measurement   // Measurement helpers
```

---

## Running Q# Programs

### Setup

```bash
# Install .NET 8 SDK first, then:
dotnet add package Microsoft.Quantum.Development.Kit
dotnet add package Microsoft.Quantum.Simulators

# Or with Python
pip install qsharp azure-quantum

# Create a new Q# project
dotnet new qsharp -o MyQuantumProject
cd MyQuantumProject
dotnet run
```

### Python Integration

```python
import qsharp

# Compile and run Q# operation from Python
from MyNamespace import MyOperation

# Run on simulator
result = MyOperation.simulate(theta=1.57)
print(result)

# Estimate resources without running
resources = MyOperation.estimate_resources(theta=1.57)
print(f"T gates: {resources['T']}")
print(f"CNOT gates: {resources['CNOT']}")
print(f"Qubits: {resources['Width']}")

# Run on Azure Quantum (real hardware)
from azure.quantum import Workspace
workspace = Workspace(
    resource_id="/subscriptions/.../quantumworkspace",
    location="eastus"
)
result = MyOperation.submit(workspace=workspace, target="ionq.simulator")
```

### Jupyter Notebook

```
%% cell 1 (magic command)
import qsharp

%% cell 2
%%qsharp
operation BellTest() : (Result, Result) {
    use (q1, q2) = (Qubit(), Qubit());
    H(q1);
    CNOT(q1, q2);
    let (r1, r2) = (M(q1), M(q2));
    ResetAll([q1, q2]);
    return (r1, r2);
}

%% cell 3 (Python cell)
results = [BellTest.simulate() for _ in range(100)]
# results will show (Zero,Zero) and (One,One) ~50/50
# Never (Zero,One) or (One,Zero) — perfect correlation!
```

---

## Quick Reference

```
Gate Cheatsheet
────────────────────────────────────────────────
H(q)              Hadamard (superposition)
X(q)              Pauli-X (NOT)
Y(q)              Pauli-Y
Z(q)              Pauli-Z (phase flip)
S(q)              Phase gate  (Z^½)
T(q)              π/8 gate    (Z^¼)
CNOT(ctrl, tgt)   Controlled-NOT
CZ(ctrl, tgt)     Controlled-Z
SWAP(q1, q2)      Swap
CCNOT(c1,c2,t)    Toffoli (AND gate)
Rx(angle, q)      Rotate X
Ry(angle, q)      Rotate Y
Rz(angle, q)      Rotate Z
M(q)              Measure (Zero or One)
Reset(q)          Reset to |0⟩
────────────────────────────────────────────────

Key Concepts
────────────────────────────────────────────────
Superposition      H|0⟩ = |+⟩ = (|0⟩+|1⟩)/√2
Entanglement       CNOT(H|0⟩, |0⟩) = |Φ⁺⟩
Measurement        Collapses superposition
No-cloning         Cannot copy unknown quantum state
Adjoint            Inverse operation (†)
Controlled         Apply op only if control = |1⟩
────────────────────────────────────────────────
```

---

## Quantum Advantage (When Does Quantum Win?)

```
Problem Type                 Speedup
──────────────────────────────────────────────────────────────
Unstructured search          √N  (Grover's)
Integer factoring            Exponential (Shor's)
Discrete logarithm           Exponential (Shor's)
Simulating quantum systems   Exponential (natural!)
Linear systems (HHL)         Exponential (under conditions)
Optimization (QAOA)          Potential polynomial speedup
Machine learning (QML)       Active research area
──────────────────────────────────────────────────────────────
NOT faster for: sorting, most graph problems, classical ML
```

---

*Q#: Programming the quantum future, one qubit at a time. Because when your computer lives in superposition, your code should too. ⚛️*
