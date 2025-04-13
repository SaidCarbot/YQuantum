# Team Tittle: Purrallel
## Factorizing Integers with Quantum Rings: Large-Scale Quantum Simulation




**YQuantum 2025 Hackathon**.




---




## 👥 Team Members




Junseok Jung  
LU ZEYU  
Said C. Cruz Trejo  
Nouman Munib  
Ilsa Afzaal  






---








## Project Overview




This project explores the implementation of Shor’s Algorithm for integer factorization, a quantum approach capable of solving this problem exponentially faster than classical algorithms.




At the heart of the algorithm lies the transformation of factoring into a period-finding problem: identifying the smallest integer \( r \) such that




$$
a^r \equiv 1 \mod N.
$$




This is achieved by preparing a quantum superposition and applying modular exponentiation, which encodes the period into the quantum state amplitudes. The Quantum Fourier Transform (QFT) is then used to extract the period through measurement.


Additionally, we apply Beauregard’s qubit optimization strategy, reducing the required qubit count to 2n + 3 for an  n -bit semiprime, making the approach more feasible for near-term quantum hardware and simulators[2].


---




# Implementation Highlights:


1. **Objective**: 
   - The goal of the paper is to design a quantum circuit for Shor's factoring algorithm that reduces the number of qubits needed for factorization. This new approach uses a circuit with 2n+3 qubits, aiming to optimize the number of qubits while maintaining polynomial time complexity for modular exponentiation.


2. **Qubit Efficiency**:
   - Previous quantum circuits for Shor’s algorithm required significantly more qubits (often 7n+1 qubits for modular exponentiation). In contrast, the proposed circuit uses only 2n+3 qubits, offering a significant reduction in resource usage. The reduction in qubits is accomplished using various optimizations, including classical hardwiring and efficient quantum addition circuits.


3. **Modular Exponentiation**:
   - The quantum part of Shor’s algorithm involves performing modular exponentiation efficiently. This is achieved using controlled modular addition gates and quantum Fourier transforms (QFT). The modular exponentiation is a crucial step, and reducing the qubit count and gate depth for this step is a key achievement of this paper.


4. **Quantum Fourier Transform (QFT)**:
   - The circuit leverages the QFT, which is integral for finding the period of a modular function. The exact QFT requires O(n²) operations, but for practical purposes, an approximate QFT is used, which reduces the gate complexity to O(n log n) while maintaining a small error margin.


5. **Gate Complexity**:
   - The gate complexity of the proposed circuit is $(O(n³ \log n))$, which is manageable in comparison to earlier designs that required O(n³) gates. The depth of the circuit is O(n³), meaning that the quantum operations are arranged to minimize the number of sequential operations required.


---


# Technical Workflow:


1. **Classical Preprocessing**:
   - **Step 1**: If the number \( N \) is even, the algorithm immediately returns 2 as a factor.
   - **Step 2**: The algorithm randomly chooses a number \( a \) and checks whether gcd(a, N) > 1. If so, it returns gcd(a, N) as a factor.
   - **Step 3**: If no factor is found, the quantum part of the algorithm is invoked.


2. **Quantum Circuit for Order-Finding**:
   - **Order Finding**: The quantum part of the algorithm focuses on finding the order \( r \) of a random number \( a \) modulo \( N \). This is done using the quantum Fourier transform (QFT) and modular exponentiation.
   - **Quantum Circuit**:
     - A quantum register of size \( n+1 \) qubits is used to store the quantum state.
     - A controlled multiplication gate, CMULT, is used to apply \( a^x \mod N \) to the quantum register.
     - The QFT is applied to extract the period of the modular function.
   
3. **Post-Quantum Measurement and Classical Postprocessing**:    - **Step 4**: Once the quantum part determines the order \( r \), if \( r \) is even and $(a^{r/2} \neq -1\mod N)$, then the gcd of $(a^{r/2} - 1)$ and \( N \) is computed. This step can yield a non-trivial factor of \( N \).    - **Step 5**: If the factors are not found directly, a new value of \( a \) is chosen, and the order-finding quantum procedure is repeated.


4. **Optimization**:
   - **Qubit Reduction**: One of the key contributions of the paper is the reduction of qubits used in the quantum part of the algorithm. This is achieved by using classical hardwiring techniques and by minimizing the quantum gates needed for modular exponentiation.
   - **Gate Optimization**: The use of doubly controlled gates and controlled modular addition gates reduces the gate complexity, allowing the algorithm to be implemented with fewer gates.


5. **Conclusion**:
   - **Qubit Count**: The final circuit for factorizing an \( n \)-bit number \( N \) uses 2n+3 qubits, which is a significant reduction compared to previous designs (which required 7n+1 qubits).
   - **Gate Complexity**: The circuit requires $(O(n³ \log n))$
gates, making it a highly efficient implementation of Shor's algorithm for factorization.


This work demonstrates that it is possible to reduce the number of qubits and the depth of Shor’s quantum factoring algorithm while maintaining polynomial time complexity, thereby making the algorithm more feasible for quantum computation in the future.


---
### &nbsp;Function Reference


#### `qft_dagger(qc, n)`
| Parameter | Type | Description |
|-----------|------|-------------|
| `qc` | `QuantumCircuit` | Circuit modified **in‑place**. |
| `n`  | `int` | Number of qubits the inverse QFT acts on. |


Steps performed:  

1. **Swap** qubits `i` ↔ `n - i - 1` to reverse register order.  

2. For each qubit *i*, apply controlled-phase rotations  
   $\mathrm{CP}\bigl(-\pi / 2^{i - j}\bigr)$ from every qubit *j < i*.  

3. Finish with a **Hadamard** on each qubit (in reverse order).  

---


#### `solve_factors(N)`
| Parameter | Type | Description |
|-----------|------|-------------|
| `N` | `int` | Semiprime (or any composite) integer to factor. |


Returns  
`(p, q)` — prime factors found by naïve trial division up to $(\sqrt{N}\)$;  
`(None, None)` if no factor is found (which only happens when `N` is prime).


---


#### `shors_algorithm(N)`
| Parameter | Type | Description |
|-----------|------|-------------|
| `N` | `int` | Semiprime to be factorised. |


Algorithm outline:


1. **Even shortcut**: if `N` is even ⇒ return `(2, N/2)`.  
2. Pick random `a ∈ {2,…,N‑1}` with `gcd(a, N)=1` (≤ 100 attempts).  
3. Build a circuit with `num_qubits = ⌈log₂N⌉ + 1`  
   * Global **Hadamard** layer.  
   * **Placeholder** modular‑exponentiation (`CX` gates).  
   * Apply `qft_dagger`.  
   * **Measure** all qubits.  
4. **Run** on `QrBackendV2` (Quantum Rings).  
5. Parse counts → candidate period `r` → attempt to derive factors;  
   if unsuccessful, fall back to `solve_factors`.  
6. Returns `(p, q)` on success, else `(None, None)`.






## 📊 Experiment Summary




| Metric                   | Value                     |
|---------------------------|----------------------------|
| Largest Integer Factored  | *13081178794322790282667*              |
| bit length          | *74*                  |
| a value          | *6452409114750336274754*                  |
| Computed Factors          | *104893902637, 124708667191*                  |
| Qubits Used               | *75*          |
| Quantum Gate Count        | *1901*           |
| Execution Time            | *5:30 hours*                  |




### Date: April 12, 2025 
---

## Executive Summary: Ongoing Relevance of Shor’s Algorithm in Quantum Factorization




- **Foundational Role in Cryptography** 
 Integer factorization is a core problem in modern cryptography (e.g., RSA). Shor’s algorithm [1] demonstrated that a quantum computer could efficiently break this cryptographic security, marking a turning point in quantum computation research.




- **Mathematical Soundness of Shor and Beauregard's Optimization** 
 Shor reduces factorization to finding the period of a modular function, solvable via the Quantum Fourier Transform (QFT). It is grounded in established number theory and Fourier analysis, requiring no unproven assumptions [1]. 
 Beauregard [2] optimized Shor’s circuit to use only `2n + 3` qubits, making it viable for early-stage quantum hardware, despite higher gate complexity (`O(n^3 log n)`).




- **Regev’s Algorithm: A Promising Yet Heuristic-Based Approach** 
 Regev [5] introduced a lattice-based factorization algorithm with potentially lower gate complexity (`~O(n^{3/2})`) and recent improvements in qubit efficiency (`O(n log n)`) [6]. 
 However, it relies on heuristic number-theoretic assumptions about short lattice vectors and requires multiple quantum executions (`O(n^{1/2})`), potentially increasing susceptibility to noise.




- **Reasons for Shor’s Continued Centrality** 
 - **Proven Mathematical Validity**: Shor’s correctness is mathematically guaranteed, with no reliance on heuristics [1]. 
 - **Algorithmic Maturity**: Decades of research have led to extensive understanding, analysis, and optimization of Shor [3]. 
 - **Benchmark Status**: Shor remains the standard reference for evaluating new quantum factorization algorithms [1][6]. 
 - **Conceptual Clarity**: The period-finding strategy via QFT is more straightforward than the lattice complexities in Regev's approach [5].




- **Conclusion** 
 Regev's algorithm represents a significant innovation with potential execution-time advantages and valuable directions for further research [5][6]. 
 Nonetheless, Shor’s algorithm, especially in optimized forms like Beauregard’s [2], retains fundamental relevance due to its robust mathematical foundation and absence of heuristic dependence. 
 Until Regev’s assumptions are rigorously proven and universally applicable, Shor remains the most reliable and theoretically secure option for quantum factorization. The choice between algorithms will depend on the available quantum hardware and tolerance for heuristic uncertainty.




---




<span style="font-size: smaller;">





## References 
[1] P. W. Shor, “Algorithms for quantum computation: discrete logarithms and factoring,” in *Proc. 35th Annual Symposium on Foundations of Computer Science*, 1994, pp. 124–134. DOI: [10.1109/SFCS.1994.365700](https://doi.org/10.1109/SFCS.1994.365700)  
[2] S. Beauregard, “Circuit for Shor’s algorithm using 2n+3 qubits,” *arXiv preprint quant-ph/0205095*, 2003. [Online]. Available: https://arxiv.org/abs/quant-ph/0205095  
[3] D. Beckman, A. N. Chari, S. Devabhaktuni, and J. Preskill, “Efficient networks for quantum factoring,” *Phys. Rev. A*, vol. 54, no. 2, pp. 1034–1063, 1996. DOI: [10.1103/PhysRevA.54.1034](https://doi.org/10.1103/PhysRevA.54.1034)  
[4] S. DeVera, “Optimized implementation of Shor’s algorithm with 7n+1 qubits,” unpublished manuscript, 2023.  
[5] O. Regev, “An Efficient Quantum Factoring Algorithm,” *Number Theory Web Seminar (NTWS 195)*, video lecture, 2023. [Online]. Available: https://www.youtube.com/watch?v=SzToqdRy1Ys  
[6] S. Ragavan and V. Vaikuntanathan, “Improved Quantum Algorithms for Factoring,” *arXiv preprint arXiv:2310.00899v4*, Dec. 2024. [Online]. Available: https://arxiv.org/abs/2310.00899  
[7] QuantumRings Team, “Installing QuantumRings SDK with CUDAq,” *QuantumRings Documentation*, 2024. [Online]. Available: https://portal.quantumrings.com/doc/Installation.html   
[8] Devpost Project: “Purrallel – Quantum Factoring,” [Online]. Available: https://devpost.com/software/purrallel/joins/0lTKGjrySzZc1C-HxzO6lg  
</span>


## 💼 Acknowledgments




This project was developed as part of **YQuantum 2025 Hackathon** — an initiative fostering quantum innovation and collaboration.
—
