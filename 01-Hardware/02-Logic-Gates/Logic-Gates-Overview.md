# Logic Gates

**🏠 [Main](../../README.md)** | **🔧 [Hardware](../README.md)** | **⚡ [Basic Components](../01-Basic-Components/)** | **🖥️ [Processors](../03-Processors/)**

## Overview
Logic gates are the fundamental building blocks of digital circuits. They perform basic logical operations on binary inputs (0 and 1) to produce binary outputs.

## Basic Logic Gates

### AND Gate
- **Function**: Output is 1 only when ALL inputs are 1
- **Symbol**: D-shaped with flat input side
- **Truth Table**:
  ```
  A | B | Output
  0 | 0 |   0
  0 | 1 |   0
  1 | 0 |   0
  1 | 1 |   1
  ```

### OR Gate
- **Function**: Output is 1 when ANY input is 1
- **Symbol**: Curved input side, pointed output
- **Truth Table**:
  ```
  A | B | Output
  0 | 0 |   0
  0 | 1 |   1
  1 | 0 |   1
  1 | 1 |   1
  ```

### NOT Gate (Inverter)
- **Function**: Output is opposite of input
- **Symbol**: Triangle with circle at output
- **Truth Table**:
  ```
  A | Output
  0 |   1
  1 |   0
  ```

## Derived Logic Gates

### NAND Gate
- **Function**: NOT AND - Output is 0 only when ALL inputs are 1
- **Importance**: Universal gate - can create any other gate
- **Truth Table**:
  ```
  A | B | Output
  0 | 0 |   1
  0 | 1 |   1
  1 | 0 |   1
  1 | 1 |   0
  ```

### NOR Gate
- **Function**: NOT OR - Output is 1 only when ALL inputs are 0
- **Importance**: Also a universal gate
- **Truth Table**:
  ```
  A | B | Output
  0 | 0 |   1
  0 | 1 |   0
  1 | 0 |   0
  1 | 1 |   0
  ```

### XOR Gate (Exclusive OR)
- **Function**: Output is 1 when inputs are different
- **Uses**: Binary addition, comparison circuits
- **Truth Table**:
  ```
  A | B | Output
  0 | 0 |   0
  0 | 1 |   1
  1 | 0 |   1
  1 | 1 |   0
  ```

## Implementation
- **Transistor Level**: Gates built from combinations of transistors
- **IC Packages**: Multiple gates in single integrated circuits
- **Logic Families**: TTL, CMOS, ECL with different characteristics

## Applications in Computing
- **ALU (Arithmetic Logic Unit)**: Performs calculations using gate combinations
- **Memory Circuits**: Store and retrieve binary data
- **Control Logic**: Manage processor operations
- **Address Decoding**: Select memory locations

## Boolean Algebra
- Mathematical foundation for logic gate operations
- Rules for simplifying logic expressions
- De Morgan's Laws for converting between gate types
- Karnaugh Maps for optimization

## Practical Considerations
- **Propagation Delay**: Time for output to change after input change
- **Fan-out**: Number of inputs a gate output can drive
- **Power Consumption**: Energy used by gate operation
- **Noise Immunity**: Resistance to electrical interference
