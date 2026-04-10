## Control Flow

```qsharp
operation ControlFlowDemo(n : Int) : Unit {
    // if / elif / else
    if n > 10 {
        Message("Large");
    } elif n > 5 {
        Message("Medium");
    } else {
        Message("Small");
    }

    // for loop (over range or array)
    for i in 0..n-1 {
        Message($"Iteration {i}");
    }

    for item in ["a", "b", "c"] {
        Message(item);
    }

    // while loop
    mutable x = 0;
    while x < 10 {
        set x += 1;
    }

    // repeat-until — unique to Q# (crucial for quantum algorithms!)
    // Used because quantum operations are probabilistic
    mutable success = false;
    repeat {
        use q = Qubit();
        H(q);
        let r = M(q);
        if r == One {
            set success = true;
        }
        Reset(q);
    } until success
    fixup {
        // optional cleanup between attempts
    }

    // within-apply — applies operation, then its adjoint
    use q = Qubit();
    within {
        H(q);    // "change of basis"
    } apply {
        Z(q);    // operation in new basis
    }
    // H is automatically applied again (adjoint of H = H)
}
```

---

## Quantum Algorithms

### Bell State Preparation (Entanglement)

```qsharp
/// # Summary
/// Prepares one of the four Bell states (maximally entangled pairs)
///
/// # Input
/// ## q1, q2: The two qubits to entangle
/// ## index: 0=Φ⁺, 1=Φ⁻, 2=Ψ⁺, 3=Ψ⁻
operation PrepareBellState(q1 : Qubit, q2 : Qubit, index : Int) : Unit {
    // Start: |00⟩
    H(q1);         // → (|0⟩+|1⟩)|0⟩ / √2
    CNOT(q1, q2);  // → (|00⟩+|11⟩) / √2  =  |Φ⁺⟩

    // Adjust phase/bit for other Bell states
    if index == 1 { Z(q1); }  // |Φ⁻⟩
    if index == 2 { X(q1); }  // |Ψ⁺⟩
    if index == 3 { X(q1); Z(q1); }  // |Ψ⁻⟩
}
```

### Quantum Teleportation

```qsharp
/// Teleports the quantum state of |msg⟩ to |here⟩
/// Uses one Bell pair (q1, q2) as the quantum channel
operation Teleport(msg : Qubit, q1 : Qubit, q2 : Qubit, here : Qubit) : Unit {
    // Step 1: Prepare Bell pair
    H(q1);
    CNOT(q1, q2);

    // Step 2: Bell measurement on (msg, q1)
    CNOT(msg, q1);
    H(msg);
    let m1 = M(msg);
    let m2 = M(q1);

    // Step 3: Classical communication & correction
    if m2 == One { X(here); }  // bit flip correction
    if m1 == One { Z(here); }  // phase flip correction

    // 'here' now has the exact state that 'msg' had!
    // (msg is now destroyed — no-cloning theorem upheld)
}
```

### Grover's Search Algorithm

```qsharp
/// Grover's algorithm: finds a marked item in an unsorted database
/// Classical:  O(N) queries    Quantum: O(√N) queries
operation GroverSearch(nQubits : Int, oracle : (Qubit[] => Unit)) : Result[] {
    use qubits = Qubit[nQubits];

    // Initialize superposition over all states
    ApplyToEach(H, qubits);

    // Optimal number of iterations ≈ π/4 * √N
    let N = 1 <<< nQubits;  // 2^nQubits
    let iterations = Round(PI() / 4.0 * Sqrt(IntAsDouble(N)));

    for _ in 1..iterations {
        // Step 1: Oracle marks the target state (phase kickback)
        oracle(qubits);

        // Step 2: Diffusion operator (inversion about average)
        within {
            ApplyToEach(H, qubits);
            ApplyToEach(X, qubits);
        } apply {
            Controlled Z(Most(qubits), Tail(qubits));
        }
    }

    // Measure — should return target state with high probability
    return MultiM(qubits);
}
```

### Quantum Fourier Transform (QFT)

```qsharp
/// QFT: the quantum version of Discrete Fourier Transform
/// Used in Shor's algorithm for factoring, phase estimation, etc.
operation QFT(qubits : Qubit[]) : Unit is Adj + Ctl {
    let n = Length(qubits);

    for i in 0..n-1 {
        H(qubits[i]);  // Hadamard on current qubit

        for j in i+1..n-1 {
            let angle = 2.0 * PI() / IntAsDouble(1 <<< (j - i + 1));
            Controlled Rz([qubits[j]], angle, qubits[i]);
        }
    }

    // Bit reversal (swap qubits in reverse order)
    for i in 0..n/2-1 {
        SWAP(qubits[i], qubits[n - 1 - i]);
    }
}
```

---

