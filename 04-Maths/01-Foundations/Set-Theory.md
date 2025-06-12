# Set Theory

**🏠 [Main](../../README.md)** | **🧮 [Mathematics](../README.md)** | **📐 [Foundations](../README.md)**

Set theory is the foundation of modern mathematics and provides the basic language for describing mathematical objects and relationships. It's essential for understanding data structures, databases, and formal reasoning in computer science.

## Overview

Set theory applications in computer science:
- **Data Structures**: Collections, arrays, hash tables, databases
- **Algorithm Analysis**: Complexity classes, decision problems
- **Database Systems**: Relational algebra, query operations
- **Programming Languages**: Type systems, collections frameworks
- **Logic and Verification**: Formal specifications, program correctness
- **Machine Learning**: Feature spaces, classification problems

## Basic Definitions

### Sets and Elements

**Set:**
A collection of distinct objects, called elements or members.

**Notation:**
- Set: A = {1, 2, 3, 4, 5}
- Element membership: x ∈ A (x is in A), x ∉ A (x is not in A)
- Empty set: ∅ or {}
- Set builder notation: {x | P(x)} (set of x such that P(x) is true)

```python
# Set representations in programming
# Python set
numbers = {1, 2, 3, 4, 5}

# Set comprehension
even_numbers = {x for x in range(10) if x % 2 == 0}

# Membership testing
print(3 in numbers)  # True
print(6 in numbers)  # False
```

### Set Equality and Subsets

**Set Equality:**
A = B if and only if every element of A is in B and every element of B is in A.

**Subset:**
A ⊆ B if every element of A is also in B.
A ⊂ B (proper subset) if A ⊆ B and A ≠ B.

```python
def is_subset(A, B):
    """Check if A is a subset of B"""
    return all(x in B for x in A)

def is_proper_subset(A, B):
    """Check if A is a proper subset of B"""
    return is_subset(A, B) and A != B

def sets_equal(A, B):
    """Check if two sets are equal"""
    return A == B  # In Python, this checks element equality

# Examples
A = {1, 2, 3}
B = {1, 2, 3, 4, 5}
C = {1, 2, 3}

print(f"A ⊆ B: {is_subset(A, B)}")  # True
print(f"A ⊂ B: {is_proper_subset(A, B)}")  # True
print(f"A = C: {sets_equal(A, C)}")  # True
```

## Set Operations

### Basic Operations

**Union (A ∪ B):**
Set of elements that are in A or B (or both).

**Intersection (A ∩ B):**
Set of elements that are in both A and B.

**Difference (A - B or A \ B):**
Set of elements that are in A but not in B.

**Complement (A^c or Ā):**
Set of elements not in A (relative to a universal set U).

```python
class SetOperations:
    def __init__(self, universal_set=None):
        self.universal_set = universal_set or set()
    
    def union(self, A, B):
        """A ∪ B"""
        return A | B  # Or A.union(B)
    
    def intersection(self, A, B):
        """A ∩ B"""
        return A & B  # Or A.intersection(B)
    
    def difference(self, A, B):
        """A - B"""
        return A - B  # Or A.difference(B)
    
    def complement(self, A):
        """A^c relative to universal set"""
        return self.universal_set - A
    
    def symmetric_difference(self, A, B):
        """A Δ B = (A - B) ∪ (B - A)"""
        return A ^ B  # Or A.symmetric_difference(B)

# Example usage
ops = SetOperations(universal_set={1, 2, 3, 4, 5, 6, 7, 8, 9, 10})
A = {1, 2, 3, 4}
B = {3, 4, 5, 6}

print(f"A ∪ B = {ops.union(A, B)}")  # {1, 2, 3, 4, 5, 6}
print(f"A ∩ B = {ops.intersection(A, B)}")  # {3, 4}
print(f"A - B = {ops.difference(A, B)}")  # {1, 2}
print(f"A^c = {ops.complement(A)}")  # {5, 6, 7, 8, 9, 10}
```

### Properties of Set Operations

**Commutative Laws:**
- A ∪ B = B ∪ A
- A ∩ B = B ∩ A

**Associative Laws:**
- (A ∪ B) ∪ C = A ∪ (B ∪ C)
- (A ∩ B) ∩ C = A ∩ (B ∩ C)

**Distributive Laws:**
- A ∩ (B ∪ C) = (A ∩ B) ∪ (A ∩ C)
- A ∪ (B ∩ C) = (A ∪ B) ∩ (A ∪ C)

**De Morgan's Laws:**
- (A ∪ B)^c = A^c ∩ B^c
- (A ∩ B)^c = A^c ∪ B^c

```python
def verify_de_morgan_laws(A, B, universal_set):
    """Verify De Morgan's Laws"""
    # First law: (A ∪ B)^c = A^c ∩ B^c
    left_side = universal_set - (A | B)
    right_side = (universal_set - A) & (universal_set - B)
    law1_holds = left_side == right_side
    
    # Second law: (A ∩ B)^c = A^c ∪ B^c
    left_side = universal_set - (A & B)
    right_side = (universal_set - A) | (universal_set - B)
    law2_holds = left_side == right_side
    
    return law1_holds, law2_holds

# Test De Morgan's Laws
U = {1, 2, 3, 4, 5, 6, 7, 8}
A = {1, 2, 3}
B = {3, 4, 5}

law1, law2 = verify_de_morgan_laws(A, B, U)
print(f"De Morgan's Law 1 holds: {law1}")
print(f"De Morgan's Law 2 holds: {law2}")
```

## Relations

### Binary Relations

**Definition:**
A binary relation R from set A to set B is a subset of the Cartesian product A × B.

**Notation:**
- (a, b) ∈ R or aRb means a is related to b
- Domain: {a ∈ A | ∃b ∈ B, (a, b) ∈ R}
- Range: {b ∈ B | ∃a ∈ A, (a, b) ∈ R}

```python
class BinaryRelation:
    def __init__(self, domain, codomain, relation_set):
        self.domain = domain
        self.codomain = codomain
        self.relation = relation_set
    
    def is_related(self, a, b):
        """Check if a is related to b"""
        return (a, b) in self.relation
    
    def get_domain(self):
        """Get the actual domain (elements that appear in relations)"""
        return {a for a, b in self.relation}
    
    def get_range(self):
        """Get the range (elements that are targets of relations)"""
        return {b for a, b in self.relation}
    
    def inverse(self):
        """Get the inverse relation"""
        return BinaryRelation(
            self.codomain, 
            self.domain, 
            {(b, a) for a, b in self.relation}
        )

# Example: "Less than" relation on integers
domain = {1, 2, 3, 4}
codomain = {1, 2, 3, 4}
less_than = {(1, 2), (1, 3), (1, 4), (2, 3), (2, 4), (3, 4)}

R = BinaryRelation(domain, codomain, less_than)
print(f"1 < 3: {R.is_related(1, 3)}")  # True
print(f"3 < 1: {R.is_related(3, 1)}")  # False
print(f"Domain: {R.get_domain()}")
print(f"Range: {R.get_range()}")
```

### Properties of Relations

**Reflexive:**
∀a ∈ A, (a, a) ∈ R

**Symmetric:**
∀a, b ∈ A, if (a, b) ∈ R then (b, a) ∈ R

**Transitive:**
∀a, b, c ∈ A, if (a, b) ∈ R and (b, c) ∈ R then (a, c) ∈ R

**Antisymmetric:**
∀a, b ∈ A, if (a, b) ∈ R and (b, a) ∈ R then a = b

```python
def check_relation_properties(relation, domain):
    """Check properties of a binary relation"""
    
    # Reflexive: every element related to itself
    reflexive = all((a, a) in relation for a in domain)
    
    # Symmetric: if (a,b) in R then (b,a) in R
    symmetric = all((b, a) in relation for a, b in relation)
    
    # Transitive: if (a,b) and (b,c) in R then (a,c) in R
    transitive = True
    for a, b in relation:
        for b2, c in relation:
            if b == b2 and (a, c) not in relation:
                transitive = False
                break
        if not transitive:
            break
    
    # Antisymmetric: if (a,b) and (b,a) in R then a = b
    antisymmetric = True
    for a, b in relation:
        if (b, a) in relation and a != b:
            antisymmetric = False
            break
    
    return {
        'reflexive': reflexive,
        'symmetric': symmetric,
        'transitive': transitive,
        'antisymmetric': antisymmetric
    }

# Example: Check properties of "less than or equal" relation
domain = {1, 2, 3}
leq_relation = {(1, 1), (1, 2), (1, 3), (2, 2), (2, 3), (3, 3)}

properties = check_relation_properties(leq_relation, domain)
print("Properties of ≤ relation:", properties)
```

### Equivalence Relations

**Definition:**
A relation that is reflexive, symmetric, and transitive.

**Equivalence Classes:**
For equivalence relation R on set A, the equivalence class of a is [a] = {b ∈ A | aRb}.

```python
def find_equivalence_classes(domain, relation):
    """Find equivalence classes for an equivalence relation"""
    # Verify it's an equivalence relation
    props = check_relation_properties(relation, domain)
    if not (props['reflexive'] and props['symmetric'] and props['transitive']):
        raise ValueError("Not an equivalence relation")
    
    classes = []
    remaining = set(domain)
    
    while remaining:
        # Pick any element and find its equivalence class
        a = remaining.pop()
        equiv_class = {a}
        
        # Find all elements equivalent to a
        for b in list(remaining):
            if (a, b) in relation:
                equiv_class.add(b)
                remaining.discard(b)
        
        classes.append(equiv_class)
    
    return classes

# Example: Modular arithmetic (mod 3) on {0, 1, 2, 3, 4, 5}
domain = {0, 1, 2, 3, 4, 5}
mod3_relation = {(a, b) for a in domain for b in domain if a % 3 == b % 3}

try:
    equiv_classes = find_equivalence_classes(domain, mod3_relation)
    print("Equivalence classes for mod 3:", equiv_classes)
except ValueError as e:
    print(f"Error: {e}")
```

## Functions

### Function Definition

**Function:**
A special type of relation where each element in the domain is related to exactly one element in the codomain.

**Notation:**
f: A → B means f is a function from A to B.

```python
class Function:
    def __init__(self, domain, codomain, mapping):
        self.domain = domain
        self.codomain = codomain
        self.mapping = mapping
        
        # Verify it's a valid function
        if not self._is_valid_function():
            raise ValueError("Invalid function: not all domain elements mapped uniquely")
    
    def _is_valid_function(self):
        """Check if mapping defines a valid function"""
        # Every domain element must be mapped
        if set(self.mapping.keys()) != self.domain:
            return False
        
        # All mapped values must be in codomain
        if not all(v in self.codomain for v in self.mapping.values()):
            return False
        
        return True
    
    def apply(self, x):
        """Apply function to input x"""
        if x not in self.domain:
            raise ValueError(f"{x} not in domain")
        return self.mapping[x]
    
    def image(self):
        """Get the image (range) of the function"""
        return set(self.mapping.values())
    
    def is_injective(self):
        """Check if function is one-to-one"""
        values = list(self.mapping.values())
        return len(values) == len(set(values))
    
    def is_surjective(self):
        """Check if function is onto"""
        return self.image() == self.codomain
    
    def is_bijective(self):
        """Check if function is bijective"""
        return self.is_injective() and self.is_surjective()

# Example: Square function on integers
domain = {1, 2, 3, 4}
codomain = {1, 4, 9, 16, 25}
square_mapping = {1: 1, 2: 4, 3: 9, 4: 16}

square_func = Function(domain, codomain, square_mapping)
print(f"f(3) = {square_func.apply(3)}")
print(f"Image: {square_func.image()}")
print(f"Injective: {square_func.is_injective()}")
print(f"Surjective: {square_func.is_surjective()}")
print(f"Bijective: {square_func.is_bijective()}")
```

### Function Composition

**Definition:**
For functions f: A → B and g: B → C, the composition g ∘ f: A → C is defined by (g ∘ f)(x) = g(f(x)).

```python
def compose_functions(f, g):
    """Compose two functions: g ∘ f"""
    # Check if composition is valid
    if f.image().issubset(g.domain):
        new_domain = f.domain
        new_codomain = g.codomain
        new_mapping = {x: g.apply(f.apply(x)) for x in f.domain}
        return Function(new_domain, new_codomain, new_mapping)
    else:
        raise ValueError("Function composition not valid: f's image not subset of g's domain")

# Example: Compose f(x) = x + 1 and g(x) = 2x
domain1 = {1, 2, 3}
codomain1 = {2, 3, 4}
f_mapping = {1: 2, 2: 3, 3: 4}
f = Function(domain1, codomain1, f_mapping)

domain2 = {2, 3, 4}
codomain2 = {4, 6, 8}
g_mapping = {2: 4, 3: 6, 4: 8}
g = Function(domain2, codomain2, g_mapping)

try:
    h = compose_functions(f, g)  # h = g ∘ f
    print("Composition g ∘ f:")
    for x in h.domain:
        print(f"h({x}) = {h.apply(x)}")
except ValueError as e:
    print(f"Error: {e}")
```

## Cardinality

### Finite Sets

**Cardinality:**
The number of elements in a set, denoted |A| or #A.

```python
def cardinality(A):
    """Get cardinality of a finite set"""
    return len(A)

def cartesian_product(A, B):
    """Compute A × B"""
    return {(a, b) for a in A for b in B}

def power_set(A):
    """Compute the power set P(A)"""
    from itertools import combinations
    
    elements = list(A)
    power_set_list = []
    
    for r in range(len(elements) + 1):
        for subset in combinations(elements, r):
            power_set_list.append(set(subset))
    
    return power_set_list

# Examples
A = {1, 2, 3}
B = {4, 5}

print(f"|A| = {cardinality(A)}")
print(f"|B| = {cardinality(B)}")

cart_prod = cartesian_product(A, B)
print(f"A × B = {cart_prod}")
print(f"|A × B| = {cardinality(cart_prod)}")

power_A = power_set(A)
print(f"P(A) = {power_A}")
print(f"|P(A)| = {len(power_A)}")  # Should be 2^|A| = 2^3 = 8
```

### Infinite Sets

**Countable Sets:**
Sets that can be put in one-to-one correspondence with natural numbers.

**Uncountable Sets:**
Sets that are infinite but not countable (like real numbers).

```python
# Example: Showing rationals are countable (Cantor's enumeration)
def enumerate_rationals(limit=10):
    """Enumerate positive rationals using Cantor's diagonal method"""
    rationals = []
    seen = set()
    
    for diagonal in range(1, limit + 1):
        for numerator in range(1, diagonal):
            denominator = diagonal - numerator
            if denominator > 0:
                # Reduce fraction to lowest terms
                from math import gcd
                g = gcd(numerator, denominator)
                reduced_num = numerator // g
                reduced_den = denominator // g
                
                if (reduced_num, reduced_den) not in seen:
                    rationals.append((reduced_num, reduced_den))
                    seen.add((reduced_num, reduced_den))
    
    return rationals

rationals = enumerate_rationals(8)
print("First few positive rationals in Cantor enumeration:")
for i, (num, den) in enumerate(rationals[:15]):
    print(f"{i+1}: {num}/{den}")
```

## Applications in Computer Science

### Database Operations

```python
class RelationalDatabase:
    def __init__(self):
        self.tables = {}
    
    def create_table(self, name, columns, rows):
        """Create a table as a set of tuples"""
        self.tables[name] = {tuple(row) for row in rows}
    
    def select(self, table_name, condition):
        """Select rows satisfying condition"""
        table = self.tables[table_name]
        return {row for row in table if condition(row)}
    
    def project(self, table_name, column_indices):
        """Project specific columns"""
        table = self.tables[table_name]
        return {tuple(row[i] for i in column_indices) for row in table}
    
    def union(self, table1_name, table2_name):
        """Union of two tables"""
        return self.tables[table1_name] | self.tables[table2_name]
    
    def intersection(self, table1_name, table2_name):
        """Intersection of two tables"""
        return self.tables[table1_name] & self.tables[table2_name]
    
    def difference(self, table1_name, table2_name):
        """Difference of two tables"""
        return self.tables[table1_name] - self.tables[table2_name]

# Example usage
db = RelationalDatabase()

# Create employee table: (ID, Name, Department)
employees = [
    (1, "Alice", "Engineering"),
    (2, "Bob", "Marketing"),
    (3, "Charlie", "Engineering"),
    (4, "Diana", "Sales")
]

db.create_table("employees", ["id", "name", "department"], employees)

# Select engineers
engineers = db.select("employees", lambda row: row[2] == "Engineering")
print("Engineers:", engineers)

# Project names only
names = db.project("employees", [1])
print("Names:", names)
```

### Type Systems

```python
class TypeSystem:
    def __init__(self):
        self.types = set()
        self.subtype_relation = set()
    
    def add_type(self, type_name):
        """Add a type to the system"""
        self.types.add(type_name)
    
    def add_subtype(self, subtype, supertype):
        """Add subtype relationship"""
        if subtype in self.types and supertype in self.types:
            self.subtype_relation.add((subtype, supertype))
    
    def is_subtype(self, t1, t2):
        """Check if t1 is a subtype of t2"""
        # Reflexive: every type is subtype of itself
        if t1 == t2:
            return True
        
        # Direct subtype
        if (t1, t2) in self.subtype_relation:
            return True
        
        # Transitive: check indirect subtype relationships
        for intermediate in self.types:
            if ((t1, intermediate) in self.subtype_relation and 
                self.is_subtype(intermediate, t2)):
                return True
        
        return False
    
    def find_common_supertype(self, t1, t2):
        """Find least common supertype"""
        common_supertypes = []
        for t in self.types:
            if self.is_subtype(t1, t) and self.is_subtype(t2, t):
                common_supertypes.append(t)
        
        # Find minimal elements (no other common supertype is subtype of it)
        minimal = []
        for t in common_supertypes:
            is_minimal = True
            for other in common_supertypes:
                if other != t and self.is_subtype(other, t):
                    is_minimal = False
                    break
            if is_minimal:
                minimal.append(t)
        
        return minimal

# Example: Simple type hierarchy
ts = TypeSystem()
types = ["Object", "Number", "Integer", "Real", "String"]
for t in types:
    ts.add_type(t)

# Add subtype relationships
ts.add_subtype("Number", "Object")
ts.add_subtype("String", "Object")
ts.add_subtype("Integer", "Number")
ts.add_subtype("Real", "Number")

print(f"Integer <: Number: {ts.is_subtype('Integer', 'Number')}")
print(f"Integer <: Object: {ts.is_subtype('Integer', 'Object')}")
print(f"String <: Number: {ts.is_subtype('String', 'Number')}")

common = ts.find_common_supertype("Integer", "String")
print(f"Common supertype of Integer and String: {common}")
```

## Common Pitfalls and Best Practices

### Set Equality vs Identity

```python
# Sets with same elements are equal even if created differently
A = {1, 2, 3}
B = {3, 2, 1}  # Order doesn't matter
C = {x for x in range(1, 4)}  # Set comprehension

print(f"A == B: {A == B}")  # True
print(f"A == C: {A == C}")  # True
print(f"A is B: {A is B}")  # False (different objects)

# Sets automatically eliminate duplicates
D = {1, 1, 2, 2, 3, 3}
print(f"D = {D}")  # {1, 2, 3}
```

### Mutable vs Immutable Sets

```python
# Regular sets are mutable
mutable_set = {1, 2, 3}
mutable_set.add(4)
print(f"After adding 4: {mutable_set}")

# Frozensets are immutable (can be elements of other sets)
frozen_set = frozenset([1, 2, 3])
# frozen_set.add(4)  # This would raise an error

# Using frozensets as set elements
set_of_sets = {frozenset([1, 2]), frozenset([2, 3]), frozenset([1, 3])}
print(f"Set of sets: {set_of_sets}")
```

### Performance Considerations

```python
import time

# Set membership is O(1) average case
large_set = set(range(1000000))
large_list = list(range(1000000))

# Time set lookup
start = time.time()
result = 999999 in large_set
set_time = time.time() - start

# Time list lookup
start = time.time()
result = 999999 in large_list
list_time = time.time() - start

print(f"Set lookup time: {set_time:.6f} seconds")
print(f"List lookup time: {list_time:.6f} seconds")
print(f"Set is {list_time/set_time:.1f}x faster")
```

---

*Set theory provides the mathematical foundation for understanding collections, relationships, and functions—core concepts in computer science and software engineering.*
