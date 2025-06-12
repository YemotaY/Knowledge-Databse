# Probability Theory

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **📊 [Statistics & Probability](../README.md)**

Probability theory provides the mathematical framework for reasoning about uncertainty and randomness. It's fundamental to machine learning, data science, computer security, and algorithm analysis.

## Overview

Probability applications in computer science:
- **Machine Learning**: Bayesian inference, probabilistic models
- **Data Science**: Statistical analysis, hypothesis testing
- **Algorithm Analysis**: Randomized algorithms, average-case complexity
- **Computer Security**: Cryptographic protocols, random number generation
- **Artificial Intelligence**: Uncertainty reasoning, decision making
- **Network Analysis**: Traffic modeling, reliability analysis

## Basic Concepts

### Sample Space and Events

**Sample Space ($\Omega$):**
The set of all possible outcomes of an experiment.

**Event:**
A subset of the sample space, $A \subseteq \Omega$.

**Set Operations on Events:**
- **Union**: $A \cup B$ (A or B occurs)
- **Intersection**: $A \cap B$ (both A and B occur)  
- **Complement**: $A^c = \Omega \setminus A$ (A does not occur)
- **Difference**: $A \setminus B = A \cap B^c$ (A but not B)

**Examples:**
- **Coin flip**: $\Omega = \{H, T\}$
- **Die roll**: $\Omega = \{1, 2, 3, 4, 5, 6\}$
- **Network packet**: $\Omega = \{\text{success}, \text{failure}, \text{timeout}\}$

**Venn Diagram Visualization:**
```
Sample Space Ω           Event Operations:
┌─────────────────┐     
│  ┌────────A────┐│      A ∪ B (Union):     A ∩ B (Intersection):
│  │      ┌──────┼┼──┐   ┌────────────┐     ┌────────────┐
│  │      │  ////││//│   │████████████│     │      ████  │
│  │      │ ////B││//│   │██A███B█████│     │  A   ████B │
│  │      │//////││//│   │████████████│     │      ████  │
│  │      └──────┼┼──┘   └────────────┘     └────────────┘
│  └─────────────┘│     
└─────────────────┘     A^c (Complement):   A \ B (Difference):
                        ┌────────────┐     ┌────────────┐
                        │████████████│     │██████      │
                        │████    ████│     │██A███  B   │
                        │████ A  ████│     │██████      │
                        │████████████│     └────────────┘
                        └────────────┘
```

### Probability Axioms

**Kolmogorov Axioms:**
For a probability measure $P$ on sample space $\Omega$:

1. **Non-negativity**: $P(A) \geq 0$ for any event $A \subseteq \Omega$
2. **Normalization**: $P(\Omega) = 1$  
3. **Additivity**: If $A \cap B = \emptyset$, then $P(A \cup B) = P(A) + P(B)$

**Basic Properties:**
- $P(\emptyset) = 0$ (empty set has probability 0)
- $P(A^c) = 1 - P(A)$ (complement rule)
- $P(A \cup B) = P(A) + P(B) - P(A \cap B)$ (inclusion-exclusion)

**Inclusion-Exclusion Principle:**
$$P(A \cup B \cup C) = P(A) + P(B) + P(C) - P(A \cap B) - P(A \cap C) - P(B \cap C) + P(A \cap B \cap C)$$

**Probability Visualization:**
```
Die Roll Example: Ω = {1,2,3,4,5,6}

Event A = {1,2,3} (roll ≤ 3)     Event B = {2,4,6} (roll even)
P(A) = 3/6 = 1/2                 P(B) = 3/6 = 1/2
A ∩ B = {2}                       P(A ∩ B) = 1/6
A ∪ B = {1,2,3,4,6}              P(A ∪ B) = 5/6

Verification: P(A ∪ B) = P(A) + P(B) - P(A ∩ B) = 1/2 + 1/2 - 1/6 = 5/6 ✓

Visual representation:
 ┌─A─┬─A∩B─┬─B─┐
 │ 1 │  2  │   │ 4
 │ 3 │     │   │ 6
 └───┴─────┴───┘
   5 (neither A nor B)
```
def probability_complement(p_a):
    return 1 - p_a

def probability_union(p_a, p_b, p_a_and_b):
    return p_a + p_b - p_a_and_b
```

### Conditional Probability

**Definition:**
P(A|B) = P(A ∩ B) / P(B), provided P(B) > 0

**Interpretation:**
Probability of event A given that event B has occurred.

```python
def conditional_probability(p_a_and_b, p_b):
    if p_b == 0:
        raise ValueError("Cannot condition on zero probability event")
    return p_a_and_b / p_b

# Example: Network reliability
p_system_works = 0.95
p_backup_works = 0.90
p_both_work = 0.85

# P(backup works | system works)
p_backup_given_system = conditional_probability(p_both_work, p_system_works)
print(f"P(backup | system) = {p_backup_given_system:.3f}")
```

### Independence

**Definition:**
Events A and B are independent if P(A ∩ B) = P(A) × P(B)

**Equivalent conditions:**
- P(A|B) = P(A)
- P(B|A) = P(B)

```python
def are_independent(p_a, p_b, p_a_and_b, tolerance=1e-10):
    return abs(p_a_and_b - p_a * p_b) < tolerance

# Example: Independent system failures
p_cpu_failure = 0.01
p_disk_failure = 0.02
p_both_failure = 0.0002

independent = are_independent(p_cpu_failure, p_disk_failure, p_both_failure)
print(f"Failures are independent: {independent}")
```

## Bayes' Theorem

**Formula:**
P(A|B) = P(B|A) × P(A) / P(B)

**Extended form:**
P(A|B) = P(B|A) × P(A) / [P(B|A) × P(A) + P(B|A^c) × P(A^c)]

```python
def bayes_theorem(p_b_given_a, p_a, p_b):
    return (p_b_given_a * p_a) / p_b

def bayes_extended(p_b_given_a, p_a, p_b_given_not_a):
    p_not_a = 1 - p_a
    p_b = p_b_given_a * p_a + p_b_given_not_a * p_not_a
    return (p_b_given_a * p_a) / p_b

# Example: Email spam detection
p_spam = 0.3  # Prior probability of spam
p_word_given_spam = 0.8  # P(contains "free" | spam)
p_word_given_not_spam = 0.1  # P(contains "free" | not spam)

p_spam_given_word = bayes_extended(p_word_given_spam, p_spam, p_word_given_not_spam)
print(f"P(spam | contains 'free') = {p_spam_given_word:.3f}")
```

## Random Variables

### Discrete Random Variables

**Definition:**
A function X: Ω → ℝ that assigns real numbers to outcomes.

**Probability Mass Function (PMF):**
P(X = x) for discrete values x

```python
class DiscreteRV:
    def __init__(self, values, probabilities):
        if abs(sum(probabilities) - 1.0) > 1e-10:
            raise ValueError("Probabilities must sum to 1")
        self.values = values
        self.probabilities = probabilities
        self.pmf = dict(zip(values, probabilities))
    
    def probability(self, x):
        return self.pmf.get(x, 0)
    
    def expected_value(self):
        return sum(x * p for x, p in zip(self.values, self.probabilities))
    
    def variance(self):
        mean = self.expected_value()
        return sum((x - mean)**2 * p for x, p in zip(self.values, self.probabilities))

# Example: Number of successful network connections
connections = DiscreteRV([0, 1, 2, 3], [0.1, 0.3, 0.4, 0.2])
print(f"E[connections] = {connections.expected_value()}")
print(f"Var[connections] = {connections.variance()}")
```

### Continuous Random Variables

**Probability Density Function (PDF):**
f(x) such that P(a ≤ X ≤ b) = ∫[a to b] f(x) dx

**Cumulative Distribution Function (CDF):**
F(x) = P(X ≤ x) = ∫[-∞ to x] f(t) dt

```python
import numpy as np
import scipy.stats as stats

# Example: Response time modeling with exponential distribution
def exponential_pdf(x, lambda_rate):
    return lambda_rate * np.exp(-lambda_rate * x) if x >= 0 else 0

def exponential_cdf(x, lambda_rate):
    return 1 - np.exp(-lambda_rate * x) if x >= 0 else 0

# Using scipy.stats
lambda_rate = 0.5  # Average response time = 2 seconds
response_time = stats.expon(scale=1/lambda_rate)

# Probability that response time is less than 3 seconds
prob = response_time.cdf(3)
print(f"P(response time < 3s) = {prob:.3f}")
```

## Common Probability Distributions

### Discrete Distributions

**Bernoulli Distribution:**
Models single success/failure trial.

```python
class BernoulliDistribution:
    def __init__(self, p):
        self.p = p
    
    def pmf(self, x):
        if x == 1:
            return self.p
        elif x == 0:
            return 1 - self.p
        else:
            return 0
    
    def mean(self):
        return self.p
    
    def variance(self):
        return self.p * (1 - self.p)
```

**Binomial Distribution:**
Number of successes in n independent Bernoulli trials.

```python
import math

def binomial_pmf(n, k, p):
    if k < 0 or k > n:
        return 0
    return math.comb(n, k) * (p ** k) * ((1 - p) ** (n - k))

# Example: Number of successful packet transmissions
n_packets = 10
success_rate = 0.9
k_successes = 8

prob = binomial_pmf(n_packets, k_successes, success_rate)
print(f"P(8 successful transmissions out of 10) = {prob:.3f}")

# Using scipy
binomial_dist = stats.binom(n_packets, success_rate)
print(f"Expected successes: {binomial_dist.mean()}")
print(f"Standard deviation: {binomial_dist.std()}")
```

**Poisson Distribution:**
Number of events in fixed time interval.

```python
def poisson_pmf(k, lambda_rate):
    return (lambda_rate ** k) * math.exp(-lambda_rate) / math.factorial(k)

# Example: Number of server requests per minute
lambda_rate = 5  # Average 5 requests per minute
k_requests = 3

prob = poisson_pmf(k_requests, lambda_rate)
print(f"P(3 requests in a minute) = {prob:.3f}")

# Using scipy
poisson_dist = stats.poisson(lambda_rate)
print(f"P(≤ 7 requests) = {poisson_dist.cdf(7):.3f}")
```

### Continuous Distributions

**Uniform Distribution:**
All values in interval equally likely.

```python
def uniform_pdf(x, a, b):
    return 1 / (b - a) if a <= x <= b else 0

def uniform_cdf(x, a, b):
    if x < a:
        return 0
    elif x > b:
        return 1
    else:
        return (x - a) / (b - a)

# Example: Random delay between 0 and 10 milliseconds
uniform_dist = stats.uniform(0, 10)
print(f"P(delay < 3ms) = {uniform_dist.cdf(3):.3f}")
```

**Normal (Gaussian) Distribution:**
Bell-shaped distribution, fundamental in statistics.

```python
def normal_pdf(x, mu, sigma):
    return (1 / (sigma * math.sqrt(2 * math.pi))) * \
           math.exp(-0.5 * ((x - mu) / sigma) ** 2)

# Example: Processing time distribution
mu = 100  # Mean processing time (ms)
sigma = 15  # Standard deviation

normal_dist = stats.norm(mu, sigma)
print(f"P(processing time < 120ms) = {normal_dist.cdf(120):.3f}")
print(f"95% of processing times are below: {normal_dist.ppf(0.95):.1f}ms")
```

**Exponential Distribution:**
Time between events in Poisson process.

```python
# Example: Time between system failures
def exponential_mean_time_to_failure(lambda_rate):
    return 1 / lambda_rate

lambda_failure = 0.001  # Failures per hour
mtbf = exponential_mean_time_to_failure(lambda_failure)
print(f"Mean time between failures: {mtbf:.0f} hours")

exp_dist = stats.expon(scale=mtbf)
print(f"P(failure within 100 hours) = {exp_dist.cdf(100):.3f}")
```

## Applications in Computer Science

### Randomized Algorithms

**Monte Carlo Methods:**
```python
import random

def estimate_pi(n_samples):
    """Estimate π using Monte Carlo method"""
    inside_circle = 0
    for _ in range(n_samples):
        x = random.uniform(-1, 1)
        y = random.uniform(-1, 1)
        if x*x + y*y <= 1:
            inside_circle += 1
    return 4 * inside_circle / n_samples

pi_estimate = estimate_pi(100000)
print(f"π estimate: {pi_estimate:.4f}")
```

**Randomized QuickSort:**
```python
def randomized_partition(arr, low, high):
    # Randomly choose pivot
    pivot_idx = random.randint(low, high)
    arr[pivot_idx], arr[high] = arr[high], arr[pivot_idx]
    
    # Regular partition
    pivot = arr[high]
    i = low - 1
    for j in range(low, high):
        if arr[j] <= pivot:
            i += 1
            arr[i], arr[j] = arr[j], arr[i]
    arr[i + 1], arr[high] = arr[high], arr[i + 1]
    return i + 1

def randomized_quicksort(arr, low, high):
    if low < high:
        pi = randomized_partition(arr, low, high)
        randomized_quicksort(arr, low, pi - 1)
        randomized_quicksort(arr, pi + 1, high)
```

### Probabilistic Data Structures

**Bloom Filter:**
```python
import hashlib

class BloomFilter:
    def __init__(self, size, hash_count):
        self.size = size
        self.hash_count = hash_count
        self.bit_array = [0] * size
    
    def _hash(self, item, seed):
        hasher = hashlib.sha256()
        hasher.update(f"{item}{seed}".encode())
        return int(hasher.hexdigest(), 16) % self.size
    
    def add(self, item):
        for i in range(self.hash_count):
            self.bit_array[self._hash(item, i)] = 1
    
    def contains(self, item):
        for i in range(self.hash_count):
            if self.bit_array[self._hash(item, i)] == 0:
                return False
        return True  # Possibly in set
    
    def false_positive_rate(self, n_items):
        # Theoretical false positive rate
        p = (1 - math.exp(-self.hash_count * n_items / self.size)) ** self.hash_count
        return p

# Example usage
bf = BloomFilter(1000, 3)
bf.add("user123")
bf.add("user456")

print(f"Contains user123: {bf.contains('user123')}")
print(f"Contains user789: {bf.contains('user789')}")
```

### Machine Learning Applications

**Naive Bayes Classifier:**
```python
class NaiveBayesClassifier:
    def __init__(self):
        self.class_probs = {}
        self.feature_probs = {}
    
    def train(self, X, y):
        n_samples = len(y)
        classes = set(y)
        
        # Calculate class probabilities
        for cls in classes:
            self.class_probs[cls] = y.count(cls) / n_samples
        
        # Calculate feature probabilities
        for cls in classes:
            self.feature_probs[cls] = {}
            class_samples = [X[i] for i in range(len(X)) if y[i] == cls]
            
            for feature_idx in range(len(X[0])):
                feature_values = [sample[feature_idx] for sample in class_samples]
                unique_values = set(feature_values)
                
                self.feature_probs[cls][feature_idx] = {}
                for value in unique_values:
                    count = feature_values.count(value)
                    self.feature_probs[cls][feature_idx][value] = count / len(class_samples)
    
    def predict(self, sample):
        predictions = {}
        
        for cls in self.class_probs:
            prob = self.class_probs[cls]
            
            for feature_idx, feature_value in enumerate(sample):
                if (feature_idx in self.feature_probs[cls] and 
                    feature_value in self.feature_probs[cls][feature_idx]):
                    prob *= self.feature_probs[cls][feature_idx][feature_value]
                else:
                    prob *= 1e-10  # Smoothing for unseen features
            
            predictions[cls] = prob
        
        return max(predictions, key=predictions.get)
```

### Performance Analysis

**Average-Case Analysis:**
```python
def expected_linear_search_comparisons(n, p_success):
    """Expected number of comparisons in linear search"""
    # If element is in array with probability p_success
    if p_success == 0:
        return n  # Always search entire array
    
    expected_position = (n + 1) / 2  # Expected position if found
    return p_success * expected_position + (1 - p_success) * n

# Example
n = 1000
p = 0.8
expected_comps = expected_linear_search_comparisons(n, p)
print(f"Expected comparisons: {expected_comps:.1f}")
```

**Load Balancing Analysis:**
```python
def server_utilization_model(arrival_rate, service_rate, n_servers):
    """M/M/n queue model for server utilization"""
    rho = arrival_rate / service_rate  # Traffic intensity
    utilization = rho / n_servers
    
    if utilization >= 1:
        return "System unstable"
    
    return {
        'utilization': utilization,
        'avg_customers': rho / (1 - utilization),
        'avg_wait_time': utilization / (service_rate * (1 - utilization))
    }

# Example: Web server analysis
results = server_utilization_model(
    arrival_rate=50,    # requests per second
    service_rate=60,    # requests per second per server
    n_servers=2
)
print(f"Server utilization: {results['utilization']:.2%}")
```

## Advanced Topics

### Markov Chains

```python
import numpy as np

class MarkovChain:
    def __init__(self, transition_matrix, states):
        self.transition_matrix = np.array(transition_matrix)
        self.states = states
        self.n_states = len(states)
    
    def simulate(self, initial_state, n_steps):
        current_state = initial_state
        path = [current_state]
        
        for _ in range(n_steps):
            current_idx = self.states.index(current_state)
            probabilities = self.transition_matrix[current_idx]
            current_state = np.random.choice(self.states, p=probabilities)
            path.append(current_state)
        
        return path
    
    def steady_state(self):
        # Find eigenvector corresponding to eigenvalue 1
        eigenvalues, eigenvectors = np.linalg.eig(self.transition_matrix.T)
        steady_state_idx = np.argmin(np.abs(eigenvalues - 1))
        steady_state = np.real(eigenvectors[:, steady_state_idx])
        return steady_state / np.sum(steady_state)

# Example: Network connection states
states = ['connected', 'disconnected']
transition_matrix = [
    [0.9, 0.1],    # From connected: 90% stay, 10% disconnect
    [0.3, 0.7]     # From disconnected: 30% connect, 70% stay
]

mc = MarkovChain(transition_matrix, states)
steady_state = mc.steady_state()
print(f"Long-term probability of being connected: {steady_state[0]:.3f}")
```

### Information Theory

```python
def entropy(probabilities):
    """Calculate Shannon entropy"""
    return -sum(p * math.log2(p) for p in probabilities if p > 0)

def mutual_information(joint_prob, marginal_x, marginal_y):
    """Calculate mutual information between two random variables"""
    mi = 0
    for i, px in enumerate(marginal_x):
        for j, py in enumerate(marginal_y):
            pxy = joint_prob[i][j]
            if pxy > 0:
                mi += pxy * math.log2(pxy / (px * py))
    return mi

# Example: Information content of messages
message_probs = [0.5, 0.25, 0.125, 0.125]  # Message probabilities
h = entropy(message_probs)
print(f"Average information content: {h:.3f} bits per message")
```

## Common Pitfalls and Best Practices

### Numerical Stability
```python
# Avoid numerical issues with very small probabilities
def log_probability_sum(log_probs):
    """Compute log(sum(exp(log_probs))) numerically stable"""
    max_log_prob = max(log_probs)
    return max_log_prob + math.log(sum(math.exp(lp - max_log_prob) for lp in log_probs))

# Use log probabilities for products
def log_likelihood(data, parameters):
    return sum(math.log(probability(x, parameters)) for x in data)
```

### Random Number Generation
```python
# Use cryptographically secure random numbers when needed
import secrets

def secure_random_choice(choices):
    return secrets.choice(choices)

def secure_random_float():
    return secrets.randbits(32) / (2**32)

# Seed random number generators for reproducibility
random.seed(42)
np.random.seed(42)
```

### Statistical Significance
```python
def confidence_interval(data, confidence=0.95):
    """Calculate confidence interval for mean"""
    n = len(data)
    mean = np.mean(data)
    std_err = stats.sem(data)
    
    # Use t-distribution for small samples
    t_critical = stats.t.ppf((1 + confidence) / 2, n - 1)
    margin_error = t_critical * std_err
    
    return (mean - margin_error, mean + margin_error)

# Example: Performance measurement with confidence intervals
response_times = [120, 115, 130, 125, 118, 122, 128, 119, 124, 121]
ci = confidence_interval(response_times)
print(f"95% confidence interval: [{ci[0]:.1f}, {ci[1]:.1f}] ms")
```

---

*Probability theory provides the mathematical framework for reasoning about uncertainty and making optimal decisions under incomplete information.*
