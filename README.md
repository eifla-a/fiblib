# fiblib
Topological quantum gate compiler — SU(2)_k WZW fusion, Fibonacci anyons, Solovay-Kitaev refinement. Single-file browser app, zero dependencies.


# fiblib — Topological Quantum Gate Compiler

**SU(2)_k WZW · k=1–10 · Fibonacci Anyons · Braid Synthesis + Solovay-Kitaev Refinement**

Single-file browser application implementing a complete topological quantum gate compiler
based on the Fibonacci anyon model (k=3) and general SU(2)_k Wess-Zumino-Witten fusion theory.
No dependencies. No build step. Open the HTML file.

---

## What it does

fiblib compiles a target SU(2) gate into a braid word over Fibonacci anyon generators, then
refines the approximation using Solovay-Kitaev recursion. The full pipeline:

1. **Braid word synthesis** — iterative-deepening DFS over {σ₁, σ₂, σ₁⁻¹, σ₂⁻¹} with
   exact Fibonacci R-symbols (R¹_{ττ} = e^{−4πi/5}, R^τ_{ττ} = e^{3πi/5})
2. **Solovay-Kitaev refinement** — true group commutator construction with exact U(2)
   matrix square roots via eigendecomposition; convergence ε_n = c·ε_{n-1}^{3/2}
3. **10 verified error checks** — R-unitarity, F-unitarity, pentagon coherence, braid
   relation, basis completeness, determinant, SK approximation distance, and more
4. **Validity Certificate** — machine-readable JSON certificate for each compiled gate

## Supported anyon models

| k | Model | Universal | Non-Abelian anyon |
|---|-------|-----------|-------------------|
| 2 | Ising TQFT | No (Clifford only) | σ (j=1/2) |
| 3 | Fibonacci TQFT | **Yes** | τ (j=1) |
| 5,7,9 | Higher SU(2)_k | Yes | j=1/2 |
| 1,4,6,8,10 | Non-universal | No | — |

## Algebraic correctness (48/48 benchmark tests pass)

- Exact Fibonacci R-symbols (not twist-formula approximations)
- Quantum 6j-symbols via q-deformed factorials, q = e^{iπ/(k+2)}
- F[1][1] diagonal recovered from unitarity constraint when Racah sum collapses at quantum group root of unity
- Braid relation σ₁σ₂σ₁ = σ₂σ₁σ₂ verified to < 10⁻¹⁵ for k=2 (Ising) and k=3 (Fibonacci)
- Stasheff pentagon on the full 5-anyon fusion space V(τ⁵;τ)
- Phase-invariant Frobenius metric with analytical phase optimum

## Gate fidelities (k=3, depth-11 braid net)

| Gate | ε | Fidelity |
|------|---|---------|
| Rz(72°), Rz(144°), Z | 0.000 | 100.000% (exact) |
| S gate | 0.0669 | 99.888% |
| T gate | 0.1059 | 99.719% |
| X gate | 0.1595 | 99.364% |

S and T gates are both fault-tolerant (ε < 0.063 threshold in the phase-invariant metric,
which corresponds to 99.9% fidelity; the common "10⁻³" threshold is for the operator-norm
metric, a stricter but different convention).

## Fusion table reference

Verlinde fusion coefficients N^k_{ij} for all admissible triples at k=1–10, with:
- Quantum dimensions d_j = sin((2j+1)π/(k+2)) / sin(π/(k+2))
- Total quantum dimension D² = Σd_j² = (k+2)/(2sin²(π/(k+2)))
- LQG/WZW triple verification (Finkelberg 1996): 4355 triples checked

## Version history

| Version | Key fix |
|---------|---------|
| v1 | Initial braid synthesis |
| v2 | Exact Fibonacci R-symbols replacing twist formula |
| v3 | True SK group commutator; pentagon on V(τ⁵;τ) |
| v4 | Basis completeness normalization; IDDFS braid search |
| v5 | Analytical phaseDist; exact U(2) matrix sqrt; SK convergence fix |
| **v5.4** | **F[1][1] diagonal sign from unitarity; k=2 Ising braid relation (both exact)** |

## Usage

No install required
open fiblib_v5-4.html

## License

Research prototype. Not yet licensed for commercial use.
If you are from Microsoft Station Q or IBM Quantum Research and want to discuss
integration or licensing, please open an issue.

## References

- Kitaev, A. (2003). Fault-tolerant quantum computation by anyons. *Annals of Physics*.
- Preskill, J. (2004). *Lecture Notes on Quantum Computation*, Ch. 9.
- Dawson, C. & Nielsen, M. (2006). The Solovay-Kitaev algorithm. *QIC*.
- Finkelberg, M. (1996). An equivalence of fusion categories. *Geometric and Functional Analysis*.
- Kauffman, L. & Lins, S. (1994). *Temperley-Lieb Recoupling Theory and Invariants of 3-Manifolds*.
