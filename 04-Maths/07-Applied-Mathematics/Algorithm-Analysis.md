# Algorithm Analysis

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **🎯 [Applied Mathematics](../README.md)**

Algorithm analysis is the mathematical study of algorithm performance, providing tools to predict and compare the efficiency of different computational approaches. It's fundamental to computer science and software engineering.

## Overview

Algorithm analysis applications:
- **Performance Prediction**: Estimating runtime and memory usage
- **Algorithm Comparison**: Choosing between different approaches
- **Scalability Assessment**: Understanding behavior with large inputs
- **Optimization**: Identifying bottlenecks and improvement opportunities
- **Resource Planning**: Capacity planning for systems and applications
- **Complexity Theory**: Understanding computational limits

## Asymptotic Notation

### Big-O Notation

**Definition:**
f(n) = O(g(n)) if there exist positive constants c and n₀ such that f(n) ≤ c·g(n) for all n ≥ n₀.

**Interpretation:**
O(g(n)) represents an upper bound on the growth rate of f(n).

```python
import math
import matplotlib.pyplot as plt
import numpy as np

def plot_complexity_functions():
    """Plot common complexity functions"""
    n = np.linspace(1, 100, 100)
    
    plt.figure(figsize=(12, 8))
    
    # Common complexity functions
    plt.plot(n, np.ones_like(n), label='O(1) - Constant', linewidth=2)
    plt.plot(n, np.log2(n), label='O(log n) - Logarithmic', linewidth=2)
    plt.plot(n, n, label='O(n) - Linear', linewidth=2)
    plt.plot(n, n * np.log2(n), label='O(n log n) - Linearithmic', linewidth=2)
    plt.plot(n, n**2, label='O(n²) - Quadratic', linewidth=2)
    plt.plot(n, n**3, label='O(n³) - Cubic', linewidth=2)
    plt.plot(n, 2**n, label='O(2ⁿ) - Exponential', linewidth=2)
    
    plt.xlabel('Input Size (n)')
    plt.ylabel('Time Complexity')
    plt.title('Common Time Complexity Functions')
    plt.legend()
    plt.yscale('log')
    plt.grid(True, alpha=0.3)
    plt.show()

# Uncomment to show plot
# plot_complexity_functions()
```

### Omega and Theta Notation

**Big-Omega (Ω):**
f(n) = Ω(g(n)) if there exist positive constants c and n₀ such that f(n) ≥ c·g(n) for all n ≥ n₀.
(Lower bound)

**Big-Theta (Θ):**
f(n) = Θ(g(n)) if f(n) = O(g(n)) and f(n) = Ω(g(n)).
(Tight bound)

```python
class ComplexityAnalyzer:
    def __init__(self):
        self.complexity_classes = {
            'constant': lambda n: 1,
            'logarithmic': lambda n: math.log2(n),
            'linear': lambda n: n,
            'linearithmic': lambda n: n * math.log2(n),
            'quadratic': lambda n: n**2,
            'cubic': lambda n: n**3,
            'exponential': lambda n: 2**n,
            'factorial': lambda n: math.factorial(min(n, 10))  # Limit for computation
        }
    
    def classify_function(self, func, test_sizes):
        """Attempt to classify a function's complexity"""
        measurements = []
        for n in test_sizes:
            try:
                measurements.append(func(n))
            except:
                measurements.append(float('inf'))
        
        best_fit = None
        best_ratio = float('inf')
        
        for name, complexity_func in self.complexity_classes.items():
            try:
                ratios = []
                for i, n in enumerate(test_sizes):
                    if measurements[i] != 0 and complexity_func(n) != 0:
                        ratio = measurements[i] / complexity_func(n)
                        ratios.append(ratio)
                
                if ratios:
                    # Check if ratios are roughly constant (indicating same complexity)
                    avg_ratio = sum(ratios) / len(ratios)
                    variance = sum((r - avg_ratio)**2 for r in ratios) / len(ratios)
                    coefficient_of_variation = math.sqrt(variance) / avg_ratio if avg_ratio > 0 else float('inf')
                    
                    if coefficient_of_variation < best_ratio:
                        best_ratio = coefficient_of_variation
                        best_fit = name
            except:
                continue
        
        return best_fit, best_ratio

# Example usage
analyzer = ComplexityAnalyzer()

# Test with a quadratic function
def quadratic_example(n):
    return n * n + 2 * n + 1

test_sizes = [10, 20, 50, 100, 200]
classification, confidence = analyzer.classify_function(quadratic_example, test_sizes)
print(f"Function classified as: {classification} (confidence: {confidence:.3f})")
```

## Time Complexity Analysis

### Elementary Operations

**Basic Operations:**
- Arithmetic: +, -, *, /, %
- Comparison: ==, !=, <, >, <=, >=
- Assignment: =
- Array access: arr[i]
- Function call overhead

**Counting Operations:**
```python
def count_operations_example(arr):
    """Example function with operation counting"""
    n = len(arr)                    # 1 operation
    total = 0                       # 1 operation
    
    for i in range(n):              # n+1 comparisons, n increments
        total += arr[i]             # n additions, n array accesses
    
    return total                    # 1 operation
    
    # Total operations: 1 + 1 + (n+1) + n + n + n + 1 = 4n + 4 = O(n)
```

### Loop Analysis

**Single Loops:**
```python
def single_loop_examples():
    """Examples of single loop complexities"""
    
    # O(n) - Linear loop
    def linear_loop(n):
        count = 0
        for i in range(n):          # Runs n times
            count += 1              # O(1) operation
        return count                # Total: O(n)
    
    # O(log n) - Logarithmic loop
    def logarithmic_loop(n):
        count = 0
        i = 1
        while i < n:                # Runs log₂(n) times
            count += 1              # O(1) operation
            i *= 2                  # Double each iteration
        return count                # Total: O(log n)
    
    # O(√n) - Square root loop
    def sqrt_loop(n):
        count = 0
        i = 1
        while i * i <= n:           # Runs √n times
            count += 1              # O(1) operation
            i += 1
        return count                # Total: O(√n)
    
    return linear_loop, logarithmic_loop, sqrt_loop

# Test the functions
linear, log, sqrt_func = single_loop_examples()
n = 1000
print(f"Linear loop({n}): {linear(n)}")
print(f"Log loop({n}): {log(n)}")
print(f"Sqrt loop({n}): {sqrt_func(n)}")
```

**Nested Loops:**
```python
def nested_loop_examples():
    """Examples of nested loop complexities"""
    
    # O(n²) - Quadratic
    def quadratic_loops(n):
        count = 0
        for i in range(n):          # Outer loop: n times
            for j in range(n):      # Inner loop: n times for each i
                count += 1          # Executed n × n = n² times
        return count                # Total: O(n²)
    
    # O(n²) - Triangular (still quadratic)
    def triangular_loops(n):
        count = 0
        for i in range(n):          # Outer loop: n times
            for j in range(i):      # Inner loop: 0, 1, 2, ..., n-1 times
                count += 1          # Executed ∑(i=0 to n-1) i = n(n-1)/2 times
        return count                # Total: O(n²)
    
    # O(n³) - Cubic
    def cubic_loops(n):
        count = 0
        for i in range(n):          # Outer loop: n times
            for j in range(n):      # Middle loop: n times for each i
                for k in range(n):  # Inner loop: n times for each (i,j)
                    count += 1      # Executed n³ times
        return count                # Total: O(n³)
    
    return quadratic_loops, triangular_loops, cubic_loops

# Test nested loops
quad, tri, cubic = nested_loop_examples()
n = 100
print(f"Quadratic loops({n}): {quad(n)}")
print(f"Triangular loops({n}): {tri(n)}")
print(f"Cubic loops({n}): {cubic(n)}")
```

### Recursive Analysis

**Recurrence Relations:**
Method for analyzing recursive algorithms.

```python
import time
from functools import lru_cache

def fibonacci_analysis():
    """Analyze different Fibonacci implementations"""
    
    # O(2ⁿ) - Exponential (naive recursion)
    def fib_naive(n):
        if n <= 1:
            return n
        return fib_naive(n-1) + fib_naive(n-2)
    
    # O(n) - Linear (memoized)
    @lru_cache(maxsize=None)
    def fib_memoized(n):
        if n <= 1:
            return n
        return fib_memoized(n-1) + fib_memoized(n-2)
    
    # O(n) - Linear (iterative)
    def fib_iterative(n):
        if n <= 1:
            return n
        a, b = 0, 1
        for _ in range(2, n + 1):
            a, b = b, a + b
        return b
    
    # O(log n) - Logarithmic (matrix exponentiation)
    def fib_matrix(n):
        if n <= 1:
            return n
        
        def matrix_mult(A, B):
            return [[A[0][0]*B[0][0] + A[0][1]*B[1][0], A[0][0]*B[0][1] + A[0][1]*B[1][1]],
                    [A[1][0]*B[0][0] + A[1][1]*B[1][0], A[1][0]*B[0][1] + A[1][1]*B[1][1]]]
        
        def matrix_power(matrix, power):
            if power == 1:
                return matrix
            if power % 2 == 0:
                half_power = matrix_power(matrix, power // 2)
                return matrix_mult(half_power, half_power)
            else:
                return matrix_mult(matrix, matrix_power(matrix, power - 1))
        
        fib_matrix = [[1, 1], [1, 0]]
        result_matrix = matrix_power(fib_matrix, n)
        return result_matrix[0][1]
    
    return fib_naive, fib_memoized, fib_iterative, fib_matrix

def benchmark_fibonacci():
    """Benchmark different Fibonacci implementations"""
    fib_naive, fib_memoized, fib_iterative, fib_matrix = fibonacci_analysis()
    
    test_values = [10, 20, 30]
    
    for n in test_values:
        print(f"\nFibonacci({n}):")
        
        # Benchmark each implementation
        implementations = [
            ("Naive", fib_naive),
            ("Memoized", fib_memoized),
            ("Iterative", fib_iterative),
            ("Matrix", fib_matrix)
        ]
        
        for name, func in implementations:
            if name == "Naive" and n > 35:  # Skip naive for large n
                print(f"{name}: Skipped (too slow)")
                continue
                
            start_time = time.time()
            result = func(n)
            end_time = time.time()
            
            print(f"{name}: {result} (Time: {end_time - start_time:.6f}s)")

# Uncomment to run benchmark
# benchmark_fibonacci()
```

**Master Theorem:**
For recurrences of the form T(n) = aT(n/b) + f(n):

```python
def master_theorem_solver(a, b, f_complexity):
    """
    Solve recurrence using Master Theorem
    T(n) = aT(n/b) + f(n)
    
    f_complexity should be in the form (exponent, log_factor)
    e.g., O(n^2) -> (2, 0), O(n log n) -> (1, 1)
    """
    import math
    
    log_b_a = math.log(a) / math.log(b)
    f_exp, f_log = f_complexity
    
    print(f"Recurrence: T(n) = {a}T(n/{b}) + O(n^{f_exp}")
    if f_log > 0:
        print(f" * (log n)^{f_log})")
    else:
        print(")")
    
    print(f"log_{b}({a}) = {log_b_a:.3f}")
    
    # Case 1: f(n) = O(n^c) where c < log_b(a)
    if f_exp < log_b_a:
        print(f"Case 1: f(n) = O(n^{f_exp}) and {f_exp} < {log_b_a:.3f}")
        print(f"Solution: T(n) = Θ(n^{log_b_a:.3f})")
    
    # Case 2: f(n) = Θ(n^c * log^k(n)) where c = log_b(a)
    elif abs(f_exp - log_b_a) < 0.001:  # Approximately equal
        print(f"Case 2: f(n) = Θ(n^{f_exp} * log^{f_log}(n)) and {f_exp} ≈ {log_b_a:.3f}")
        print(f"Solution: T(n) = Θ(n^{log_b_a:.3f} * log^{f_log + 1}(n))")
    
    # Case 3: f(n) = Ω(n^c) where c > log_b(a) and regularity condition
    elif f_exp > log_b_a:
        print(f"Case 3: f(n) = Ω(n^{f_exp}) and {f_exp} > {log_b_a:.3f}")
        print(f"Solution: T(n) = Θ(n^{f_exp}")
        if f_log > 0:
            print(f" * log^{f_log}(n))")
        else:
            print(")")
    
    return log_b_a

# Examples
print("Example 1: Merge Sort")
master_theorem_solver(2, 2, (1, 0))  # T(n) = 2T(n/2) + O(n)

print("\nExample 2: Binary Search")
master_theorem_solver(1, 2, (0, 0))  # T(n) = T(n/2) + O(1)

print("\nExample 3: Matrix Multiplication (naive)")
master_theorem_solver(8, 2, (2, 0))  # T(n) = 8T(n/2) + O(n²)
```

## Space Complexity Analysis

### Memory Usage Patterns

```python
def space_complexity_examples():
    """Examples of different space complexities"""
    
    # O(1) - Constant space
    def constant_space_sum(arr):
        total = 0                   # O(1) space
        for num in arr:             # Loop variable doesn't count extra space
            total += num
        return total                # Total space: O(1)
    
    # O(n) - Linear space
    def linear_space_copy(arr):
        copy = []                   # O(n) space for the copy
        for num in arr:
            copy.append(num)        # Each append uses O(1) space
        return copy                 # Total space: O(n)
    
    # O(n) - Linear space (recursive call stack)
    def recursive_factorial(n):
        if n <= 1:                  # Base case
            return 1
        return n * recursive_factorial(n-1)  # O(n) call stack space
    
    # O(log n) - Logarithmic space (binary search)
    def binary_search_recursive(arr, target, left=0, right=None):
        if right is None:
            right = len(arr) - 1
        
        if left > right:
            return -1
        
        mid = (left + right) // 2
        if arr[mid] == target:
            return mid
        elif arr[mid] < target:
            return binary_search_recursive(arr, target, mid + 1, right)
        else:
            return binary_search_recursive(arr, target, left, mid - 1)
        # Call stack depth: O(log n)
    
    # O(n²) - Quadratic space
    def create_2d_array(n):
        matrix = []                 # O(n²) space for n×n matrix
        for i in range(n):
            row = []
            for j in range(n):
                row.append(i * j)
            matrix.append(row)
        return matrix               # Total space: O(n²)
    
    return constant_space_sum, linear_space_copy, recursive_factorial, binary_search_recursive, create_2d_array

# Example usage
examples = space_complexity_examples()
arr = [1, 2, 3, 4, 5]

print(f"Constant space sum: {examples[0](arr)}")
print(f"Linear space copy: {examples[1](arr)}")
print(f"Recursive factorial(5): {examples[2](5)}")
print(f"Binary search for 3: {examples[3](arr, 3)}")
print(f"2D array (3x3): {examples[4](3)}")
```

### Auxiliary Space vs Total Space

```python
def space_analysis_comparison():
    """Compare auxiliary space vs total space"""
    
    # In-place algorithm: O(1) auxiliary space
    def bubble_sort_inplace(arr):
        n = len(arr)
        # Input space: O(n), Auxiliary space: O(1)
        for i in range(n):
            for j in range(0, n - i - 1):
                if arr[j] > arr[j + 1]:
                    arr[j], arr[j + 1] = arr[j + 1], arr[j]  # O(1) temp space
        return arr
    
    # Out-of-place algorithm: O(n) auxiliary space
    def merge_sort(arr):
        if len(arr) <= 1:
            return arr
        
        mid = len(arr) // 2
        left = merge_sort(arr[:mid])      # O(n) space for copying
        right = merge_sort(arr[mid:])     # O(n) space for copying
        
        return merge(left, right)         # O(n) space for merging
    
    def merge(left, right):
        result = []                       # O(n) auxiliary space
        i = j = 0
        
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                result.append(left[i])
                i += 1
            else:
                result.append(right[j])
                j += 1
        
        result.extend(left[i:])
        result.extend(right[j:])
        return result
    
    return bubble_sort_inplace, merge_sort

# Demonstrate space usage
bubble_sort, merge_sort = space_analysis_comparison()

test_array = [64, 34, 25, 12, 22, 11, 90]
print(f"Original array: {test_array}")

# Bubble sort (modifies original, O(1) auxiliary space)
bubble_result = bubble_sort(test_array.copy())
print(f"Bubble sort result: {bubble_result}")

# Merge sort (creates new arrays, O(n) auxiliary space)
merge_result = merge_sort(test_array.copy())
print(f"Merge sort result: {merge_result}")
```

## Amortized Analysis

### Aggregate Method

```python
class DynamicArray:
    """Dynamic array with amortized analysis"""
    
    def __init__(self):
        self.capacity = 1
        self.size = 0
        self.data = [None] * self.capacity
        self.total_cost = 0
        self.operations = 0
    
    def append(self, item):
        """Append with amortized O(1) complexity"""
        self.operations += 1
        
        if self.size == self.capacity:
            # Resize operation: O(n) cost
            old_capacity = self.capacity
            self.capacity *= 2
            new_data = [None] * self.capacity
            
            # Copy all elements: O(n) operations
            for i in range(self.size):
                new_data[i] = self.data[i]
            
            self.data = new_data
            self.total_cost += self.size  # Cost of copying
            print(f"Resized from {old_capacity} to {self.capacity} (cost: {self.size})")
        
        # Insert operation: O(1) cost
        self.data[self.size] = item
        self.size += 1
        self.total_cost += 1  # Cost of insertion
    
    def get_amortized_cost(self):
        """Calculate amortized cost per operation"""
        return self.total_cost / self.operations if self.operations > 0 else 0
    
    def analyze_sequence(self, n):
        """Analyze cost of n append operations"""
        print(f"Analyzing {n} append operations:")
        
        for i in range(n):
            self.append(f"item_{i}")
        
        print(f"Total operations: {self.operations}")
        print(f"Total cost: {self.total_cost}")
        print(f"Amortized cost per operation: {self.get_amortized_cost():.2f}")
        print(f"Final capacity: {self.capacity}")
        print(f"Final size: {self.size}")

# Demonstrate amortized analysis
dynamic_array = DynamicArray()
dynamic_array.analyze_sequence(16)
```

### Potential Method

```python
class StackWithMultipop:
    """Stack with multipop operation for potential method analysis"""
    
    def __init__(self):
        self.stack = []
        self.operations = 0
        self.actual_cost = 0
    
    def push(self, item):
        """Push operation: actual cost = 1"""
        self.stack.append(item)
        self.operations += 1
        self.actual_cost += 1
        return 1  # Actual cost
    
    def pop(self):
        """Pop operation: actual cost = 1"""
        if self.stack:
            item = self.stack.pop()
            self.operations += 1
            self.actual_cost += 1
            return item, 1  # Item and actual cost
        return None, 0
    
    def multipop(self, k):
        """Multipop operation: actual cost = min(k, stack_size)"""
        actual_pops = 0
        popped_items = []
        
        while self.stack and actual_pops < k:
            popped_items.append(self.stack.pop())
            actual_pops += 1
        
        self.operations += 1
        self.actual_cost += actual_pops
        return popped_items, actual_pops  # Items and actual cost
    
    def potential(self):
        """Potential function: Φ(D) = number of elements in stack"""
        return len(self.stack)
    
    def amortized_cost(self, operation, *args):
        """Calculate amortized cost using potential method"""
        phi_before = self.potential()
        
        if operation == "push":
            actual_cost = self.push(*args)
        elif operation == "pop":
            _, actual_cost = self.pop()
        elif operation == "multipop":
            _, actual_cost = self.multipop(*args)
        else:
            raise ValueError("Unknown operation")
        
        phi_after = self.potential()
        amortized_cost = actual_cost + phi_after - phi_before
        
        return actual_cost, amortized_cost, phi_before, phi_after

# Demonstrate potential method
stack = StackWithMultipop()

print("Amortized Analysis using Potential Method:")
print("Operation | Actual Cost | Φ(before) | Φ(after) | Amortized Cost")
print("-" * 65)

operations = [
    ("push", 1), ("push", 2), ("push", 3), ("push", 4),
    ("multipop", 2), ("push", 5), ("multipop", 3), ("pop",)
]

for op_data in operations:
    operation = op_data[0]
    args = op_data[1:] if len(op_data) > 1 else ()
    
    actual, amortized, phi_before, phi_after = stack.amortized_cost(operation, *args)
    
    op_str = f"{operation}({', '.join(map(str, args))})" if args else operation
    print(f"{op_str:<10} | {actual:^11} | {phi_before:^9} | {phi_after:^8} | {amortized:^14}")
```

## Practical Analysis Techniques

### Empirical Analysis

```python
import time
import random
import matplotlib.pyplot as plt
import numpy as np

def empirical_complexity_analysis():
    """Empirically analyze algorithm complexity"""
    
    def selection_sort(arr):
        """O(n²) sorting algorithm"""
        n = len(arr)
        for i in range(n):
            min_idx = i
            for j in range(i + 1, n):
                if arr[j] < arr[min_idx]:
                    min_idx = j
            arr[i], arr[min_idx] = arr[min_idx], arr[i]
        return arr
    
    def merge_sort(arr):
        """O(n log n) sorting algorithm"""
        if len(arr) <= 1:
            return arr
        
        mid = len(arr) // 2
        left = merge_sort(arr[:mid])
        right = merge_sort(arr[mid:])
        
        return merge(left, right)
    
    def merge(left, right):
        result = []
        i = j = 0
        
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                result.append(left[i])
                i += 1
            else:
                result.append(right[j])
                j += 1
        
        result.extend(left[i:])
        result.extend(right[j:])
        return result
    
    def benchmark_algorithm(algorithm, sizes, trials=3):
        """Benchmark an algorithm across different input sizes"""
        times = []
        
        for size in sizes:
            trial_times = []
            
            for _ in range(trials):
                # Generate random array
                arr = [random.randint(1, 1000) for _ in range(size)]
                
                # Measure execution time
                start_time = time.time()
                algorithm(arr.copy())
                end_time = time.time()
                
                trial_times.append(end_time - start_time)
            
            # Average time over trials
            avg_time = sum(trial_times) / len(trial_times)
            times.append(avg_time)
        
        return times
    
    # Test different input sizes
    sizes = [100, 200, 400, 800, 1600]
    
    print("Empirical Complexity Analysis")
    print("=" * 40)
    
    # Benchmark selection sort
    selection_times = benchmark_algorithm(selection_sort, sizes)
    print("\nSelection Sort (O(n²)):")
    for size, time_taken in zip(sizes, selection_times):
        print(f"n={size:4d}: {time_taken:.6f} seconds")
    
    # Benchmark merge sort
    merge_times = benchmark_algorithm(merge_sort, sizes)
    print("\nMerge Sort (O(n log n)):")
    for size, time_taken in zip(sizes, merge_times):
        print(f"n={size:4d}: {time_taken:.6f} seconds")
    
    # Analyze growth rates
    print("\nGrowth Rate Analysis:")
    print("Selection Sort ratios (should approach 4 for doubling input):")
    for i in range(1, len(sizes)):
        ratio = selection_times[i] / selection_times[i-1]
        input_ratio = sizes[i] / sizes[i-1]
        print(f"T({sizes[i]})/T({sizes[i-1]}) = {ratio:.2f} (input ratio: {input_ratio:.1f})")
    
    print("\nMerge Sort ratios (should approach ~2.1 for doubling input):")
    for i in range(1, len(sizes)):
        ratio = merge_times[i] / merge_times[i-1]
        input_ratio = sizes[i] / sizes[i-1]
        expected_ratio = input_ratio * math.log2(input_ratio)
        print(f"T({sizes[i]})/T({sizes[i-1]}) = {ratio:.2f} (expected: ~{expected_ratio:.1f})")

# Uncomment to run empirical analysis
# empirical_complexity_analysis()
```

### Profiling and Optimization

```python
import cProfile
import pstats
from functools import wraps
import time

def profile_decorator(func):
    """Decorator to profile function execution"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        profiler = cProfile.Profile()
        profiler.enable()
        
        result = func(*args, **kwargs)
        
        profiler.disable()
        
        # Print profiling results
        stats = pstats.Stats(profiler)
        stats.sort_stats('cumulative')
        print(f"\nProfiling results for {func.__name__}:")
        stats.print_stats(10)  # Show top 10 functions
        
        return result
    return wrapper

class AlgorithmProfiler:
    """Class for detailed algorithm profiling"""
    
    def __init__(self):
        self.timing_data = {}
    
    def time_function(self, func, *args, **kwargs):
        """Time a function execution"""
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        end_time = time.perf_counter()
        
        execution_time = end_time - start_time
        func_name = func.__name__
        
        if func_name not in self.timing_data:
            self.timing_data[func_name] = []
        self.timing_data[func_name].append(execution_time)
        
        return result, execution_time
    
    def compare_algorithms(self, algorithms, test_data):
        """Compare multiple algorithms on same data"""
        results = {}
        
        for name, algorithm in algorithms.items():
            print(f"\nTesting {name}...")
            result, exec_time = self.time_function(algorithm, test_data.copy())
            results[name] = {
                'time': exec_time,
                'result': result
            }
            print(f"{name}: {exec_time:.6f} seconds")
        
        # Find fastest algorithm
        fastest = min(results.keys(), key=lambda x: results[x]['time'])
        print(f"\nFastest algorithm: {fastest}")
        
        # Compare relative performance
        fastest_time = results[fastest]['time']
        print("\nRelative performance:")
        for name, data in results.items():
            slowdown = data['time'] / fastest_time
            print(f"{name}: {slowdown:.2f}x slower than fastest")
        
        return results
    
    def get_statistics(self):
        """Get timing statistics"""
        stats = {}
        for func_name, times in self.timing_data.items():
            if times:
                stats[func_name] = {
                    'count': len(times),
                    'total': sum(times),
                    'average': sum(times) / len(times),
                    'min': min(times),
                    'max': max(times)
                }
        return stats

# Example usage
@profile_decorator
def inefficient_prime_check(n):
    """Inefficient O(n) primality test"""
    if n < 2:
        return False
    for i in range(2, n):
        if n % i == 0:
            return False
    return True

def efficient_prime_check(n):
    """Efficient O(√n) primality test"""
    if n < 2:
        return False
    if n == 2:
        return True
    if n % 2 == 0:
        return False
    
    i = 3
    while i * i <= n:
        if n % i == 0:
            return False
        i += 2
    return True

# Compare prime checking algorithms
profiler = AlgorithmProfiler()
test_number = 10007  # A prime number

algorithms = {
    'Inefficient O(n)': inefficient_prime_check,
    'Efficient O(√n)': efficient_prime_check
}

print("Algorithm Comparison: Prime Checking")
print("=" * 40)
comparison_results = profiler.compare_algorithms(algorithms, test_number)

# Show detailed statistics
print("\nDetailed Statistics:")
stats = profiler.get_statistics()
for func_name, func_stats in stats.items():
    print(f"\n{func_name}:")
    for stat_name, value in func_stats.items():
        if stat_name in ['total', 'average', 'min', 'max']:
            print(f"  {stat_name}: {value:.6f} seconds")
        else:
            print(f"  {stat_name}: {value}")
```

## Complexity Classes

### P vs NP

```python
def complexity_class_examples():
    """Examples of different complexity classes"""
    
    # P: Polynomial time algorithms
    def matrix_multiplication(A, B):
        """O(n³) - Polynomial time"""
        n = len(A)
        C = [[0] * n for _ in range(n)]
        
        for i in range(n):
            for j in range(n):
                for k in range(n):
                    C[i][j] += A[i][k] * B[k][j]
        
        return C
    
    # NP: Nondeterministic polynomial time
    def subset_sum_verify(numbers, target, subset_indices):
        """Verify subset sum solution in O(n) time"""
        subset_sum = sum(numbers[i] for i in subset_indices if i < len(numbers))
        return subset_sum == target
    
    def subset_sum_brute_force(numbers, target):
        """Find subset sum solution in O(2ⁿ) time"""
        n = len(numbers)
        
        # Try all possible subsets (2ⁿ possibilities)
        for i in range(1 << n):  # 2ⁿ iterations
            subset = []
            subset_sum = 0
            
            for j in range(n):
                if i & (1 << j):  # If j-th bit is set
                    subset.append(j)
                    subset_sum += numbers[j]
            
            if subset_sum == target:
                return subset
        
        return None  # No solution found
    
    # Demonstrate the difference
    print("Complexity Class Examples")
    print("=" * 30)
    
    # P example: Matrix multiplication
    A = [[1, 2], [3, 4]]
    B = [[5, 6], [7, 8]]
    result = matrix_multiplication(A, B)
    print(f"Matrix multiplication result: {result}")
    
    # NP example: Subset sum
    numbers = [3, 34, 4, 12, 5, 2]
    target = 9
    
    # Finding solution (exponential time)
    solution = subset_sum_brute_force(numbers, target)
    print(f"Subset sum solution for target {target}: {solution}")
    
    # Verifying solution (polynomial time)
    if solution:
        is_valid = subset_sum_verify(numbers, target, solution)
        subset_values = [numbers[i] for i in solution]
        print(f"Verification: {subset_values} sums to {target}: {is_valid}")
    
    return matrix_multiplication, subset_sum_verify, subset_sum_brute_force

# Run examples
complexity_class_examples()
```

### Reductions and NP-Completeness

```python
def np_completeness_example():
    """Demonstrate polynomial reduction between NP-complete problems"""
    
    def three_sat_to_vertex_cover(clauses, num_variables):
        """Reduce 3-SAT to Vertex Cover problem"""
        # This is a simplified illustration of the concept
        # Full reduction is quite complex
        
        print("3-SAT to Vertex Cover Reduction (Simplified)")
        print("-" * 45)
        
        # Example 3-SAT instance
        print("3-SAT Instance:")
        for i, clause in enumerate(clauses):
            clause_str = " ∨ ".join([f"x{abs(lit)}" if lit > 0 else f"¬x{abs(lit)}" for lit in clause])
            print(f"C{i+1}: ({clause_str})")
        
        # Create vertex cover instance (simplified)
        # In actual reduction, we create gadgets for variables and clauses
        vertices = set()
        edges = []
        
        # Variable gadgets (simplified)
        for i in range(1, num_variables + 1):
            vertices.add(f"x{i}")
            vertices.add(f"¬x{i}")
            edges.append((f"x{i}", f"¬x{i}"))  # Must choose one
        
        # Clause gadgets (simplified)
        for i, clause in enumerate(clauses):
            clause_vertices = []
            for lit in clause:
                var_name = f"x{abs(lit)}" if lit > 0 else f"¬x{abs(lit)}"
                clause_vertex = f"c{i+1}_{var_name}"
                vertices.add(clause_vertex)
                clause_vertices.append(clause_vertex)
                # Connect to corresponding variable
                edges.append((clause_vertex, var_name))
            
            # Connect clause vertices to form triangle (simplified)
            for j in range(len(clause_vertices)):
                for k in range(j + 1, len(clause_vertices)):
                    edges.append((clause_vertices[j], clause_vertices[k]))
        
        print(f"\nVertex Cover Instance:")
        print(f"Vertices: {vertices}")
        print(f"Edges: {edges[:10]}...")  # Show first 10 edges
        print(f"Total vertices: {len(vertices)}")
        print(f"Total edges: {len(edges)}")
        
        return vertices, edges
    
    # Example 3-SAT instance
    # (x1 ∨ ¬x2 ∨ x3) ∧ (¬x1 ∨ x2 ∨ ¬x3) ∧ (x1 ∨ x2 ∨ x3)
    clauses = [
        [1, -2, 3],   # x1 ∨ ¬x2 ∨ x3
        [-1, 2, -3],  # ¬x1 ∨ x2 ∨ ¬x3
        [1, 2, 3]     # x1 ∨ x2 ∨ x3
    ]
    
    vertices, edges = three_sat_to_vertex_cover(clauses, 3)
    
    print(f"\nThis reduction shows that if we can solve Vertex Cover")
    print(f"efficiently, we can solve 3-SAT efficiently.")
    print(f"Since 3-SAT is NP-complete, this proves Vertex Cover is")
    print(f"also NP-complete (assuming the reduction is polynomial-time).")

# Run NP-completeness example
np_completeness_example()
```

---

*Algorithm analysis provides the mathematical tools to understand, predict, and optimize the performance of computational solutions, enabling informed decisions about algorithm design and system architecture.*
