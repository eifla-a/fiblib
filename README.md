# fiblib v5.4

**Topological quantum gate compiler for SU(2)_k WZW anyonic models.**

Runs entirely in the browser — one HTML file, zero dependencies. Open `fiblib_v5-4.html` and compile.

Intended as a reference implementation and verification layer for Fibonacci anyon gate synthesis — the compilation step that no standard quantum compiler (pytket, quilc) currently supports.

---
![fiblib compiler screenshot](Fiblib-screenshot.jpg)

## What it does

fiblib synthesises quantum gates from braid sequences of non-abelian anyons using the Solovay-Kitaev algorithm, with exact algebraic data for SU(2)_k Wess-Zumino-Witten models at levels k = 1–10.

The full pipeline:

1. **Braid synthesis** — iterative-deepening DFS (IDDFS) over the generator set {σ₁, σ₂, σ₁⁻¹, σ₂⁻¹}, guaranteed shortest braid word achieving target ε
2. **SK refinement** — true iterative Solovay-Kitaev: εₙ = c · εₙ₋₁^(3/2) via group commutator construction, gate count O(log^3.97(1/ε))
3. **10 error checks** — pentagon coherence, braid relation, unitarity, basis completeness, determinant, SK approximation distance, and more
4. **57-test benchmark suite** — algebraic correctness, metric properties, matrix square roots, SK convergence, cross-level consistency, numerical precision

---

## Physics

### Models supported

| k | Anyons | Universal | d_{1/2} |
|---|--------|-----------|---------|
| 1 | SU(2)₁ | No | 1 |
| 2 | Ising (σ, ψ) | No | √2 |
| 3 | Fibonacci (τ) | **Yes** | φ = 1.6180… |
| 4 | — | No | √3 |
| 5 | — | **Yes** | 2cos(π/7) |
| 6 | — | No | 2cos(π/8) |
| 7 | — | **Yes** | 2cos(π/9) |
| 8 | — | No | 2cos(π/10) |
| 9 | — | **Yes** | 2cos(π/11) |
| 10 | — | No | 2cos(π/12) |

Universal levels (odd k ≥ 3) support dense approximation of any SU(2) gate by braiding alone.

### Exact data (k = 3, Fibonacci)

```
R¹_{ττ}  = e^{−4πi/5}   (fusion to vacuum)
R^τ_{ττ} = e^{+3πi/5}   (fusion to τ)

F^τ_{ττ} = [[φ⁻¹,  φ⁻¹/²],
             [φ⁻¹/², −φ⁻¹]]    φ = (1+√5)/2

σ₁ = diag(R¹_{ττ}, R^τ_{ττ})
σ₂ = F · σ₁ · F              (F self-inverse)

Braid relation: σ₁σ₂σ₁ = σ₂σ₁σ₂,  error < 2.4×10⁻¹⁶
```

### F-matrix (general k)

For k ≠ 3, F-matrix entries are computed via exact quantum 6j-symbols:

```
F^{j₁j₂}[m][n] = (−1)^{2(j₁+j₂+cₘ+cₙ)} · √(dₘ dₙ) · {j₁ j₂ cₙ; j₁ j₂ cₘ}_q

[n]_q = sin(nπ/(k+2)) / sin(π/(k+2))     q-deformed integer
```

The k = 3 diagonal entry F[1,1] = −φ⁻¹ is hardcoded because the q6j formula returns the wrong sign for this entry (phase convention mismatch on the diagonal). The benchmark explicitly guards against this regression.

### Ising (k = 2) braid generators

The FIB_DATA/twist-formula phases for k = 2 (−135°, +45°) are the eigenvalues of **σ₁²** — the squared braid generator — not σ₁ itself. The actual matrix eigenvalues are their principal square roots:

```
R^1_{σσ}  = e^{−3iπ/8}   (−67.5°)    σ₁²: e^{−3iπ/4} = −135°  ✓
R^ψ_{σσ} = e^{+iπ/8}    (+22.5°)    σ₁²: e^{+iπ/4}  = +45°   ✓
```

The ratio β/α = e^{iπ/2} = i is the condition for braid closure with a Hadamard F-matrix. The fusion table R-phase display values (−135°, +45°) are unchanged — they are the correct topological spin ratios θ_c/(θ_a θ_b).

---

## Benchmark

| Group | Tests | What is checked |
|-------|-------|-----------------|
| 1 — Algebraic Correctness | 13 | R-symbols, F-matrix, pentagon, braid relation (k=3 exact) |
| 1b — Ising Braid Relation | 7 | k=2 braid relation, F-matrix, σ₁² ↔ twist consistency |
| 2 — phaseDist Metric | 5 | Phase-invariant distance: identity, symmetry, triangle inequality |
| 3 — Matrix Square Root | 10 | U(2) eigendecomposition sqrt for σ₁, σ₂, products, edge cases |
| 4 — SK Convergence | 6 | Rz(144°), Rz(72°), Z exact; S 99.9%, T 99.7%, X 99.4% |
| 5 — Verlinde / WZW | 11 | D² formula, golden ratio, palindrome symmetry, fusion positivity |
| 6 — Numerical Precision | 5 | Complex arithmetic, identity multiplication, machine-epsilon bounds |

Gate fidelities use the phase-invariant metric: fidelity = 1 − ε²/4.

---

## Seven theorems (proved k = 1–10)

| # | Name | Statement |
|---|------|-----------|
| T1 | Primary Quantum Dimension | d_{1/2}(k) = 2cos(π/(k+2)) |
| T2 | Palindrome Symmetry | d_j = d_{k/2−j} |
| T3 | Tetrahedral Admissible Count | `|admissible|` = C(k+3, 3) |
| T4 | Total Quantum Dimension | D² = (k+2) / (2sin²(π/(k+2))) |
| T5 | Braiding Phase Quantisation | R ∈ exp(iπ·ℤ/(k+2)) |
| T6 | WZW ↔ LQG Equivalence | N^k_{ij} > 0 ⟺ (j₁,j₂,j₃) LQG-admissible |
| T7 | Lossless Gate Guarantee | `|R|` = 1 for all admissible triples |

---

## Known limitations

- **F[1,1] hardcode (k=3):** The q6j path returns the wrong sign for the F[1,1] diagonal entry due to a phase convention mismatch. The value −φ⁻¹ is hardcoded directly. The benchmark includes a regression guard that will catch any future removal of this hardcode.
- **SK constant c = 0.97 is empirical.** The true Dawson-Nielsen bound is gate-set dependent; convergence is not guaranteed for all targets at k ≠ 3. The constant was chosen to avoid divergence (c = 4 diverges for ε₀ > 1/16).
- **Single-qubit only.** Two-qubit gates (CNOT, Toffoli) are not implemented. The compiler handles single-qubit SU(2) synthesis only. Two-qubit encoding requires a 6-anyon fusion space and is future work.
- **No hardware interface.** fiblib outputs braid words and matrices. Translating these to physical control pulses for Majorana or photonic anyon hardware is outside the scope of this implementation.

---

## Usage

Open the HTML file in any modern browser. No build step, no server, no install.

**Compiler tab** — select gate, anyon level, precision ε, and hit Compile. Shows fusion tree, F/R matrices, composed unitary, braid word, SK refinement table, and 10 error checks.

**Level Overview** — quantum dimensions, admissible triple counts, universality for k = 1–10.

**Fusion Tables** — all admissible triples with R-phases and lossless certification.

**Validity Certificate** — check any spin sequence for pairwise Verlinde channel consistency.

**7 Theorems** — proofs and k = 1–10 verification matrix.

**SK Convergence** — interactive εₙ table for any k and starting error.

**Benchmark Suite** — runs all 57 tests in-browser, ~4–6 seconds on first run (epsilon-net is cached for the session).

---

## References

- Kitaev, A. (2006). Anyons in an exactly solved model and beyond. *Annals of Physics*, 321(1), 2–111.
- Preskill, J. (2004). Lecture Notes on Quantum Computation, Chapter 9: Topological Quantum Computation.
- Dawson, C. M., & Nielsen, M. A. (2006). The Solovay-Kitaev algorithm. *Quantum Information & Computation*, 6(1), 81–95.
- Kauffman, L. H., & Lins, S. L. (1994). *Temperley-Lieb Recoupling Theory and Invariants of 3-Manifolds*. Princeton University Press.
- Finkelberg, M. (1996). An equivalence of fusion categories. *Geometric and Functional Analysis*, 6(2), 249–267.
- Bonderson, P. (2007). *Non-Abelian Anyons and Interferometry* (PhD thesis). Caltech.

---

## Citation

If you use fiblib in research, please cite this repository:

```
@software{fiblib,
  title  = {fiblib v5.4: Topological Quantum Gate Compiler},
  author = {Alfred Kimani},
  year   = {2026},
  url    = {https://github.com/eifla-a/fiblib}
}
```

---

## License

MIT

Questions and discussion welcome — open an issue.
