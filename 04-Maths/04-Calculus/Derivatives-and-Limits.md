# Derivatives and Limits

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **📈 [Calculus](../README.md)**

Calculus is the mathematical study of continuous change, providing the foundation for optimization, machine learning, physics simulations, and algorithm analysis.

## Overview

Calculus applications in computer science:
- **Machine Learning**: Gradient descent, backpropagation, optimization
- **Computer Graphics**: Smooth animations, curve fitting, surface modeling
- **Algorithm Analysis**: Growth rates, continuous approximations
- **Numerical Methods**: Root finding, integration, differential equations
- **Game Development**: Physics engines, motion trajectories
- **Data Science**: Regression analysis, signal processing

## Limits

### Definition of a Limit

**Mathematical Definition:**
$$\lim_{x \to a} f(x) = L$$
means for every $\varepsilon > 0$, there exists $\delta > 0$ such that:
$$0 < |x - a| < \delta \Rightarrow |f(x) - L| < \varepsilon$$

**Intuitive Understanding:**
As $x$ approaches $a$, $f(x)$ approaches $L$.

**Limit Visualization:**
```
f(x) = (x² - 1)/(x - 1) as x → 1

     y
     ↑
   3 +     •(1,2) ← removable discontinuity
     |    ╱
   2 +   ╱
     |  ╱
   1 + ╱
     |╱
   0 +────────→ x
     0   1   2

lim[x→1] (x²-1)/(x-1) = lim[x→1] (x+1) = 2

Numerical approach:
x → 1⁻:  0.9 → 1.9,  0.99 → 1.99,  0.999 → 1.999
x → 1⁺:  1.1 → 2.1,  1.01 → 2.01,  1.001 → 2.001
Limit = 2
```

### Important Limits

**Fundamental Limits:**
$$\lim_{x \to 0} \frac{\sin x}{x} = 1$$

$$\lim_{x \to \infty} \left(1 + \frac{1}{x}\right)^x = e$$

$$\lim_{h \to 0} \frac{e^h - 1}{h} = 1$$

**L'Hôpital's Rule:**
If $\lim_{x \to a} f(x) = \lim_{x \to a} g(x) = 0$ or $\pm\infty$, then:
$$\lim_{x \to a} \frac{f(x)}{g(x)} = \lim_{x \to a} \frac{f'(x)}{g'(x)}$$

**Example Visualization:**
```
lim[x→0] sin(x)/x = 1

    y ↑
    1 +   • • •
      |  /     \
  0.5 + /       \
      |/         \
    0 +───────────→ x
     -π   0   π

sin(x)/x approaches 1 as x → 0
This limit is fundamental to the derivative of sin(x)
```

## Derivatives

### Definition of a Derivative

**Limit Definition:**
$$f'(x) = \lim_{h \to 0} \frac{f(x+h) - f(x)}{h}$$

**Geometric Interpretation:**
The derivative represents the slope of the tangent line at a point.

**Physical Interpretation:**
- **Position → Velocity**: $v(t) = \frac{d}{dt}s(t)$
- **Velocity → Acceleration**: $a(t) = \frac{d}{dt}v(t)$

**Derivative Visualization:**
```
f(x) = x²  at  x = 2

     y ↑
   9 +        •
     |       ╱|
   6 +      ╱ |
     |     ╱  |
   4 +•═══╱   |  ← tangent line: y = 4x - 4
     |  ╱     |    slope = f'(2) = 4
   1 + ╱      |
     |╱       |
   0 +────────+→ x
     0   1   2   3

f'(2) = lim[h→0] [(2+h)² - 4]/h = lim[h→0] [4h + h²]/h = 4
```

### Differentiation Rules

**Basic Rules:**
$$\frac{d}{dx}[c] = 0 \quad \text{(constant rule)}$$

$$\frac{d}{dx}[x^n] = nx^{n-1} \quad \text{(power rule)}$$

$$\frac{d}{dx}[cf(x)] = c \cdot f'(x) \quad \text{(scalar multiplication)}$$

$$\frac{d}{dx}[f(x) + g(x)] = f'(x) + g'(x) \quad \text{(sum rule)}$$

**Product Rule:**
$$\frac{d}{dx}[f(x) \cdot g(x)] = f'(x) \cdot g(x) + f(x) \cdot g'(x)$$

**Quotient Rule:**
$$\frac{d}{dx}\left[\frac{f(x)}{g(x)}\right] = \frac{f'(x) \cdot g(x) - f(x) \cdot g'(x)}{[g(x)]^2}$$

**Chain Rule:**
$$\frac{d}{dx}[f(g(x))] = f'(g(x)) \cdot g'(x)$$

### Common Derivatives

**Elementary Functions:**
$$\frac{d}{dx}[\sin x] = \cos x$$
$$\frac{d}{dx}[\cos x] = -\sin x$$
$$\frac{d}{dx}[e^x] = e^x$$
$$\frac{d}{dx}[\ln x] = \frac{1}{x}$$

**Chain Rule Examples:**
```
f(x) = sin(x²)
f'(x) = cos(x²) · 2x

g(x) = e^(3x+1)  
g'(x) = e^(3x+1) · 3

h(x) = (x² + 1)^5
h'(x) = 5(x² + 1)^4 · 2x = 10x(x² + 1)^4
```

## Applications

### Optimization

**Critical Points:**
Find where $f'(x) = 0$ or $f'(x)$ is undefined.

**Second Derivative Test:**
- If $f''(c) > 0$: local minimum
- If $f''(c) < 0$: local maximum  
- If $f''(c) = 0$: inconclusive

**Optimization Example:**
```
Minimize cost: f(x) = x³ - 6x² + 9x + 15

Step 1: f'(x) = 3x² - 12x + 9 = 3(x² - 4x + 3) = 3(x-1)(x-3)
Step 2: Critical points: x = 1, x = 3
Step 3: f''(x) = 6x - 12
        f''(1) = -6 < 0  → local maximum
        f''(3) = 6 > 0   → local minimum

    f(x) ↑
      20 +     •(1,19)
         |    ╱ \
      15 +   ╱   \
         |  ╱     \
      10 + ╱       •(3,15)
         |╱         
       5 +           
         +──────────→ x
         0   1   2   3   4

Minimum at x = 3 with value f(3) = 15
```

### Related Rates

**Problem Type:**
Given relationships between quantities and their rates of change, find unknown rates.

**Strategy:**
1. Identify the relationship equation
2. Differentiate both sides with respect to time
3. Substitute known values
4. Solve for the unknown rate

**Example:**
```
Balloon inflation: V = (4/3)πr³

Given: dr/dt = 2 cm/s
Find: dV/dt when r = 5 cm

Solution:
dV/dt = d/dt[(4/3)πr³] = (4/3)π · 3r² · dr/dt = 4πr² · dr/dt

When r = 5 and dr/dt = 2:
dV/dt = 4π(5)² · 2 = 200π cm³/s

Visualization:
    r(t) ↑
       5 +     •
         |    ╱
       3 +   ╱  slope = dr/dt = 2
         |  ╱
       1 + ╱
         +────→ t
         
Volume grows as V(t) = (4/3)π[r(t)]³
Rate of volume change = 4πr²(dr/dt)
```

## Computational Applications

### Numerical Differentiation

**Forward Difference:**
$$f'(x) \approx \frac{f(x+h) - f(x)}{h}$$

**Central Difference (more accurate):**
$$f'(x) \approx \frac{f(x+h) - f(x-h)}{2h}$$

**Implementation Example:**
```python
import numpy as np

def numerical_derivative(f, x, h=1e-5):
    """Compute numerical derivative using central difference"""
    return (f(x + h) - f(x - h)) / (2 * h)

# Example: f(x) = x²
f = lambda x: x**2
x = 2.0

analytical = 2 * x      # f'(x) = 2x
numerical = numerical_derivative(f, x)

print(f"Analytical: {analytical}")    # 4.0
print(f"Numerical:  {numerical}")     # ≈ 4.0
```

### Automatic Differentiation

**Forward Mode:**
Computes derivatives alongside function evaluation.

**Reverse Mode (Backpropagation):**
Used in machine learning for gradient computation.

**Example in Machine Learning:**
```
Loss function: L(w) = (1/2)(wx - y)²
Gradient: ∇L = (wx - y) · x

For optimization: w_new = w_old - α · ∇L
where α is the learning rate.

Visualization of gradient descent:
    L(w) ↑
         |     •
      10 +    ╱ \
         |   ╱   \
       5 +  ╱  •  \  ← step 1
         | ╱   •   \   ← step 2  
       0 +╱_____•___\→ w
        -2   -1  0  1  2

Converges to minimum via gradient descent
```
