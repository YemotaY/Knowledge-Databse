# Number Theory

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **📐 [Foundations](../README.md)**

Number theory is the study of integers and their properties. It forms the mathematical foundation for cryptography, computer security, and many algorithms in computer science.

## Overview

Number theory provides essential tools for:
- **Cryptographic Algorithms**: RSA, elliptic curve cryptography
- **Hash Functions**: Modular arithmetic in hash table implementations
- **Pseudorandom Number Generation**: Linear congruential generators
- **Error Detection**: Checksum algorithms, parity checks
- **Algorithm Design**: Modular exponentiation, greatest common divisor

## Fundamental Concepts

### Prime Numbers
A prime number is a natural number greater than 1 that has no positive divisors other than 1 and itself.

**Properties:**
- **Fundamental Theorem of Arithmetic**: Every integer > 1 is either prime or can be uniquely factored into primes
- **Infinitude**: There are infinitely many prime numbers (Euclid's proof)
- **Distribution**: Prime gaps and the Prime Number Theorem

**Applications in CS:**
- **Cryptography**: RSA relies on difficulty of factoring large primes
- **Hash Functions**: Prime moduli reduce clustering
- **Data Structures**: Prime-sized hash tables minimize collisions

### Divisibility and GCD

**Greatest Common Divisor (GCD):**
- `gcd(a, b)` is the largest positive integer that divides both a and b
- **Euclidean Algorithm**: Efficient method to compute GCD

```
Algorithm: Euclidean GCD
Input: integers a, b with a ≥ b > 0
Output: gcd(a, b)

while b ≠ 0:
    temp = b
    b = a mod b
    a = temp
return a
```

**Extended Euclidean Algorithm:**
Finds integers x, y such that ax + by = gcd(a, b)

**Applications:**
- **Modular Inverse**: Computing multiplicative inverses
- **Cryptography**: Key generation in RSA
- **Rational Arithmetic**: Reducing fractions

### Modular Arithmetic

**Definition:**
a ≡ b (mod n) means n divides (a - b)

**Properties:**
- **Addition**: (a + b) mod n = ((a mod n) + (b mod n)) mod n
- **Multiplication**: (a × b) mod n = ((a mod n) × (b mod n)) mod n
- **Distributive**: (a + b) mod n = (a mod n + b mod n) mod n

**Modular Exponentiation:**
Computing a^b mod n efficiently using repeated squaring:

```
Algorithm: Fast Modular Exponentiation
Input: base a, exponent b, modulus n
Output: a^b mod n

result = 1
a = a mod n
while b > 0:
    if b is odd:
        result = (result × a) mod n
    b = b // 2
    a = (a × a) mod n
return result
```

**Applications:**
- **Cryptography**: RSA encryption/decryption
- **Hash Functions**: Polynomial rolling hash
- **Pseudorandom Generators**: Linear congruential generators

## Advanced Topics

### Chinese Remainder Theorem
If n₁, n₂, ..., nₖ are pairwise coprime, then the system:
- x ≡ a₁ (mod n₁)
- x ≡ a₂ (mod n₂)
- ...
- x ≡ aₖ (mod nₖ)

has a unique solution modulo N = n₁ × n₂ × ... × nₖ.

**Applications:**
- **RSA Speedup**: CRT optimization for decryption
- **Parallel Computation**: Breaking large problems into smaller ones
- **Error Correction**: Reed-Solomon codes

### Fermat's Little Theorem
If p is prime and a is not divisible by p, then:
a^(p-1) ≡ 1 (mod p)

**Corollary:**
For any integer a and prime p: a^p ≡ a (mod p)

**Applications:**
- **Primality Testing**: Fermat test for probable primes
- **Modular Inverse**: a^(-1) ≡ a^(p-2) (mod p) when p is prime
- **Cryptography**: Theoretical foundation for many algorithms

### Euler's Theorem
For any integer a coprime to n:
a^φ(n) ≡ 1 (mod n)

Where φ(n) is Euler's totient function counting integers ≤ n that are coprime to n.

**Euler's Totient Function:**
- φ(p) = p - 1 for prime p
- φ(p^k) = p^k - p^(k-1) = p^(k-1)(p - 1)
- φ(mn) = φ(m)φ(n) if gcd(m,n) = 1

**Applications:**
- **RSA Algorithm**: e × d ≡ 1 (mod φ(n))
- **Multiplicative Order**: Finding periods in modular arithmetic
- **Carmichael Function**: Lambda function for cryptographic applications

## Cryptographic Applications

### RSA Algorithm
**Key Generation:**
1. Choose large primes p, q
2. Compute n = pq, φ(n) = (p-1)(q-1)
3. Choose e coprime to φ(n)
4. Compute d ≡ e^(-1) (mod φ(n))
5. Public key: (n, e), Private key: (n, d)

**Encryption/Decryption:**
- Encrypt: c ≡ m^e (mod n)
- Decrypt: m ≡ c^d (mod n)

### Discrete Logarithm Problem
Given g, h, p where p is prime, find x such that:
g^x ≡ h (mod p)

**Applications:**
- **Diffie-Hellman Key Exchange**
- **ElGamal Encryption**
- **Digital Signature Algorithm (DSA)**

## Computational Considerations

### Primality Testing
**Trial Division**: Check divisibility up to √n
- Time complexity: O(√n)
- Practical for small numbers

**Miller-Rabin Test**: Probabilistic primality test
- Time complexity: O(k log³ n) for k rounds
- Very reliable for large numbers

**AKS Test**: Deterministic polynomial-time test
- Time complexity: O(log⁶ n)
- Theoretical importance, less practical

### Integer Factorization
**Difficulty**: No known polynomial-time algorithm
**Methods:**
- **Trial Division**: O(√n)
- **Pollard's Rho**: O(n^(1/4))
- **Quadratic Sieve**: Sub-exponential
- **General Number Field Sieve**: Most efficient for large numbers

**Cryptographic Security**: Based on assumed hardness of factoring

## Programming Implementation

### GCD Implementation
```python
def gcd(a, b):
    while b:
        a, b = b, a % b
    return a

def extended_gcd(a, b):
    if a == 0:
        return b, 0, 1
    gcd, x1, y1 = extended_gcd(b % a, a)
    x = y1 - (b // a) * x1
    y = x1
    return gcd, x, y
```

### Modular Exponentiation
```python
def mod_exp(base, exp, mod):
    result = 1
    base = base % mod
    while exp > 0:
        if exp % 2 == 1:
            result = (result * base) % mod
        exp = exp // 2
        base = (base * base) % mod
    return result
```

### Miller-Rabin Primality Test
```python
def miller_rabin(n, k=5):
    if n < 2: return False
    if n == 2 or n == 3: return True
    if n % 2 == 0: return False
    
    # Write n-1 as d * 2^r
    r = 0
    d = n - 1
    while d % 2 == 0:
        r += 1
        d //= 2
    
    for _ in range(k):
        a = random.randrange(2, n - 1)
        x = mod_exp(a, d, n)
        if x == 1 or x == n - 1:
            continue
        for _ in range(r - 1):
            x = mod_exp(x, 2, n)
            if x == n - 1:
                break
        else:
            return False
    return True
```

## Common Pitfalls

1. **Integer Overflow**: Use appropriate data types for large numbers
2. **Negative Modulo**: Handle negative numbers correctly in modular arithmetic
3. **Coprimality**: Ensure gcd(a, n) = 1 for modular inverse to exist
4. **Randomness**: Use cryptographically secure random number generators

## Further Reading

- **Concrete Mathematics** by Graham, Knuth, Patashnik
- **Introduction to Algorithms** by Cormen et al. (Number-theoretic algorithms)
- **Handbook of Applied Cryptography** by Menezes, van Oorschot, Vanstone
- **A Computational Introduction to Number Theory and Algebra** by Shoup

---

*Number theory provides the mathematical foundation for modern cryptography and secure communication.*
