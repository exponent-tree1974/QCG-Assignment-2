# Quantum Parity Oracle using Qiskit

## Overview

This code implements a quantum oracle that computes the parity of a 4-bit input using Qiskit.

The parity function takes four input bits and returns 1 if the number of ones is odd, and 0 if it is even. The result is stored in a target qubit.

---

## Key Idea

* Four input qubits: q0, q1, q2, q3
* One target qubit: q4
* Each input qubit controls a CNOT gate acting on the target
* The target qubit stores the XOR (parity) of all inputs

---

## Code Breakdown

### Import Required Libraries

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit.quantum_info import Statevector
```

---

### Create the Oracle Circuit

```python
qc = QuantumCircuit(5)
for i in range(4):
    qc.cx(i, 4)
qc.draw(output="mpl")
```

Explanation:

* A 5-qubit circuit is created
* Qubits q0 to q3 are inputs
* Qubit q4 is the target
* Each input qubit is connected to the target using a CNOT gate
* The target flips once for every input qubit in state 1

---

### Convert Circuit into a Gate

```python
oracle = qc.to_gate(label="Parity")
```

Explanation:

* The circuit is converted into a reusable quantum gate
* The label "Parity" is assigned for clarity
* This allows the oracle to be reused in larger circuits

---

### Apply Oracle to a New Circuit

```python
new = QuantumCircuit(5)
new.append(oracle, [0, 1, 2, 3, 4])
new.draw(output="mpl")
```

Explanation:

* A new circuit is created
* The oracle gate is applied to all five qubits
* This keeps the design modular

---

### Get the Quantum State

```python
state = Statevector.from_instruction(new)
display(state.draw('latex'))
```

Explanation:

* The circuit is simulated using the statevector method
* The final quantum state is displayed

---

## What This Circuit Does

* If the number of input qubits in state 1 is even, the target qubit remains 0
* If the number is odd, the target qubit becomes 1

---

## Applications

* Quantum algorithms such as Grover’s algorithm and Deutsch–Jozsa algorithm
* Error detection using parity checks
* Construction of quantum oracles

---

## Summary

* A parity oracle is implemented using CNOT gates
* The circuit is converted into a reusable gate
* The oracle is applied to a new circuit
* The result is analyzed using statevector simulation

---

## Note

* No measurements are used in this implementation
* The result is obtained through quantum state simulation

---


# Classical Analysis of a Parity Function

## Overview

This code evaluates whether a Boolean function is **balanced**, **constant**, or neither.

The function computes the parity of four input bits using the XOR operation.

---

## Function Definition

```python
def parity(x1, x2, x3, x4):
    return x1 ^ x2 ^ x3 ^ x4
```

Explanation:

* The `^` operator represents XOR (exclusive OR)
* The function returns:

  * `1` if an odd number of inputs are 1
  * `0` if an even number of inputs are 1

---

## Counting Outputs

```python
count_0 = 0
for x1 in range(2):
    for x2 in range(2):
        for x3 in range(2):
            for x4 in range(2):
                if parity(x1, x2, x3, x4) == 0:
                    count_0 += 1
```

Explanation:

* All possible 4-bit inputs are generated (total 16 combinations)
* For each input, the parity function is evaluated
* The number of times the output is `0` is counted

---

## Classification Logic

```python
if count_0 == 8:
    print("balanced")
elif count_0 == 0 or count_0 == 16:
    print("constant")
else:
    print("none")
```

Explanation:

* A Boolean function is:

  * **Balanced** if it outputs 0 exactly half the time (8 out of 16 cases)
  * **Constant** if it always outputs the same value (0 or 1)
* Any other case is classified as "none"

---

## Result

For the parity function:

* Exactly half of the inputs produce 0
* The other half produce 1

Therefore, the function is classified as:

```
balanced
```

---

## Key Insight

The parity function is inherently balanced because:

* Every change in one input bit flips the output
* Across all possible inputs, zeros and ones are evenly distributed

---

## Summary

* The code evaluates all possible inputs of a 4-bit Boolean function
* It counts how often the output is 0
* Based on this count, it classifies the function
* The parity function is confirmed to be balanced

---


# Quantum Parity Oracle with Superposition (Deutsch–Jozsa Style)

## Overview

This code constructs and applies a parity oracle in a quantum circuit. The circuit prepares input qubits in superposition and uses an ancilla qubit to encode the function output through phase kickback.

This setup is similar to the structure used in the Deutsch–Jozsa algorithm.

---

## Key Idea

* Four input qubits: q0, q1, q2, q3
* One ancilla qubit: q4
* Input qubits are placed in superposition using Hadamard gates
* The ancilla qubit is prepared in a special state to enable phase kickback
* The oracle encodes the parity function

---

## Code Breakdown

### Import Required Libraries

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit.quantum_info import Statevector
```

---

### Build the Parity Oracle

```python
qc = QuantumCircuit(5)
for i in range(4):
    qc.cx(i, 4)
oracle = qc.to_gate(label="Parity")
```

Explanation:

* A 5-qubit circuit is created
* The first four qubits act as inputs
* The fifth qubit is the target
* Each input qubit controls a CNOT gate targeting the fifth qubit
* This computes the parity of the inputs

---

### Prepare the Main Circuit

```python
ckt = QuantumCircuit(5)
for i in range(4):
    ckt.h(i)
```

Explanation:

* Hadamard gates are applied to the first four qubits
* This creates a superposition of all possible input states

---

### Prepare the Ancilla Qubit

```python
ckt.x(4)
ckt.h(4)
```

Explanation:

* The ancilla qubit is first flipped to state 1 using an X gate
* Then a Hadamard gate is applied
* This prepares the ancilla in a state that allows phase kickback

---

### Apply the Oracle

```python
ckt.append(oracle, [0, 1, 2, 3, 4])
```

Explanation:

* The parity oracle is applied to all five qubits
* Instead of storing the result directly, the oracle encodes it into the phase of the quantum state

---

### Get the Final State

```python
state = Statevector.from_instruction(ckt)
display(ckt.draw(output="mpl"))
display(state.draw("latex"))
```

Explanation:

* The circuit is simulated using the statevector method
* The circuit diagram is displayed
* The final quantum state is shown

---

## What This Circuit Demonstrates

* Superposition allows evaluation of all inputs simultaneously
* The oracle computes parity using CNOT gates
* Phase kickback encodes the function output into the quantum state
* The result is stored in amplitudes rather than classical bits

---

## Conceptual Insight

This circuit demonstrates how quantum computation differs from classical computation:

* Classical approach evaluates inputs one by one
* Quantum approach evaluates all inputs at once using superposition
* The result is encoded in the phase of the state

---

## Applications

* Deutsch–Jozsa algorithm
* Quantum algorithm design using oracles
* Understanding phase kickback
* Foundations of quantum speedup

---

## Summary

* A parity oracle is constructed using CNOT gates
* Input qubits are placed in superposition
* An ancilla qubit is prepared for phase kickback
* The oracle is applied to encode parity into the quantum state
* The final state is analyzed using statevector simulation

---

## Note

* No measurements are used in this circuit
* The result is observed through the quantum state rather than classical output

---


# Deutsch–Jozsa Algorithm for Parity Function using Qiskit

## Overview

This code implements the Deutsch–Jozsa algorithm to determine whether a Boolean function is **constant** or **balanced**.

The oracle used here computes the parity of four input bits. The algorithm uses quantum parallelism and interference to classify the function in a single evaluation.

---

## Key Idea

* Four input qubits: q0, q1, q2, q3
* One ancilla qubit: q4
* Four classical bits for measurement
* The oracle computes the parity function
* The algorithm determines whether the function is constant or balanced

---

## Code Breakdown

### Import Required Libraries

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit.quantum_info import Statevector
```

---

### Construct the Oracle

```python
qc = QuantumCircuit(5)
for i in range(4):
    qc.cx(i, 4)
oracle = qc.to_gate(label="Uf")
```

Explanation:

* A 5-qubit circuit is created
* The first four qubits are inputs
* The fifth qubit is the target
* Each input qubit controls a CNOT gate acting on the target
* This implements the parity function

---

### Create Registers and Circuit

```python
c = ClassicalRegister(4)
q = QuantumRegister(5)
ckt = QuantumCircuit(q, c)
```

Explanation:

* A quantum register with 5 qubits is created
* A classical register with 4 bits is created
* These are combined into a new circuit

---

### Initialize the Circuit

```python
ckt.x(4)
for i in range(5):
    ckt.h(i)
```

Explanation:

* The ancilla qubit is flipped to state 1
* Hadamard gates are applied to all qubits
* This creates superposition and prepares the ancilla for phase kickback

---

### Apply the Oracle

```python
ckt.append(oracle, [0, 1, 2, 3, 4])
```

Explanation:

* The oracle is applied to all qubits
* The function is encoded into the phase of the quantum state

---

### Apply Final Hadamard and Measure

```python
for i in range(4):
    ckt.h(i)
    ckt.measure(i, c[i])
```

Explanation:

* Hadamard gates are applied again to the input qubits
* This causes interference that reveals whether the function is constant or balanced
* The qubits are measured into classical bits

---

### Display the Circuit

```python
display(ckt.draw(output="mpl"))
```

---

## Important Note on Result Extraction

The following part of the code is not correct:

```python
counter = 0
for i in range(4):
    if c[i] == 0:
        counter += 1
```

Explanation:

* `c[i]` is a classical register object, not the measured value
* Measurement results are only available after running the circuit on a simulator or backend

---

## Correct Way to Get Results

You should run the circuit and get measurement results as follows:

```python
from qiskit_aer import AerSimulator
from qiskit import transpile

sim = AerSimulator()
compiled = transpile(ckt, sim)
result = sim.run(compiled, shots=1).result()

counts = result.get_counts()
print(counts)
```

---

## Interpreting the Output

* If the output is `0000`, the function is constant
* If the output is anything else, the function is balanced

Example:

```python
{'0000': 1}  -> constant
{'1010': 1}  -> balanced
```

For the parity function, the result will always indicate **balanced**.

---

## Conceptual Insight

* The algorithm evaluates the function for all inputs simultaneously
* Interference reveals global properties of the function
* Only one execution is required to classify the function

---

## Summary

* A parity oracle is constructed using CNOT gates
* The Deutsch–Jozsa circuit is implemented
* Measurement results determine whether the function is constant or balanced
* The parity function is correctly identified as balanced

---


# Deutsch–Jozsa Algorithm with Custom Oracle in Qiskit

## Overview

This code implements the Deutsch–Jozsa algorithm using a custom oracle. The oracle represents a Boolean function, and the algorithm determines whether the function is constant or balanced using a single quantum evaluation.

---

## Key Idea

* Four input qubits: q0, q1, q2, q3
* One ancilla qubit: q4
* Four classical bits for measurement
* The oracle applies selected CNOT operations to define a Boolean function
* The algorithm uses superposition and interference to classify the function

---

## Code Breakdown

### Import Required Libraries

```python
from qiskit import QuantumCircuit, QuantumRegister, ClassicalRegister
from qiskit_aer import AerSimulator
```

---

### Construct the Oracle

```python
qc = QuantumCircuit(5)
qc.cx(0, 4)
qc.cx(1, 4)
qc.cx(3, 4)
oracle = qc.to_instruction(label="Uf")
```

Explanation:

* A 5-qubit circuit is created
* CNOT gates are applied from qubits 0, 1, and 3 to the target qubit 4
* This defines a Boolean function depending only on selected inputs
* The circuit is converted into an instruction named "Uf"

---

### Create Registers and Circuit

```python
c = ClassicalRegister(4)
q = QuantumRegister(5)
ckt = QuantumCircuit(q, c)
```

Explanation:

* A quantum register with 5 qubits is created
* A classical register with 4 bits is created
* These are combined into a single circuit

---

### Initialize the Circuit

```python
ckt.x(4)
ckt.h(4)
for i in range(4):
    ckt.h(i)
```

Explanation:

* The ancilla qubit is flipped and then put into superposition
* The input qubits are also placed into superposition
* This prepares the system for quantum parallelism and phase kickback

---

### Apply the Oracle

```python
ckt.append(oracle, [0, 1, 2, 3, 4])
```

Explanation:

* The oracle is applied to all qubits
* The function is encoded into the phase of the quantum state

---

### Apply Final Hadamard and Measure

```python
for i in range(4):
    ckt.h(i)

for i in range(4):
    ckt.measure(i, c[i])
```

Explanation:

* Hadamard gates are applied again to the input qubits
* This creates interference that reveals whether the function is constant or balanced
* The qubits are measured into classical bits

---

### Run the Simulation

```python
ckt = ckt.decompose()
sim = AerSimulator()
result = sim.run(ckt, shots=1024).result()
counts = result.get_counts()

print(counts)
```

Explanation:

* The circuit is decomposed into basic gates for compatibility with the simulator
* The Aer simulator executes the circuit 1024 times
* Measurement results are collected as counts

---

## Interpreting the Output

* If the output is always `0000`, the function is constant
* If the output is any non-zero bitstring, the function is balanced

Example:

```
{'0000': 1024}  -> constant
{'1010': 1024}  -> balanced
```

---

## Result for This Oracle

* The oracle depends on three input qubits (0, 1, and 3)
* The function changes for different inputs
* Therefore, it is a balanced function

---

## Summary

* A custom oracle is constructed using selected CNOT gates
* The Deutsch–Jozsa algorithm is implemented
* The circuit uses superposition and interference
* Measurement results classify the function
* The given oracle is identified as balanced

---
