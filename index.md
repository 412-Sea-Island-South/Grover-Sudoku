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
    <th>Output></th>
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
