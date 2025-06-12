# Instruction Sets

**🏠 [Back to Main](../../README.md)** | **🔧 [Hardware Section](../README.md)** | **🖥️ [Processors Section](README.md)** | **🏗️ [CPU Architecture](CPU-Architecture.md)** | **💾 [Memory](../04-Memory/)**

## Overview
An instruction set architecture (ISA) defines the interface between software and hardware, specifying the instructions a processor can execute, their formats, and their behavior.

## RISC vs CISC Architecture

### RISC (Reduced Instruction Set Computing)
**Philosophy**: Simple instructions that execute quickly

**Characteristics**:
- **Fixed Instruction Length**: All instructions same size (e.g., 32 bits)
- **Simple Addressing Modes**: Limited ways to specify operands
- **Load/Store Architecture**: Only load/store instructions access memory
- **Large Register Set**: Many general-purpose registers
- **Simple Instructions**: Each instruction does one simple operation

**Advantages**:
- **Pipeline Friendly**: Regular instruction format aids pipelining
- **High Clock Speeds**: Simple instructions execute faster
- **Efficient Compilers**: Easier to optimize for regular architecture
- **Lower Power**: Simpler decode logic

**Examples**: ARM, RISC-V, PowerPC, MIPS, SPARC

### CISC (Complex Instruction Set Computing)
**Philosophy**: Complex instructions that do more work per instruction

**Characteristics**:
- **Variable Instruction Length**: Instructions can be different sizes
- **Complex Addressing Modes**: Many ways to specify operands
- **Memory-to-Memory Operations**: Can operate directly on memory
- **Fewer Registers**: Historically smaller register sets
- **Complex Instructions**: Single instructions can do multiple operations

**Advantages**:
- **Code Density**: Programs require fewer instructions
- **Backward Compatibility**: Can maintain compatibility across generations
- **Powerful Instructions**: Complex operations in hardware

**Examples**: x86/x64, VAX, Motorola 68k

## Major Instruction Set Architectures

### x86/x64 (Intel/AMD)
**History**: Evolved from 8086 (1978) to modern 64-bit x64

**Key Features**:
- **CISC Architecture**: Complex, variable-length instructions
- **Backward Compatibility**: 64-bit processors run 16/32-bit code
- **Extensions**: MMX, SSE, AVX for parallel operations
- **Microcode**: Complex instructions broken into micro-operations

**Register Set**:
- **General Purpose**: EAX, EBX, ECX, EDX, ESI, EDI (32-bit)
- **64-bit Extension**: RAX, RBX, etc., plus R8-R15
- **Special Purpose**: ESP (stack), EBP (base), EIP (instruction pointer)

**Addressing Modes**:
- **Immediate**: MOV EAX, 42
- **Register**: MOV EAX, EBX
- **Memory**: MOV EAX, [EBX + 8]
- **Scaled Index**: MOV EAX, [EBX + ECX*4 + 8]

### ARM (Advanced RISC Machines)
**History**: Originally Acorn RISC Machine, now dominant in mobile

**Key Features**:
- **RISC Architecture**: Fixed 32-bit instructions (ARM) or 16/32-bit (Thumb)
- **Conditional Execution**: Most instructions can be conditionally executed
- **Barrel Shifter**: Operands can be shifted as part of instruction
- **Low Power**: Designed for energy efficiency

**ARM Versions**:
- **ARMv7**: 32-bit architecture (Cortex-A series)
- **ARMv8**: 64-bit architecture (AArch64/ARM64)
- **Thumb**: 16-bit instruction subset for code density

**Register Set**:
- **General Purpose**: R0-R12 (user mode)
- **Special Purpose**: R13 (SP), R14 (LR), R15 (PC)
- **CPSR**: Current Program Status Register

### RISC-V
**Philosophy**: Open-source instruction set architecture

**Key Features**:
- **Modular Design**: Base ISA plus optional extensions
- **Open Standard**: No licensing fees, fully open specification
- **Clean Design**: Modern RISC principles without legacy baggage
- **Scalable**: From microcontrollers to supercomputers

**Base ISAs**:
- **RV32I**: 32-bit base integer instruction set
- **RV64I**: 64-bit base integer instruction set
- **RV128I**: 128-bit base (future)

**Extensions**:
- **M**: Integer multiplication and division
- **A**: Atomic instructions
- **F**: Single-precision floating point
- **D**: Double-precision floating point
- **C**: Compressed instructions (16-bit)

## Instruction Categories

### Data Movement
- **Load**: Move data from memory to register
- **Store**: Move data from register to memory
- **Move**: Copy data between registers
- **Exchange**: Swap data between locations

### Arithmetic Operations
- **Addition/Subtraction**: ADD, SUB, ADC (with carry)
- **Multiplication/Division**: MUL, DIV, MOD
- **Increment/Decrement**: INC, DEC
- **Comparison**: CMP, TEST

### Logical Operations
- **Bitwise**: AND, OR, XOR, NOT
- **Shift/Rotate**: SHL, SHR, ROL, ROR
- **Bit Manipulation**: Set, clear, test specific bits

### Control Flow
- **Unconditional Jump**: JMP, CALL, RET
- **Conditional Branch**: JE, JNE, JG, JL (jump if equal, not equal, etc.)
- **Loop Control**: LOOP, REP prefixes

### System Control
- **Privileged Instructions**: Kernel-only operations
- **Interrupt Handling**: INT, IRET
- **Memory Management**: Page table manipulation
- **I/O Operations**: IN, OUT (x86)

## Addressing Modes

### Immediate Addressing
- **Operand**: Constant value in instruction
- **Example**: MOV EAX, 42
- **Use**: Loading constants, loop counters

### Register Addressing
- **Operand**: Value in register
- **Example**: MOV EAX, EBX
- **Use**: Fast register-to-register operations

### Direct Addressing
- **Operand**: Memory address specified directly
- **Example**: MOV EAX, [1000h]
- **Use**: Global variables, absolute addresses

### Indirect Addressing
- **Operand**: Address stored in register/memory
- **Example**: MOV EAX, [EBX]
- **Use**: Pointers, arrays, dynamic addressing

### Indexed Addressing
- **Operand**: Base address plus offset
- **Example**: MOV EAX, [EBX + 4]
- **Use**: Array elements, structure fields

### Scaled Index Addressing
- **Operand**: Base + Index*Scale + Displacement
- **Example**: MOV EAX, [EBX + ECX*4 + 8]
- **Use**: Multi-dimensional arrays, complex data structures

## Assembly Language Basics

### Assembly Structure
```assembly
section .data          ; Data section
    msg db 'Hello', 0  ; Define byte string

section .text          ; Code section
    global _start      ; Entry point

_start:
    mov eax, 4         ; System call number
    mov ebx, 1         ; File descriptor (stdout)
    mov ecx, msg       ; Message to write
    mov edx, 5         ; Message length
    int 0x80           ; System call
```

### Common Instructions (x86)
- **MOV**: Move data between registers/memory
- **ADD/SUB**: Arithmetic operations
- **CMP**: Compare two values
- **JMP/JE/JNE**: Control flow
- **PUSH/POP**: Stack operations
- **CALL/RET**: Function calls

### Calling Conventions
Different ways to pass parameters and return values:
- **x86 cdecl**: Parameters on stack, caller cleans up
- **x86 stdcall**: Parameters on stack, callee cleans up
- **x64 System V**: Parameters in registers (RDI, RSI, RDX, RCX, R8, R9)
- **x64 Microsoft**: Parameters in registers (RCX, RDX, R8, R9)

## ISA Design Considerations

### Instruction Encoding
- **Fixed vs Variable**: Trade-off between simplicity and code density
- **Opcode Length**: How many bits for operation code
- **Operand Specification**: How to encode register/memory addresses
- **Immediate Values**: How to encode constants in instructions

### Register Architecture
- **Number of Registers**: More registers reduce memory traffic
- **Register Size**: 32-bit, 64-bit, or mixed
- **Special vs General**: Dedicated vs multipurpose registers
- **Register Windows**: SPARC's overlapping register sets

### Memory Model
- **Addressing Range**: How much memory can be addressed
- **Alignment Requirements**: Data alignment restrictions
- **Byte Order**: Big-endian vs little-endian
- **Memory Protection**: User vs kernel space separation

### Performance Implications
- **Instruction Mix**: Frequency of different instruction types
- **Pipeline Compatibility**: How well instructions pipeline
- **Cache Behavior**: Instruction fetch and decode efficiency
- **Compiler Optimization**: How well compilers can optimize
