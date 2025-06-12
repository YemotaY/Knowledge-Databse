# Numerical Analysis

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **🔢 [Numerical Methods](../README.md)**

Numerical methods provide computational techniques for solving mathematical problems that cannot be solved analytically, forming the backbone of scientific computing and engineering simulations.

## Overview

Numerical methods applications in computer science:
- **Scientific Computing**: Simulation, modeling, data analysis
- **Machine Learning**: Optimization algorithms, matrix computations
- **Computer Graphics**: Ray tracing, physics simulations, 3D rendering
- **Financial Computing**: Risk analysis, option pricing, Monte Carlo methods
- **Signal Processing**: Digital filters, Fourier transforms
- **Game Development**: Physics engines, collision detection

## Root Finding Methods

### Bisection Method

**Problem**: Find $x$ such that $f(x) = 0$

**Algorithm**: Use Intermediate Value Theorem to narrow down the interval.

**Mathematical Foundation:**
If $f$ is continuous on $[a,b]$ and $f(a) \cdot f(b) < 0$, then $\exists c \in (a,b)$ such that $f(c) = 0$.

**Bisection Algorithm:**
```
Given: f(x), interval [a,b] where f(a)·f(b) < 0
Repeat:
    c = (a + b) / 2
    if f(c) = 0: return c
    if f(a)·f(c) < 0: b = c
    else: a = c
Until |b - a| < tolerance
```

**Visualization:**
```
f(x) = x³ - x - 1, find root in [1, 2]

f(x) ↑
   2 +
     |     ╱
   1 +    ╱
     |   ╱
   0 +──╱─────→ x
     | ╱   1  2
  -1 +╱

Iteration table:
n |   a   |   b   |   c   | f(c)  | New interval
--|-------|-------|-------|-------|-------------
1 | 1.000 | 2.000 | 1.500 | 0.875 | [1.000, 1.500]
2 | 1.000 | 1.500 | 1.250 | 0.203 | [1.000, 1.250]  
3 | 1.000 | 1.250 | 1.125 |-0.252 | [1.125, 1.250]
4 | 1.125 | 1.250 | 1.188 |-0.027 | [1.188, 1.250]
...
Root ≈ 1.3247...
```

### Newton-Raphson Method

**Formula:**
$$x_{n+1} = x_n - \frac{f(x_n)}{f'(x_n)}$$

**Geometric Interpretation:**
Use tangent line to approximate the root.

**Convergence:**
- **Quadratic convergence** when started near the root
- **Requires**: $f'(x) \neq 0$ and good initial guess

**Newton's Method Visualization:**
```
f(x) = x² - 2, find √2

     f(x) ↑
       2 +      •x₁
         |     ╱|
       1 +    ╱ |
         |   ╱  |
       0 +──╱───┼──→ x
         | ╱    |
      -1 +╱     |
         ╱x₂ ←──┘
        ╱
    
x₀ = 2.0  →  x₁ = 1.5    →  x₂ = 1.417  →  x₃ = 1.414...
f'(x) = 2x

x₁ = 2 - (4-2)/(4) = 2 - 0.5 = 1.5
x₂ = 1.5 - (2.25-2)/(3) = 1.5 - 0.083 = 1.417
x₃ = 1.417 - (2.007-2)/(2.833) = 1.414...
```

### Secant Method

**Formula:**
$$x_{n+1} = x_n - f(x_n) \frac{x_n - x_{n-1}}{f(x_n) - f(x_{n-1})}$$

**Advantage**: No need to compute derivative (unlike Newton's method)

**Convergence**: Superlinear convergence rate ≈ 1.618 (golden ratio)

## Numerical Integration

### Trapezoidal Rule

**Approximation:**
$$\int_a^b f(x) dx \approx \frac{h}{2}[f(a) + 2f(x_1) + 2f(x_2) + \cdots + 2f(x_{n-1}) + f(b)]$$

where $h = \frac{b-a}{n}$ and $x_i = a + ih$

**Visualization:**
```
Approximate ∫₀² x² dx using 4 trapezoids

f(x) = x² ↑
       4 +        ████
         |       ██ ██
       3 +      ██  ██
         |     ██   ██
       2 +    ██    ██
         |   ██     ██
       1 +  ██      ██
         | ██       ██
       0 +█████████████→ x
         0  0.5  1  1.5  2

h = 0.5
Trapezoids: [0,0.5], [0.5,1], [1,1.5], [1.5,2]
Area ≈ 0.5/2 × [0 + 2(0.25) + 2(1) + 2(2.25) + 4] = 2.75
Exact: ∫₀² x² dx = [x³/3]₀² = 8/3 ≈ 2.667
Error ≈ 0.083
```

### Simpson's Rule

**Formula (1/3 Rule):**
$$\int_a^b f(x) dx \approx \frac{h}{3}[f(a) + 4f(x_1) + 2f(x_2) + 4f(x_3) + \cdots + 4f(x_{n-1}) + f(b)]$$

**Higher accuracy**: $O(h^4)$ vs $O(h^2)$ for trapezoidal

**Parabolic approximation**: Fits parabolas through every three points

### Monte Carlo Integration

**Basic Idea**: Use random sampling to estimate integrals

**Formula:**
$$\int_a^b f(x) dx \approx \frac{b-a}{N} \sum_{i=1}^N f(x_i)$$

where $x_i$ are randomly sampled from $[a,b]$

**Visualization (Estimating π):**
```
Estimate π by computing area of quarter circle

  1 +─────────•
    |   ••••••█|
    |  •••••••█|
 0.5+ ••••••••█|  ← Quarter circle: x² + y² ≤ 1
    | •••••••█ |    Area = π/4
    |•••••••█  |
  0 +█████████─+→ x
    0   0.5    1

Monte Carlo:
- Generate N random points in [0,1] × [0,1]
- Count how many fall inside quarter circle
- π ≈ 4 × (points inside) / (total points)

Convergence rate: O(1/√N)
```

## Linear Systems

### Gaussian Elimination

**Goal**: Solve $A\mathbf{x} = \mathbf{b}$

**Method**: Transform to upper triangular form, then back-substitute

**Example:**
$$\begin{pmatrix} 2 & 1 & -1 \\ -3 & -1 & 2 \\ -2 & 1 & 2 \end{pmatrix} \begin{pmatrix} x \\ y \\ z \end{pmatrix} = \begin{pmatrix} 8 \\ -11 \\ -3 \end{pmatrix}$$

**Forward Elimination:**
```
Original:           After elimination:
[ 2  1 -1 |  8]    [ 2  1 -1 |  8]
[-3 -1  2 |-11] →  [ 0  1  1 |  1]
[-2  1  2 | -3]    [ 0  0  4 | 12]

Step 1: R₂ + (3/2)R₁ → R₂
Step 2: R₃ + R₁ → R₃  
Step 3: R₃ - 2R₂ → R₃
```

**Back Substitution:**
```
4z = 12     →  z = 3
y + z = 1   →  y = 1 - 3 = -2  
2x + y - z = 8  →  x = (8 + 2 + 3)/2 = 6.5

Solution: x = 6.5, y = -2, z = 3
```

### LU Decomposition

**Factorization**: $A = LU$ where $L$ is lower triangular, $U$ is upper triangular

**Advantage**: Solve multiple systems $A\mathbf{x} = \mathbf{b}$ efficiently

**Process:**
1. $L\mathbf{y} = \mathbf{b}$ (forward substitution)
2. $U\mathbf{x} = \mathbf{y}$ (backward substitution)

### Iterative Methods

#### Jacobi Method

**Formula:**
$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j \neq i} a_{ij} x_j^{(k)} \right)$$

**Matrix Form:**
$$\mathbf{x}^{(k+1)} = D^{-1}(L + U)\mathbf{x}^{(k)} + D^{-1}\mathbf{b}$$

where $A = D + L + U$ (diagonal + lower + upper)

#### Gauss-Seidel Method

**Improvement**: Use updated values immediately

$$x_i^{(k+1)} = \frac{1}{a_{ii}} \left( b_i - \sum_{j < i} a_{ij} x_j^{(k+1)} - \sum_{j > i} a_{ij} x_j^{(k)} \right)$$

**Convergence Visualization:**
```
2x + y = 3        x = (3 - y)/2
x + 2y = 3   →    y = (3 - x)/2

Starting point: (0, 0)
Jacobi iterations:
(0,0) → (1.5,1.5) → (0.75,0.75) → (1.125,1.125) → ...

    y ↑
  2   +
      |    •(1,1) ← true solution
  1.5 +   •
      |  ╱ \
  1   + •   •
      |╱     \
  0.5 +•      •
      +────────→ x
      0   1   2

Gauss-Seidel converges faster than Jacobi
```

## Error Analysis

### Types of Errors

**Round-off Error**: Due to finite precision arithmetic
$$\varepsilon_{\text{machine}} = 2^{-52} \approx 2.22 \times 10^{-16}$$ (double precision)

**Truncation Error**: From approximating infinite processes
- Taylor series truncation
- Numerical integration/differentiation

**Propagation Error**: How input errors affect output

### Condition Numbers

**Definition:**
$$\kappa(A) = \|A\| \|A^{-1}\|$$

**Interpretation:**
- $\kappa(A) = 1$: perfectly conditioned
- $\kappa(A) \gg 1$: ill-conditioned (small input changes → large output changes)

**Example:**
```
Well-conditioned:           Ill-conditioned:
A = [2 0]                   A = [1.0000  1.0000]
    [0 3]                       [1.0000  1.0001]

κ(A) = 1.5                  κ(A) ≈ 40,000

Small change in b:          Small change in b:
Δb = [0.01]                Δb = [0.01]
     [0.01]                     [0.01]

Small change in x           Large change in x!
```

## Optimization Methods

### Gradient Descent

**Update Rule:**
$$\mathbf{x}^{(k+1)} = \mathbf{x}^{(k)} - \alpha \nabla f(\mathbf{x}^{(k)})$$

**Learning Rate Selection:**
- Too small: slow convergence
- Too large: oscillation or divergence

**Visualization:**
```
f(x,y) = x² + 10y²   (elongated bowl)

Contour plot:        Gradient descent path:
     y ↑                   y ↑
   1   + ◯ ◯ ◯             1 + •
       | ◯ • ◯                | \
   0   + ◯ • ◯             0 +  •─•─•→ x
       | ◯ • ◯               0  1  2
  -1   + ◯ ◯ ◯
       +─────→ x

Large steps in x direction (small curvature)
Small steps in y direction (large curvature)
```

### Newton's Method for Optimization

**Update Rule:**
$$\mathbf{x}^{(k+1)} = \mathbf{x}^{(k)} - [H f(\mathbf{x}^{(k)})]^{-1} \nabla f(\mathbf{x}^{(k)})$$

where $H$ is the Hessian matrix (second derivatives)

**Advantage**: Quadratic convergence near optimum
**Disadvantage**: Requires computing and inverting Hessian

## Computational Complexity

### Algorithm Efficiency

**Root Finding:**
- Bisection: $O(\log \varepsilon^{-1})$ iterations
- Newton: $O(\log \log \varepsilon^{-1})$ iterations (quadratic convergence)

**Linear Systems:**
- Gaussian elimination: $O(n^3)$ operations
- Iterative methods: $O(n^2)$ per iteration

**Integration:**
- Trapezoidal: $O(h^{-1})$ evaluations for error $O(h^2)$
- Simpson's: $O(h^{-1})$ evaluations for error $O(h^4)$
- Monte Carlo: $O(\varepsilon^{-2})$ samples for error $O(\varepsilon)$

### Memory Considerations

**Sparse Matrices**: Store only non-zero elements
- Compressed Sparse Row (CSR)
- Compressed Sparse Column (CSC)  
- Coordinate format (COO)

**Matrix-free Methods**: Avoid storing large matrices
- Krylov subspace methods
- Conjugate gradient method

```python
# Example: Sparse matrix storage
import scipy.sparse as sp

# Dense matrix (wasteful for sparse data):
A_dense = [[2, 0, 0, 1],
           [0, 3, 0, 0], 
           [0, 0, 5, 0],
           [1, 0, 0, 4]]

# Sparse storage (efficient):
A_sparse = sp.csr_matrix(A_dense)
# Only stores: data=[2,1,3,5,1,4], indices=[0,3,1,2,0,3], indptr=[0,2,3,4,6]
```

This foundation in numerical methods enables efficient computation for scientific computing, machine learning, and engineering applications where analytical solutions are impractical.
