+++
date = '2025-09-10T11:34:57+02:00'
title = 'DHM 2024 - Time'
keywords = ['ctf']
+++

In the first iteration of the [German Hacking Championship,](https://hacking-meisterschaft.de/) we were given a linear-feedback shift register (LFSR) over a Galois Field with two elements.
In this writeup, I want to explain what a LFSR does and how we can solve this challenge using linear algebra.
<!--more-->

# Background
Initially, Linear Feedback Shift Registers (LFSRs) were used to implement finite state machines in hardware and relied on flip-flops to store the internal state.
Using \(n\) flip-flops, one could implement a machine with \(2^{n}\) possible states.

These flip-flops are numbered \(F_{0}, \ldots, F_{n-1}\), and at every clock cycle, each \(F_{i}\) (for \(i > 0\)) takes the value of \(F_{i - 1}\).
The flip-flop \(F_{0}\) is updated according to a feedback function that takes the internal state as input.
The value of \(F_{n - 1}\) serves as the output of the shift register.
LFSRs are commonly used for pseudo-random number generation and in stream ciphers.
In our case, we have LFSR over a \(GF(2)\), which consists of the elements 0 and 1.

# Challenge
```python
def step_lfsr(lfsr: int, n: int) -> int:
    mask = (1 << 257) - 1
    for i in range(n):
        feedback = lfsr >> 256
        lfsr = (lfsr << 1) & mask
        if feedback:
            lfsr ^= 0x1001
    return lfsr


if __name__ == "__main__":
    locked = (
        203745769409068536978743361691286385722962785255197588475888648333603407239233
    )
    unlocked = step_lfsr(locked, 2**256) # this will never finish MUHAHAHA...
    print(unlocked.to_bytes(33, "little").strip(b"\x00").decode())
```

The function `step_lfsr` simulates stepping a 257-bit LFSR forward \(n\) times.
Each step extracts the most significant bit as a feedback bit,and shifts the register left by one bit, maintaining a fixed width of 257 bits.
If the feedback bit is 1, the LFSR state is XORed with `0x1001`, a feedback polynomial with bits set at positions 12 and 0.
Clearly, the naive approach of running the LFSR forward for \(2^{256}\) is infeasible, as the comment already suggests.
Using linear algebra, we can find a closed formula for the LFSR, and solve the challenge more efficiently.

We can represent the LFSR by an initial state vector and a transition matrix.
Each step is equivalent to multiplying the current state with the transition matrix.
Consequently, stepping the LFSR \(n\) times is equivalent to multiplying the initial state vector by the transformation matrix, but raised to the \(n\)-th power.

Although \(n\) is exponentially large, we can use exponentiation by squaring to compute the transformation matrix in logarithmic time instead.
Furthermore, addition and multiplication can be represented efficiently in binary arithmetic using XOR and AND.
For that, we first need to define both the initial state vector and the transition matrix.
We can represent our 257-bit LFSR using a column vector with 257 elements, where each element corresponds to a bit.

\[
\begin{pmatrix}
    a_{k}         \\
    \vdots        \\
    a_{k + n - 2} \\
    a_{k + n - 1} \\
\end{pmatrix} =
\begin{pmatrix}
    0      & 1      &        & 0        \\
    \vdots & \vdots & \ddots & \vdots   \\
    0      & 0      &        & 1        \\
    c_{0}  & c_{1}  & \ldots & c_{n - 1} \\
\end{pmatrix}^{k}
\cdot
\begin{pmatrix}
    a_{0}     \\
    \vdots    \\
    a_{n - 2} \\
    a_{n - 1} \\
\end{pmatrix}
\]

In our solve script, we define linear algebra functions that define the companion matrix and multiplication and exponentiation for vectors and matrices over the \(GF(2)\) using binary arithmetic.

```python
def companion_matrix(n: int, poly: int) -> list[list[int]]:
    matrix = [[0] * n for _ in range(257)]
    for i in range(n - 1):
        matrix[i][i + 1] = 1
    for i in range(n):
        if (poly >> i) & 1:
            matrix[n - 1][i] = 1
    return matrix


def matrix_mult(A: list[list[int]], B: list[list[int]], size: int) -> list[list[int]]:
    C = [[0] * size for _ in range(size)]
    for i in range(size):
        for j in range(size):
            for k in range(size):
                C[i][j] ^= A[i][k] & B[k][j]
    return C


def matrix_pow(matrix: list[list[int]], exp: int, size: int) -> list[list[int]]:
    result = [[1 if i == j else 0 for j in range(size)] for i in range(size)]
    base = matrix
    while exp > 0:
        if exp % 2 == 1:
            result = matrix_mult(result, base, size)
        base = matrix_mult(base, base, size)
        exp //= 2
    return result


def vector_mult(matrix: list[list[int]], vector: int, size: int) -> int:
    result = 0
    for i in range(size):
        if vector & (1 << i):
            for j in range(size):
                if matrix[i][j]:
                    result ^= 1 << j
    return result


def step_lfsr(lfsr: int, steps: int, poly: int) -> int:
    mask = (1 << 257) - 1
    for i in range(steps):
        feedback = lfsr >> 256
        lfsr = (lfsr << 1) & mask
        if feedback:
            lfsr ^= poly
    return lfsr


def solve(lfsr: int, steps: int, size: int, poly: int) -> int:
    transformation_matrix = matrix_pow(companion_matrix(size, poly), steps, size)
    return vector_mult(transformation_matrix, lfsr, size)

if __name__ == "__main__":
    initial_state = (
        203745769409068536978743361691286385722962785255197588475888648333603407239233
    )
    steps = 2**256
    size = 257
    poly = 0x1001
    final_state = solve(initial_state, steps, size, poly)
    flag = final_state.to_bytes(33, "little").strip(b"\x00").decode()
    print(flag)
```

# References
Klein, A. (2013). Linear Feedback Shift Registers. In: Stream Ciphers. Springer, London. https://doi.org/10.1007/978-1-4471-5079-4_2
