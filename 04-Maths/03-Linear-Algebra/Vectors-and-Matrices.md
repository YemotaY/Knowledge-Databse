# Vectors and Matrices

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **📊 [Linear Algebra](../README.md)**

Vectors and matrices are fundamental mathematical structures in linear algebra that form the backbone of computer graphics, machine learning, data analysis, and scientific computing.

## Overview

Linear algebra applications in computer science:
- **Computer Graphics**: 3D transformations, rotations, projections
- **Machine Learning**: Feature vectors, neural networks, dimensionality reduction
- **Data Science**: Principal component analysis, data processing
- **Computer Vision**: Image processing, pattern recognition
- **Game Development**: Physics simulations, collision detection
- **Optimization**: Linear programming, gradient descent

## Vectors

### Vector Basics

**Definition:**
A vector is an ordered collection of numbers, representing magnitude and direction in space.

**Mathematical Definition:**
$$\mathbf{v} = \begin{pmatrix} v_1 \\ v_2 \\ \vdots \\ v_n \end{pmatrix} \in \mathbb{R}^n$$

**Notation:**
- Column vector: $\mathbf{v} = [v_1, v_2, \ldots, v_n]^T$
- Row vector: $\mathbf{v} = [v_1, v_2, \ldots, v_n]$
- Component notation: $\mathbf{v} = (v_1, v_2, \ldots, v_n)$

**Geometric Interpretation:**
- **2D Vector**: Point in plane or directed line segment
- **3D Vector**: Point in space or directed line segment  
- **n-D Vector**: Point in n-dimensional space

**Vector Visualization:**
```
2D Vector (3, 2):        3D Vector (2, 3, 1):
    ↑ y                      z ↑
    |                          |
  2 +----→                     + (2,3,1)
    |    /                    /|
  1 +   /                    / |
    |  /                    /  |
  0 +------→ x             +---+----→ y
    0 1 2 3              0  1  2  3

Length: √(3² + 2²) = √13   Length: √(2² + 3² + 1²) = √14
```

### Vector Operations

**Vector Addition:**
$$\mathbf{u} + \mathbf{v} = \begin{pmatrix} u_1 + v_1 \\ u_2 + v_2 \\ \vdots \\ u_n + v_n \end{pmatrix}$$

**Geometric Visualization:**
```
Vector Addition (Parallelogram Rule):
    ↑
    |  
  v +----→ u+v
    |\    /|
    | \  / |
    |  \/  |
    | /u\  |
    |/   \ |
    +----→-+
       u

Tip-to-tail method:
u = (2,1), v = (1,2)
u + v = (3,3)
```

**Scalar Multiplication:**
$$c\mathbf{v} = \begin{pmatrix} cv_1 \\ cv_2 \\ \vdots \\ cv_n \end{pmatrix}$$

**Properties:**
- **Magnitude scaling**: $\|c\mathbf{v}\| = |c| \cdot \|\mathbf{v}\|$
- **Direction**: Same if $c > 0$, opposite if $c < 0$

**Visualization:**
```
Original: v = (2,1)     2v = (4,2)     -0.5v = (-1,-0.5)
    ↑                     ↑                  ↑
  1 +----→               2 +                 +
    |  /                  |  /               |\
    | /                   | /                | \
    +------→            4 +------→           +--\--→
    0  2                  0    4             -1  0.5
```
c**v** = [cv₁, cv₂, ..., cvₙ]

```python
def scalar_multiply(c, v):
    return [c * v[i] for i in range(len(v))]

# Using NumPy
v = np.array([1, 2, 3])
result = 2 * v  # [2, 4, 6]
```

**Dot Product (Inner Product):**
$$\mathbf{u} \cdot \mathbf{v} = \sum_{i=1}^{n} u_i v_i = u_1v_1 + u_2v_2 + \cdots + u_nv_n$$

**Geometric Interpretation:**
$$\mathbf{u} \cdot \mathbf{v} = \|\mathbf{u}\| \|\mathbf{v}\| \cos \theta$$

**Properties:**
- **Commutative**: $\mathbf{u} \cdot \mathbf{v} = \mathbf{v} \cdot \mathbf{u}$
- **Distributive**: $\mathbf{u} \cdot (\mathbf{v} + \mathbf{w}) = \mathbf{u} \cdot \mathbf{v} + \mathbf{u} \cdot \mathbf{w}$
- **Scalar multiplication**: $(c\mathbf{u}) \cdot \mathbf{v} = c(\mathbf{u} \cdot \mathbf{v})$

**Visualization:**
```
Dot Product Geometric Meaning:
u = (3,1), v = (2,2)

u·v = 3×2 + 1×2 = 8
|u| = √10, |v| = 2√2
cos θ = 8/(√10 × 2√2) = 8/(2√20) = 2/√5

    ↑ v
    |\
    | \
    |  \θ
    |   \
----+----→ u
    
θ ≈ 26.57° (acute angle → positive dot product)
```

**Cross Product (3D only):**
$$\mathbf{u} \times \mathbf{v} = \begin{pmatrix} u_2v_3 - u_3v_2 \\ u_3v_1 - u_1v_3 \\ u_1v_2 - u_2v_1 \end{pmatrix}$$

**Properties:**
- **Anti-commutative**: $\mathbf{u} \times \mathbf{v} = -(\mathbf{v} \times \mathbf{u})$
- **Magnitude**: $\|\mathbf{u} \times \mathbf{v}\| = \|\mathbf{u}\|\|\mathbf{v}\|\sin \theta$
- **Direction**: Perpendicular to both $\mathbf{u}$ and $\mathbf{v}$ (right-hand rule)

**Cross Product Visualization:**
```
Right-hand rule:        Area interpretation:
   z ↑ (u×v)           u×v magnitude = area of parallelogram
     |                      |\
     |                      | \
     +----→ y               |  \v
    /                       |   \
   /                        |    \
  ↙ x                       +-----\
u points toward x           u
v points toward y
u×v points toward z
```

### Vector Properties

**Magnitude (Length):**
$$\|\mathbf{v}\| = \sqrt{v_1^2 + v_2^2 + \cdots + v_n^2} = \sqrt{\mathbf{v} \cdot \mathbf{v}}$$

**Unit Vector:**
$$\hat{\mathbf{v}} = \frac{\mathbf{v}}{\|\mathbf{v}\|}$$

**Angle Between Vectors:**
$$\cos \theta = \frac{\mathbf{u} \cdot \mathbf{v}}{\|\mathbf{u}\| \|\mathbf{v}\|}$$

**Distance Between Points:**
$$d(\mathbf{u}, \mathbf{v}) = \|\mathbf{u} - \mathbf{v}\|$$

**Visualization Examples:**
```
Vector: v = (3, 4)           Unit vectors:           Angle calculation:
                            î = (1,0)                u = (1,0), v = (1,1)
    ↑                       ĵ = (0,1)                
  4 +----→ v                                         u·v = 1×1 + 0×1 = 1
    |   /|                  v̂ = v/|v|                |u| = 1, |v| = √2
  3 +  / |                    = (3,4)/5              cos θ = 1/√2
  2 + /  |                    = (0.6, 0.8)           θ = 45°
  1 +/   |
    +----+----→
    0  1 2 3 4

|v| = √(3² + 4²) = 5
```

# Using NumPy
v = np.array([3, 4])
result = np.linalg.norm(v)  # 5.0
```

**Unit Vector (Normalization):**
**û** = **v**/|**v**|

```python
def normalize(v):
    mag = magnitude(v)
    return [x / mag for x in v] if mag != 0 else v

# Using NumPy
v = np.array([3, 4])
unit_v = v / np.linalg.norm(v)  # [0.6, 0.8]
```

**Distance Between Vectors:**
d(**u**, **v**) = |**u** - **v**|

```python
def distance(u, v):
    diff = [u[i] - v[i] for i in range(len(u))]
    return magnitude(diff)

# Using NumPy
u = np.array([1, 2])
v = np.array([4, 6])
dist = np.linalg.norm(u - v)  # 5.0
```

**Angle Between Vectors:**
cos(θ) = (**u** · **v**) / (|**u**||**v**|)

```python
import math

def angle_between(u, v):
    dot_prod = dot_product(u, v)
    mag_u = magnitude(u)
    mag_v = magnitude(v)
    cos_theta = dot_prod / (mag_u * mag_v)
    return math.acos(max(-1, min(1, cos_theta)))  # Clamp for numerical stability
```

## Matrices

### Matrix Basics

**Definition:**
A matrix is a rectangular array of numbers arranged in rows and columns.

**Notation:**
```
A = [a₁₁  a₁₂  ...  a₁ₙ]
    [a₂₁  a₂₂  ...  a₂ₙ]
    [... ... ... ...]
    [aₘ₁  aₘ₂  ...  aₘₙ]
```

**Matrix Dimensions:**
- m × n matrix: m rows, n columns
- **Square Matrix**: m = n
- **Column Vector**: m × 1 matrix
- **Row Vector**: 1 × n matrix

### Matrix Operations

**Matrix Addition:**
C = A + B where cᵢⱼ = aᵢⱼ + bᵢⱼ

```python
def matrix_add(A, B):
    return [[A[i][j] + B[i][j] for j in range(len(A[0]))] 
            for i in range(len(A))]

# Using NumPy
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])
C = A + B  # [[6, 8], [10, 12]]
```

**Scalar Multiplication:**
C = cA where cᵢⱼ = c × aᵢⱼ

```python
def scalar_matrix_multiply(c, A):
    return [[c * A[i][j] for j in range(len(A[0]))] 
            for i in range(len(A))]

# Using NumPy
A = np.array([[1, 2], [3, 4]])
C = 2 * A  # [[2, 4], [6, 8]]
```

**Matrix Multiplication:**
C = AB where cᵢⱼ = Σₖ aᵢₖbₖⱼ

```python
def matrix_multiply(A, B):
    rows_A, cols_A = len(A), len(A[0])
    rows_B, cols_B = len(B), len(B[0])
    
    if cols_A != rows_B:
        raise ValueError("Cannot multiply matrices")
    
    C = [[0 for _ in range(cols_B)] for _ in range(rows_A)]
    
    for i in range(rows_A):
        for j in range(cols_B):
            for k in range(cols_A):
                C[i][j] += A[i][k] * B[k][j]
    
    return C

# Using NumPy
A = np.array([[1, 2], [3, 4]])
B = np.array([[5, 6], [7, 8]])
C = np.dot(A, B)  # or A @ B  # [[19, 22], [43, 50]]
```

**Matrix-Vector Multiplication:**
**y** = A**x** where yᵢ = Σⱼ aᵢⱼxⱼ

```python
def matrix_vector_multiply(A, x):
    return [sum(A[i][j] * x[j] for j in range(len(x))) 
            for i in range(len(A))]

# Using NumPy
A = np.array([[1, 2], [3, 4]])
x = np.array([5, 6])
y = A @ x  # [17, 39]
```

### Special Matrices

**Identity Matrix:**
Square matrix with 1s on diagonal, 0s elsewhere.

```python
def identity_matrix(n):
    return [[1 if i == j else 0 for j in range(n)] for i in range(n)]

# Using NumPy
I = np.eye(3)  # 3x3 identity matrix
```

**Zero Matrix:**
Matrix with all elements equal to zero.

```python
def zero_matrix(m, n):
    return [[0 for _ in range(n)] for _ in range(m)]

# Using NumPy
Z = np.zeros((3, 4))  # 3x4 zero matrix
```

**Diagonal Matrix:**
Square matrix with non-zero elements only on the diagonal.

```python
def diagonal_matrix(diag_elements):
    n = len(diag_elements)
    matrix = zero_matrix(n, n)
    for i in range(n):
        matrix[i][i] = diag_elements[i]
    return matrix

# Using NumPy
D = np.diag([1, 2, 3])  # Diagonal matrix with 1, 2, 3 on diagonal
```

**Transpose:**
A^T where (A^T)ᵢⱼ = aⱼᵢ

```python
def transpose(A):
    return [[A[j][i] for j in range(len(A))] for i in range(len(A[0]))]

# Using NumPy
A = np.array([[1, 2, 3], [4, 5, 6]])
A_T = A.T  # or np.transpose(A)
```

### Matrix Properties

**Determinant (Square Matrices):**
Scalar value that determines if matrix is invertible.

```python
def determinant_2x2(A):
    return A[0][0] * A[1][1] - A[0][1] * A[1][0]

def determinant_3x3(A):
    return (A[0][0] * (A[1][1] * A[2][2] - A[1][2] * A[2][1]) -
            A[0][1] * (A[1][0] * A[2][2] - A[1][2] * A[2][0]) +
            A[0][2] * (A[1][0] * A[2][1] - A[1][1] * A[2][0]))

# Using NumPy
A = np.array([[1, 2], [3, 4]])
det_A = np.linalg.det(A)  # -2.0
```

**Trace:**
Sum of diagonal elements.

```python
def trace(A):
    return sum(A[i][i] for i in range(min(len(A), len(A[0]))))

# Using NumPy
A = np.array([[1, 2], [3, 4]])
tr_A = np.trace(A)  # 5
```

**Matrix Inverse:**
A⁻¹ such that AA⁻¹ = A⁻¹A = I

```python
def inverse_2x2(A):
    det = determinant_2x2(A)
    if det == 0:
        raise ValueError("Matrix is not invertible")
    
    return [[A[1][1] / det, -A[0][1] / det],
            [-A[1][0] / det, A[0][0] / det]]

# Using NumPy
A = np.array([[1, 2], [3, 4]])
A_inv = np.linalg.inv(A)
```

**Rank:**
Maximum number of linearly independent rows/columns.

```python
# Using NumPy
A = np.array([[1, 2, 3], [4, 5, 6], [7, 8, 9]])
rank_A = np.linalg.matrix_rank(A)  # 2
```

## Linear Systems

**System of Linear Equations:**
A**x** = **b**

```python
# Solving using NumPy
A = np.array([[2, 1], [1, 3]])
b = np.array([3, 4])
x = np.linalg.solve(A, b)  # Solution vector
```

**Gaussian Elimination:**
```python
def gaussian_elimination(A, b):
    n = len(A)
    # Augmented matrix
    augmented = [A[i] + [b[i]] for i in range(n)]
    
    # Forward elimination
    for i in range(n):
        # Find pivot
        max_row = max(range(i, n), key=lambda r: abs(augmented[r][i]))
        augmented[i], augmented[max_row] = augmented[max_row], augmented[i]
        
        # Make all rows below this one 0 in current column
        for k in range(i + 1, n):
            factor = augmented[k][i] / augmented[i][i]
            for j in range(i, n + 1):
                augmented[k][j] -= factor * augmented[i][j]
    
    # Back substitution
    x = [0] * n
    for i in range(n - 1, -1, -1):
        x[i] = augmented[i][n]
        for j in range(i + 1, n):
            x[i] -= augmented[i][j] * x[j]
        x[i] /= augmented[i][i]
    
    return x
```

## Applications in Computer Graphics

### 2D Transformations
**Translation:**
```python
def translation_matrix_2d(tx, ty):
    return np.array([[1, 0, tx],
                     [0, 1, ty],
                     [0, 0, 1]])
```

**Rotation:**
```python
import math

def rotation_matrix_2d(angle):
    cos_a = math.cos(angle)
    sin_a = math.sin(angle)
    return np.array([[cos_a, -sin_a, 0],
                     [sin_a,  cos_a, 0],
                     [0,      0,     1]])
```

**Scaling:**
```python
def scaling_matrix_2d(sx, sy):
    return np.array([[sx, 0,  0],
                     [0,  sy, 0],
                     [0,  0,  1]])
```

### 3D Transformations
**Rotation around X-axis:**
```python
def rotation_x_3d(angle):
    cos_a = math.cos(angle)
    sin_a = math.sin(angle)
    return np.array([[1, 0,      0,     0],
                     [0, cos_a, -sin_a, 0],
                     [0, sin_a,  cos_a, 0],
                     [0, 0,      0,     1]])
```

**Perspective Projection:**
```python
def perspective_matrix(fov, aspect, near, far):
    f = 1.0 / math.tan(fov / 2.0)
    return np.array([[f/aspect, 0, 0, 0],
                     [0, f, 0, 0],
                     [0, 0, (far + near)/(near - far), (2*far*near)/(near - far)],
                     [0, 0, -1, 0]])
```

## Applications in Machine Learning

### Feature Vectors
```python
# Example: Document representation using TF-IDF
def tfidf_vector(document, vocabulary, corpus):
    vector = []
    for word in vocabulary:
        tf = document.count(word) / len(document)
        df = sum(1 for doc in corpus if word in doc)
        idf = math.log(len(corpus) / df) if df > 0 else 0
        vector.append(tf * idf)
    return vector
```

### Covariance Matrix
```python
def covariance_matrix(data):
    # data: n x m matrix (n samples, m features)
    mean = np.mean(data, axis=0)
    centered = data - mean
    return np.dot(centered.T, centered) / (len(data) - 1)
```

### Distance Metrics
```python
def euclidean_distance(u, v):
    return np.linalg.norm(u - v)

def manhattan_distance(u, v):
    return np.sum(np.abs(u - v))

def cosine_similarity(u, v):
    return np.dot(u, v) / (np.linalg.norm(u) * np.linalg.norm(v))
```

## Computational Considerations

### Numerical Stability
```python
# Use appropriate data types
A = np.array([[1, 2], [3, 4]], dtype=np.float64)

# Check condition number
cond = np.linalg.cond(A)
if cond > 1e12:
    print("Matrix is ill-conditioned")

# Use SVD for better numerical stability
U, s, Vt = np.linalg.svd(A)
```

### Performance Optimization
```python
# Use vectorized operations
# Slow
result = []
for i in range(len(vector1)):
    result.append(vector1[i] * vector2[i])

# Fast
result = vector1 * vector2  # Element-wise multiplication

# Use appropriate libraries
import scipy.sparse  # For sparse matrices
from numba import jit  # For JIT compilation
```

### Memory Management
```python
# Pre-allocate arrays
result = np.empty((1000, 1000))

# Use views instead of copies when possible
submatrix = A[10:20, 5:15]  # View, not copy

# Clear large arrays when done
del large_matrix
```

## Common Pitfalls

1. **Matrix Dimensions**: Always check compatibility for operations
2. **Singular Matrices**: Check determinant before inversion
3. **Numerical Precision**: Use appropriate tolerances for comparisons
4. **Memory Usage**: Consider sparse matrices for large, sparse data
5. **Performance**: Use vectorized operations and optimized libraries

---

*Vectors and matrices provide the fundamental tools for representing and manipulating multidimensional data in computer science applications.*
