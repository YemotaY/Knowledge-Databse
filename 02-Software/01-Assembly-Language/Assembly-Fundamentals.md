# Assembly Language Fundamentals

**🏠 [Main](../../README.md)** | **💻 [Software](../README.md)** | **🖥️ [CPU Architecture](../../01-Hardware/03-Processors/CPU-Architecture.md)** | **🔧 [Operating Systems](../02-Operating-Systems/)**

## Overview
Assembly language is a low-level programming language that provides a human-readable representation of machine code instructions. It offers direct control over hardware while being more readable than pure machine code.

## What is Assembly Language?
- **One-to-one correspondence** with machine code instructions
- **Hardware-specific**: Each processor architecture has its own assembly language
- **Symbolic representation**: Uses mnemonics instead of binary codes
- **Direct hardware access**: Can manipulate registers, memory, and I/O directly

## Key Components

### Instructions
- **Mnemonics**: Human-readable operation codes (MOV, ADD, SUB, JMP)
- **Operands**: Data or addresses the instruction operates on
- **Addressing Modes**: Different ways to specify where data is located

### Registers
- **General Purpose**: Store temporary data and intermediate results
- **Special Purpose**: Stack pointer, instruction pointer, flags register
- **Architecture Dependent**: Number and size vary by processor

### Memory Addressing
- **Direct Addressing**: Specify exact memory address
- **Indirect Addressing**: Address stored in register or memory location
- **Indexed Addressing**: Base address plus offset

## Common Assembly Instructions

### Data Movement
- **MOV**: Move data between registers, memory, or immediate values
- **LOAD/STORE**: Load from memory to register, store from register to memory
- **PUSH/POP**: Stack operations for temporary storage

### Arithmetic Operations
- **ADD/SUB**: Addition and subtraction
- **MUL/DIV**: Multiplication and division
- **INC/DEC**: Increment and decrement by one
- **NEG**: Two's complement negation

### Logical Operations
- **AND/OR/XOR**: Bitwise logical operations
- **NOT**: Bitwise complement
- **SHL/SHR**: Bit shifting left/right
- **ROL/ROR**: Bit rotation left/right

### Control Flow
- **JMP**: Unconditional jump
- **JE/JNE**: Jump if equal/not equal
- **JG/JL**: Jump if greater/less than
- **CALL/RET**: Function call and return
- **LOOP**: Loop with counter

### Comparison
- **CMP**: Compare two values (sets flags)
- **TEST**: Bitwise test (sets flags)
- **Flags**: Zero, carry, overflow, sign flags

## Assembly Language Structure

### Program Sections
- **Data Section**: Global variables and constants
- **BSS Section**: Uninitialized data
- **Text Section**: Program instructions
- **Stack Section**: Local variables and function calls

### Labels and Symbols
- **Labels**: Mark locations in code for jumps and calls
- **Symbols**: Named constants and variables
- **Global/Local**: Scope of symbols

### Directives
- **Assembler Directives**: Instructions to the assembler, not CPU
- **Data Declaration**: DB (byte), DW (word), DD (dword)
- **Section Declaration**: .data, .text, .bss
- **Include/Import**: Include other files or libraries

## Example Assembly Program (x86)
```assembly
section .data
    msg db 'Hello, World!', 0
    msg_len equ $ - msg

section .text
    global _start

_start:
    ; Write system call
    mov eax, 4          ; sys_write
    mov ebx, 1          ; stdout
    mov ecx, msg        ; message
    mov edx, msg_len    ; length
    int 0x80            ; system call

    ; Exit system call
    mov eax, 1          ; sys_exit
    mov ebx, 0          ; exit status
    int 0x80            ; system call
```

## Advantages of Assembly
- **Maximum Performance**: Direct hardware control
- **Precise Control**: Exact control over every instruction
- **Small Code Size**: Optimal code generation
- **Real-time Systems**: Predictable timing
- **System Programming**: OS kernels, device drivers
- **Embedded Systems**: Resource-constrained environments

## Disadvantages of Assembly
- **Hardware Specific**: Not portable between architectures
- **Development Time**: Much slower to write than high-level languages
- **Maintenance**: Difficult to read and modify
- **Error Prone**: Easy to make mistakes
- **No Abstraction**: Must handle all details manually

## When to Use Assembly
- **Performance Critical**: When every cycle counts
- **Hardware Interface**: Direct hardware manipulation
- **System Boot**: Bootloaders and kernel initialization
- **Embedded Systems**: Microcontrollers with limited resources
- **Cryptography**: Constant-time implementations
- **Optimization**: Critical inner loops in high-level programs

## Assembly Tools

### Assemblers
- **NASM**: Netwide Assembler (x86/x64)
- **MASM**: Microsoft Macro Assembler
- **GAS**: GNU Assembler (part of binutils)
- **YASM**: Modular assembler

### Linkers
- **LD**: GNU linker
- **LINK**: Microsoft linker
- **Function**: Combines object files into executable

### Debuggers
- **GDB**: GNU debugger
- **OllyDbg**: Windows assembly debugger
- **IDA Pro**: Disassembler and debugger

## Mixed Programming
- **Inline Assembly**: Assembly code within high-level language
- **Assembly Functions**: Separate assembly modules called from high-level code
- **Compiler Intrinsics**: High-level interface to assembly instructions
- **Optimization**: Hand-optimize critical sections

## Learning Assembly
- **Start Simple**: Basic register operations and data movement
- **Use Debugger**: Step through instructions to understand execution
- **Read Disassembly**: Examine compiler output
- **Practice**: Write simple programs and build complexity
- **Architecture Manual**: Reference for instruction details
