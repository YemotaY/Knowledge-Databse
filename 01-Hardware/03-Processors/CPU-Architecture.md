# CPU Architecture

**🏠 [Main](../../README.md)** | **🔧 [Hardware](../README.md)** | **🚪 [Logic Gates](../02-Logic-Gates/)** | **💾 [Memory](../04-Memory/)** | **📋 [Instruction Sets](Instruction-Sets.md)**

## Overview
The Central Processing Unit (CPU) is the brain of a computer, executing instructions and coordinating all system operations. Modern CPUs are incredibly complex, containing billions of transistors organized into functional units.

## Table of Contents
- [Basic CPU Components](#basic-cpu-components)
- [Instruction Execution Cycle](#instruction-execution-cycle)
- [Cache Hierarchy in Multi-Core](#cache-hierarchy-in-multi-core)
- [CPU Organization Models](#cpu-organization-models)
- [Addressing and Memory Management](#addressing-and-memory-management)
- [Performance Factors](#performance-factors)
- [Modern CPU Features](#modern-cpu-features)

## Basic CPU Components

### Control Unit (CU)
- **Function**: Orchestrates instruction execution
- **Responsibilities**:
  - Fetches instructions from memory
  - Decodes instruction format
  - Generates control signals
  - Manages instruction pipeline

### Arithmetic Logic Unit (ALU)
- **Function**: Performs mathematical and logical operations
- **Operations**:
  - Arithmetic: Add, subtract, multiply, divide
  - Logic: AND, OR, NOT, XOR
  - Comparison: Equal, greater than, less than
  - Bit manipulation: Shift, rotate

### Registers
- **Function**: High-speed storage within CPU
- **Types**:
  - **General Purpose**: Store data and addresses
  - **Special Purpose**: Program counter, stack pointer, flags
  - **Index/Pointer**: Array indexing and memory addressing

## Instruction Execution Cycle

### Fetch-Decode-Execute Cycle
1. **Fetch**: Retrieve instruction from memory at PC address
2. **Decode**: Interpret instruction format and operands
3. **Execute**: Perform the operation
4. **Store**: Write results back to memory/registers
5. **Update**: Increment program counter

### Pipeline Architecture
Modern CPUs use pipelining to overlap instruction execution:

#### Classic 5-Stage Pipeline
1. **IF (Instruction Fetch)**: Get instruction from memory
2. **ID (Instruction Decode)**: Decode and read registers
3. **EX (Execute)**: Perform ALU operation
4. **MEM (Memory)**: Access data memory if needed
5. **WB (Write Back)**: Store result in register

#### Pipeline Hazards
- **Structural**: Resource conflicts
- **Data**: Dependencies between instructions
- **Control**: Branch instructions disrupting flow

## Cache Hierarchy

### Cache Levels
- **L1 Cache**: Fastest, smallest, closest to CPU cores
  - Instruction cache (I-cache)
  - Data cache (D-cache)
  - Typical size: 32-64KB per core
- **L2 Cache**: Larger, slightly slower
  - Unified instruction and data
  - Typical size: 256KB-1MB per core
- **L3 Cache**: Largest, shared between cores
  - Typical size: 8-32MB total

### Cache Organization
- **Cache Lines**: Fixed-size blocks (typically 64 bytes)
- **Associativity**: How cache lines map to memory
- **Replacement Policies**: LRU, random, FIFO
- **Write Policies**: Write-through, write-back

## CPU Organization Models

### Von Neumann Architecture
- **Shared Memory**: Instructions and data in same memory space
- **Single Bus**: One pathway between CPU and memory
- **Simplicity**: Easier to implement and program
- **Bottleneck**: Memory bandwidth limits performance

### Harvard Architecture
- **Separate Memory**: Instruction and data memory separate
- **Parallel Access**: Can fetch instruction and data simultaneously
- **Performance**: Higher bandwidth, faster execution
- **Complexity**: More complex memory system

### Modified Harvard
- **L1 Cache Split**: Separate instruction and data caches
- **Unified Memory**: Single main memory space
- **Best of Both**: Performance benefit with programming simplicity

## Addressing and Memory Management

### Memory Addressing
- **Physical Addressing**: Direct memory addresses
- **Virtual Addressing**: Logical addresses translated by MMU
- **Segmentation**: Memory divided into segments
- **Paging**: Memory divided into fixed-size pages

### Memory Management Unit (MMU)
- **Address Translation**: Virtual to physical address mapping
- **Protection**: Prevent unauthorized memory access
- **Cache Control**: Manage cache coherency
- **TLB**: Translation Lookaside Buffer for fast translation

## Performance Factors

### Clock Speed
- **Frequency**: Number of cycles per second (GHz)
- **Cycle Time**: Time for one clock cycle
- **Not Everything**: Higher clock doesn't always mean better performance

### Instructions Per Cycle (IPC)
- **Efficiency**: How many instructions completed per clock cycle
- **Superscalar**: Multiple execution units allow IPC > 1
- **Pipeline Depth**: Affects maximum achievable IPC

### Memory Hierarchy Performance
- **Memory Wall**: Gap between processor and memory speed
- **Cache Hit Rate**: Percentage of memory accesses served by cache
- **Memory Bandwidth**: Rate of data transfer to/from memory

## Modern CPU Features

### Branch Prediction
- **Purpose**: Guess direction of conditional branches
- **Types**: Static, dynamic, hybrid predictors
- **Importance**: Avoid pipeline stalls on branches

### Out-of-Order Execution
- **Concept**: Execute instructions when operands are ready
- **Benefits**: Better utilization of execution units
- **Complexity**: Requires sophisticated control logic

### Simultaneous Multithreading (SMT)
- **Hyperthreading**: Intel's implementation
- **Concept**: Multiple threads share CPU resources
- **Benefits**: Better resource utilization
- **Challenges**: Resource contention between threads
