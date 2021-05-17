🦈Solving Sudoku Using Grover's Algorithm!🦈
=============================================
## 2 by 2 Sudoku
This is the first/second (depending on if you read [this](https://tksmax.github.io/Grover-3SAT)) example of Grover's Algorithm but the solutions are unknown.
We will solve this simple binary sudoku using Grover's Algorithm.
As mentioned, we have no idea what the solution is.
There are only two simple rules we must follow:
- No column can contain the same value
- No row can contain the same value
And those are the rules!
  
We can visualize our binary sudoku like the picture below.
Obviously, this isn't very pratical because you can probably find the solution instantly in your head.
This example is just to demonstrate the conversion of classical [decision problems](https://en.wikipedia.org/wiki/Decision_problem) into Grover oracles.
<p align="center">
  <img src="https://qiskit.org/textbook/ch-algorithms/images/binary_sudoku.png">
</p>
  
## Creating the Circuit
We will create an oracle that will help us solve this problem.
For more information about oracles, you may want to visit **[this page](https://medium.com/nerd-for-tech/grovers-algorithm-3ac4616ce23a)**.
We just need to create a classical function to help us check if our arrangment \[of variable bits\] is valid.
In particular, there are four conditions we need to check:
1. v0 ≠ v1 (top row)
2. v2 ≠ v3 (bottom row)
3. v0 ≠ v2 (left column)
4. v1 ≠ v3 (right column)
We can write this as a **list of clauses**:  
```python
clause_list=list()
clause_list.append([0,1])
clause_list.append([2,3])
clause_list.append([0,2])
clause_list.append([1,3])
print(clause_list)
```
Now to check if the constraints are satisfied, we will use the XOR gate.
Recall from [here](https://tksmax.github.io/Quantum-Operations) that the XOR gate outputs a 1 (i.e true) if and only if the two inputs two it are different.
Here's a little table to show the XOR gate workings:
<table align="center">
  <tr>
    <th>Input Qubit 1</th>
    <th>Input Qubit 2</th>
    <th>Output</th>
  </tr>
  <tr>
    <th>0</th>
    <th>0</th>
    <th>0</th>
  </tr>
  <tr>
    <th>0</th>
    <th>1</th>
    <th>1</th>
  </tr>
  <tr>
    <th>1</th>
    <th>0</th>
    <th>1</th>
  </tr>
  <tr>
    <th>1</th>
    <th>1</th>
    <th>0</th>
  </tr>
</table>
  
This code will help us check our contrainst satisfaction using the XOR gate.
Notice that the XOR gate is made up of CNOT gates.
  
```python
def XOR(circuit, a, b, out):
  circuit.cx(a, out)
  circuit.cx(b, out)
```
  
### Instantiation
We will use separate registers to name the bits for clarity.  
```python
import matplotlib,pyplot as plt
import numpy as np
from qiskit import *
from qiskit.providers.ibmq import *
from qiksit.visualization import *

in = QuantumRegister(2, name='in')
out = QuantumRegister(1, name='out')
circuit = QuantumCircuit(in, out)
XOR(circuit, in[0], in[1], out)
circuit.draw()
```
The first picture shows how we'll use the XOR gate: We'll apply the XOR to each clause in the **list of clauses**.
<h4 align="center">XOR Circuit</h4>
<p align="center">
  <img src="https://user-images.githubusercontent.com/81530826/118532528-e9c54c00-b6fb-11eb-9199-313e9bb8ae3f.png">
</p>
  
This code will allow us to make all the XOR gates and whatnot to make sure our solution is actually a solution.
```python
# Create separate registers to name bits
variable = QuantumRegister(4, name='variable')  
clause = QuantumRegister(4, name='clause')  

# Create quantum circuit
qc = QuantumCircuit(variable, clause)

# Use XOR gate to check each clause
i = 0
for clause in clause_list:
    XOR(qc, clause[0], clause[1], clause_qubits[i])
    i += 1

qc.draw()
```
  
After all those XOR gates, we get something like this:  
If it looks very convoluted to you right now, just wait until later.
You haven't seen the *whole* circuit.
<h4 align="center">A Bunch of XORs</h4>
<p align="center">
  <img src="https://user-images.githubusercontent.com/81530826/118533456-f302e880-b6fc-11eb-948f-5225aef7d56e.png">
</p>
  
The final state of the clause qubit will only be a **1** if the corresponding values of **v0, v1, v2, v3** are solutions to the sudoku.
We want a single bit to be **1** if and only if all the constraints are satisfied, this way we can be lazy and just check one bit to validate the solution.
To do this, we use a **multi-controlled Toffoli gate**.
```python
variables = QuantumRegister(4, name='variable')
clauses = QuantumRegister(4, name='clause')
out = QuantumRegister(1, name='out')
circuit = QuantumCircuit(variables, clauses, out)

counter = 0
for clause in clause_list:
  XOR(circuit, clause[0], clause[1], clauses[1])
  i+=1
 
 circuit.mct(clauses, out)
 qc.draw()
 ```
  
### Uncomputing, The Oracle, and The Solution
With everything out of the way, we can now yeet this circuit into oblivion, and turn it into a Grover oracle using [phase kickback](https://qiksit.org/textbook/ch-gates/phase-kickback.html).
As a quick summary:
1. The first register stores our sudoku variables (in reverse order, so **v3, v2, v1, v0***)
2. The second register stores our clauses, starting in the state |0000⟩
3. The one qubit/register to store the output
  
And now, for some very sophisticated math.  
We want out circuit to do the following transformation:
<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=\color{white}U_\omega|x\rangle|0\rangle=|x\rangle|0\rangle|out_0\bigoplus f(x)\rangle">
</p>
If we set the qubit out0 to the superposition state |-⟩, we have:
<p align="center">
  <img src="https://render.githubusercontent.com/render/math?math=\color{white}U_{\omega}|x\rangle|0\rangle|-\rangle = U_{\omega}|x\rangle|0\rangle\bigoplus\frac{1}{\sqrt{2}}(|0\rangle + |1\rangle) = |x\rangle|0\rangle\bigoplus\frac{1}{\sqrt{2}}(|0\bigoplus f(x)\rangle - |1\bigoplus f(x)\rangle)">
</p>
