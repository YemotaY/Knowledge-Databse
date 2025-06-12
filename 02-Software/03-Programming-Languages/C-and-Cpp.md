# C and C++ Programming Languages

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🔤 [Programming Languages](README.md)** | **🐍 [Python](Python-Ecosystem.md)** | **⚙️ [Assembly](../01-Assembly-Language/)**

## Overview
C and C++ are foundational programming languages that have shaped modern computing. C provides system-level programming capabilities with minimal overhead, while C++ extends C with object-oriented features and high-level abstractions without sacrificing performance.

## Table of Contents
- [C Language Fundamentals](#c-language-fundamentals)
- [C++ Language Features](#c-language-features)
- [Memory Management](#memory-management)
- [Object-Oriented Programming in C++](#object-oriented-programming-in-c)
- [Standard Libraries](#standard-libraries)
- [Performance and Optimization](#performance-and-optimization)
- [Modern C++ Features](#modern-c-features)
- [Applications and Use Cases](#applications-and-use-cases)

## C Language Fundamentals

### History and Philosophy
**Creation**: Developed by Dennis Ritchie at Bell Labs (1969-1973)
**Purpose**: System programming language for Unix
**Philosophy**: Simple, efficient, close to hardware
**Influence**: Foundation for many subsequent languages

### Key Characteristics
**Procedural Programming**: Function-based program organization
**Manual Memory Management**: Explicit allocation and deallocation
**Minimal Runtime**: Small runtime overhead
**Portable**: Code runs on many different platforms
**Low-Level Access**: Direct hardware and memory manipulation

### Basic Syntax and Features
```c
#include <stdio.h>

int main() {
    printf("Hello, World!\n");
    return 0;
}
```

**Data Types**:
- **Basic Types**: `int`, `char`, `float`, `double`
- **Modifiers**: `signed`, `unsigned`, `short`, `long`
- **Derived Types**: Arrays, pointers, structures, unions
- **Size Flexibility**: Platform-dependent sizing

**Control Structures**:
- **Conditionals**: `if`, `else if`, `else`, `switch`
- **Loops**: `for`, `while`, `do-while`
- **Jumps**: `break`, `continue`, `goto`, `return`

### Pointers and Memory
**Pointer Basics**:
```c
int x = 42;
int *ptr = &x;  // ptr points to x
int value = *ptr;  // Dereference to get value
```

**Pointer Arithmetic**:
```c
int arr[5] = {1, 2, 3, 4, 5};
int *p = arr;  // Points to first element
p++;  // Now points to second element
```

**Dynamic Memory Allocation**:
```c
int *buffer = malloc(100 * sizeof(int));
// Use buffer...
free(buffer);  // Must free manually
```

### Structures and Data Organization
```c
struct Person {
    char name[50];
    int age;
    float height;
};

struct Person person1;
strcpy(person1.name, "John");
person1.age = 30;
```

## C++ Language Features

### History and Evolution
**Creation**: Bjarne Stroustrup at Bell Labs (1979-1985)
**Original Name**: "C with Classes"
**Philosophy**: Zero-overhead abstraction
**Evolution**: C++98, C++03, C++11, C++14, C++17, C++20, C++23

### Object-Oriented Programming
**Classes and Objects**:
```cpp
class Rectangle {
private:
    double width, height;
    
public:
    Rectangle(double w, double h) : width(w), height(h) {}
    
    double area() const {
        return width * height;
    }
    
    double getWidth() const { return width; }
    double getHeight() const { return height; }
};
```

**Inheritance**:
```cpp
class Shape {
public:
    virtual double area() const = 0;  // Pure virtual function
    virtual ~Shape() = default;       // Virtual destructor
};

class Circle : public Shape {
private:
    double radius;
    
public:
    Circle(double r) : radius(r) {}
    
    double area() const override {
        return 3.14159 * radius * radius;
    }
};
```

**Polymorphism**:
```cpp
void printArea(const Shape& shape) {
    std::cout << "Area: " << shape.area() << std::endl;
}

Circle circle(5.0);
Rectangle rect(4.0, 6.0);
printArea(circle);  // Calls Circle::area()
printArea(rect);    // Calls Rectangle::area()
```

### Templates and Generic Programming
**Function Templates**:
```cpp
template<typename T>
T maximum(T a, T b) {
    return (a > b) ? a : b;
}

int max_int = maximum(10, 20);
double max_double = maximum(3.14, 2.71);
```

**Class Templates**:
```cpp
template<typename T>
class Vector {
private:
    T* data;
    size_t size;
    size_t capacity;
    
public:
    Vector() : data(nullptr), size(0), capacity(0) {}
    
    void push_back(const T& value) {
        // Implementation...
    }
    
    T& operator[](size_t index) {
        return data[index];
    }
};

Vector<int> intVector;
Vector<std::string> stringVector;
```

## Memory Management

### Manual Memory Management in C
**Stack Allocation**:
- **Automatic**: Variables declared in functions
- **Fast**: Allocation/deallocation very fast
- **Limited**: Stack size limitations
- **Scope-Based**: Automatically cleaned up

**Heap Allocation**:
```c
// C style
int *ptr = malloc(sizeof(int) * 100);
free(ptr);

// Must check for allocation failure
if (ptr == NULL) {
    // Handle allocation failure
}
```

### C++ Memory Management
**RAII (Resource Acquisition Is Initialization)**:
```cpp
class FileWrapper {
private:
    FILE* file;
    
public:
    FileWrapper(const char* filename) {
        file = fopen(filename, "r");
        if (!file) throw std::runtime_error("File open failed");
    }
    
    ~FileWrapper() {
        if (file) fclose(file);
    }
    
    // Disable copying to prevent double-free
    FileWrapper(const FileWrapper&) = delete;
    FileWrapper& operator=(const FileWrapper&) = delete;
};
```

**Smart Pointers (C++11+)**:
```cpp
#include <memory>

// Unique ownership
std::unique_ptr<int> ptr1 = std::make_unique<int>(42);

// Shared ownership
std::shared_ptr<int> ptr2 = std::make_shared<int>(100);
std::shared_ptr<int> ptr3 = ptr2;  // Reference count now 2

// Weak reference (doesn't affect reference count)
std::weak_ptr<int> weak_ptr = ptr2;
```

### Memory Safety Considerations
**Common Issues**:
- **Buffer Overflows**: Writing past array bounds
- **Use After Free**: Accessing freed memory
- **Memory Leaks**: Forgetting to free allocated memory
- **Double Free**: Freeing same memory twice

**Best Practices**:
- **Bounds Checking**: Always verify array indices
- **Initialization**: Initialize all variables
- **Const Correctness**: Use `const` where possible
- **RAII**: Use constructors/destructors for resource management

## Object-Oriented Programming in C++

### Encapsulation
**Access Specifiers**:
- **private**: Accessible only within the class
- **protected**: Accessible within class and derived classes
- **public**: Accessible from anywhere

**Data Hiding**:
```cpp
class BankAccount {
private:
    double balance;  // Hidden implementation detail
    
public:
    void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    bool withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
            return true;
        }
        return false;
    }
    
    double getBalance() const { return balance; }
};
```

### Inheritance and Polymorphism
**Virtual Functions**:
```cpp
class Animal {
public:
    virtual void makeSound() const {
        std::cout << "Some generic animal sound" << std::endl;
    }
    
    virtual ~Animal() = default;  // Virtual destructor
};

class Dog : public Animal {
public:
    void makeSound() const override {
        std::cout << "Woof!" << std::endl;
    }
};

class Cat : public Animal {
public:
    void makeSound() const override {
        std::cout << "Meow!" << std::endl;
    }
};
```

**Abstract Classes**:
```cpp
class Drawable {
public:
    virtual void draw() const = 0;  // Pure virtual function
    virtual ~Drawable() = default;
};

class Button : public Drawable {
public:
    void draw() const override {
        // Draw button implementation
    }
};
```

## Standard Libraries

### C Standard Library
**Core Headers**:
- **stdio.h**: Input/output functions
- **stdlib.h**: General utilities (malloc, free, etc.)
- **string.h**: String manipulation functions
- **math.h**: Mathematical functions
- **time.h**: Date and time functions

**Common Functions**:
```c
// String functions
strlen(str);           // String length
strcpy(dest, src);     // String copy
strcat(dest, src);     // String concatenation
strcmp(str1, str2);    // String comparison

// Memory functions
memset(ptr, value, size);     // Set memory
memcpy(dest, src, size);      // Copy memory
memcmp(ptr1, ptr2, size);     // Compare memory
```

### C++ Standard Library (STL)
**Containers**:
```cpp
#include <vector>
#include <list>
#include <map>
#include <set>

std::vector<int> vec = {1, 2, 3, 4, 5};
std::map<std::string, int> ages;
ages["Alice"] = 30;
ages["Bob"] = 25;

std::set<int> unique_numbers = {3, 1, 4, 1, 5, 9};  // Duplicates removed
```

**Algorithms**:
```cpp
#include <algorithm>

std::vector<int> vec = {3, 1, 4, 1, 5, 9};

// Sorting
std::sort(vec.begin(), vec.end());

// Searching
auto it = std::find(vec.begin(), vec.end(), 4);
if (it != vec.end()) {
    std::cout << "Found: " << *it << std::endl;
}

// Transforming
std::transform(vec.begin(), vec.end(), vec.begin(), 
               [](int x) { return x * 2; });
```

**Iterators**:
```cpp
std::vector<int> vec = {1, 2, 3, 4, 5};

// Range-based for loop (C++11)
for (const auto& element : vec) {
    std::cout << element << " ";
}

// Iterator-based loop
for (auto it = vec.begin(); it != vec.end(); ++it) {
    std::cout << *it << " ";
}
```

## Performance and Optimization

### Performance Characteristics
**C Performance**:
- **Minimal Overhead**: Close to assembly language performance
- **Predictable**: No hidden costs or garbage collection
- **Optimizable**: Compilers can heavily optimize
- **Control**: Precise control over memory layout and access

**C++ Performance**:
- **Zero-Cost Abstractions**: High-level features with no runtime cost
- **Template Optimization**: Compile-time code generation
- **Inline Functions**: Function call overhead elimination
- **Move Semantics**: Efficient resource transfer (C++11+)

### Optimization Techniques
**Compiler Optimizations**:
```bash
# GCC/Clang optimization levels
gcc -O0  # No optimization (debug)
gcc -O1  # Basic optimizations
gcc -O2  # Standard optimizations
gcc -O3  # Aggressive optimizations
gcc -Os  # Size optimization
```

**Code-Level Optimizations**:
```cpp
// Use const for compiler optimizations
void processArray(const std::vector<int>& data) {
    // Compiler knows data won't change
}

// Move semantics for efficiency
std::vector<int> createLargeVector() {
    std::vector<int> result(1000000);
    // Fill vector...
    return result;  // Move, not copy
}

// Inline for small functions
inline int square(int x) {
    return x * x;
}
```

### Memory Optimization
**Data Structure Layout**:
```cpp
// Poor cache performance
struct BadLayout {
    char a;      // 1 byte
    double b;    // 8 bytes (likely 7 bytes padding)
    char c;      // 1 byte
    double d;    // 8 bytes (likely 7 bytes padding)
};

// Better cache performance
struct GoodLayout {
    double b;    // 8 bytes
    double d;    // 8 bytes
    char a;      // 1 byte
    char c;      // 1 byte (6 bytes padding at end)
};
```

## Modern C++ Features

### C++11 Features
**Auto Keyword**:
```cpp
auto x = 42;                    // int
auto y = 3.14;                  // double
auto z = std::make_shared<int>(10);  // std::shared_ptr<int>
```

**Range-Based For Loops**:
```cpp
std::vector<int> vec = {1, 2, 3, 4, 5};
for (const auto& element : vec) {
    std::cout << element << std::endl;
}
```

**Lambda Expressions**:
```cpp
auto lambda = [](int x, int y) -> int {
    return x + y;
};

std::vector<int> vec = {3, 1, 4, 1, 5};
std::sort(vec.begin(), vec.end(), [](int a, int b) {
    return a > b;  // Sort in descending order
});
```

**Move Semantics**:
```cpp
class MyString {
private:
    char* data;
    size_t length;
    
public:
    // Move constructor
    MyString(MyString&& other) noexcept 
        : data(other.data), length(other.length) {
        other.data = nullptr;
        other.length = 0;
    }
    
    // Move assignment operator
    MyString& operator=(MyString&& other) noexcept {
        if (this != &other) {
            delete[] data;
            data = other.data;
            length = other.length;
            other.data = nullptr;
            other.length = 0;
        }
        return *this;
    }
};
```

### C++14 and Later Features
**Generic Lambdas (C++14)**:
```cpp
auto lambda = [](auto x, auto y) {
    return x + y;
};

int result1 = lambda(1, 2);           // int + int
double result2 = lambda(1.5, 2.5);   // double + double
```

**Structured Bindings (C++17)**:
```cpp
std::map<std::string, int> ages = {{"Alice", 30}, {"Bob", 25}};

for (const auto& [name, age] : ages) {
    std::cout << name << " is " << age << " years old" << std::endl;
}
```

**Concepts (C++20)**:
```cpp
#include <concepts>

template<typename T>
concept Numeric = std::integral<T> || std::floating_point<T>;

template<Numeric T>
T add(T a, T b) {
    return a + b;
}
```

## Applications and Use Cases

### System Programming
**Operating Systems**: Linux kernel, Windows components
**Device Drivers**: Hardware interface software
**Embedded Systems**: Microcontroller programming
**Real-Time Systems**: Deterministic timing requirements

### Application Development
**Desktop Applications**: GUI applications, productivity software
**Games**: High-performance game engines
**Databases**: Core database management systems
**Compilers**: Language implementation tools

### Performance-Critical Applications
**Scientific Computing**: Numerical simulations and modeling
**Financial Systems**: High-frequency trading systems
**Graphics and Multimedia**: Image/video processing
**Network Programming**: Servers and network protocols

### Modern C++ Applications
**Web Backends**: High-performance web servers
**Machine Learning**: Core libraries and frameworks
**Blockchain**: Cryptocurrency implementations
**IoT**: Internet of Things device programming

## Best Practices and Guidelines

### Code Organization
**Header Files**: Declarations and interfaces
**Source Files**: Implementation details
**Namespaces**: Organize related functionality
**Include Guards**: Prevent multiple inclusion

### Error Handling
**C Error Handling**: Return codes and errno
**C++ Exceptions**: RAII-based resource management
**Error Codes vs Exceptions**: When to use each approach
**Defensive Programming**: Validate inputs and assumptions

### Code Style and Standards
**Naming Conventions**: Consistent naming schemes
**const Correctness**: Use const wherever possible
**RAII**: Resource management through constructors/destructors
**Modern C++**: Use modern language features appropriately

The C and C++ languages remain fundamental to modern computing, providing the performance and control needed for system programming while offering increasingly powerful abstractions for application development. Their influence on programming language design and their continued relevance in performance-critical applications make them essential knowledge for serious programmers.
