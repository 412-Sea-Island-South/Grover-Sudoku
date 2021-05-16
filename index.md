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
This example is to demonstrate the conversion of classical [decision problems](https://en.wikipedia.org/wiki/Decision_problem) into Grover oracles.
<p align="center">
  <img src="https://qiskit.org/textbook/ch-algorithms/images/binary_sudoku.png">
</p>
  
## Creating the Circuit
