# Operating System Fundamentals

**🏠 [Back to Main](../../README.md)** | **💻 [Software Section](../README.md)** | **🖥️ [Operating Systems](README.md)** | **⚙️ [Assembly Language](../01-Assembly-Language/)** | **🔤 [Programming Languages](../03-Programming-Languages/)**

## Overview
An operating system (OS) is the software layer that manages computer hardware resources and provides services for application programs. It acts as an intermediary between users/applications and the computer hardware.

## What is an Operating System?

### Core Definition
- **Resource Manager**: Manages CPU, memory, storage, and I/O devices
- **Interface Provider**: Provides interface between applications and hardware
- **Service Provider**: Offers services like file management, networking, security
- **Platform**: Foundation upon which applications run

### Key Functions
- **Process Management**: Creating, scheduling, and terminating processes
- **Memory Management**: Allocating and deallocating memory
- **File System Management**: Organizing and accessing files
- **Device Management**: Controlling hardware devices
- **Security and Protection**: Ensuring system and data security
- **User Interface**: Command line or graphical interface

## System Architecture

### Kernel and User Space
**Kernel Space**:
- **Privileged Mode**: Full access to hardware and system resources
- **Kernel Code**: Core OS functions running with maximum privileges
- **System Calls**: Interface for user programs to request kernel services
- **Device Drivers**: Hardware-specific code running in kernel space

**User Space**:
- **Unprivileged Mode**: Limited access to system resources
- **Applications**: User programs and utilities
- **Libraries**: Shared code libraries (libc, DLLs)
- **Protection**: Memory protection prevents interference

### System Calls
**Definition**: Programming interface to request kernel services

**Common Categories**:
- **Process Control**: fork(), exec(), exit(), wait()
- **File Operations**: open(), read(), write(), close()
- **Device Management**: ioctl(), mount(), unmount()
- **Information Maintenance**: getpid(), alarm(), sleep()
- **Communication**: pipe(), shmget(), msgget()

**Implementation**:
1. Application makes system call
2. CPU switches to kernel mode
3. Kernel validates request and parameters
4. Kernel performs requested operation
5. Result returned to application
6. CPU switches back to user mode

## OS Architecture Patterns

### Monolithic Kernel
**Structure**: All OS services run in kernel space
**Examples**: Linux, traditional Unix
**Advantages**:
- High performance (no mode switches)
- Efficient communication between components
- Simple design and implementation

**Disadvantages**:
- Large kernel size
- Security risks (everything runs privileged)
- Difficult to maintain and debug
- Stability issues (one bug can crash system)

### Microkernel
**Structure**: Minimal kernel with services in user space
**Examples**: Minix, QNX, L4
**Advantages**:
- Better security and stability
- Modular design
- Easier to maintain and extend
- Fault isolation

**Disadvantages**:
- Performance overhead (frequent mode switches)
- Complex inter-process communication
- More complex implementation

### Hybrid Kernel
**Structure**: Compromise between monolithic and microkernel
**Examples**: Windows NT, macOS (XNU)
**Approach**: Keep performance-critical services in kernel
**Benefits**: Balance of performance and modularity

### Layered Architecture
**Structure**: OS organized in layers, each using services of layer below
**Example**: THE operating system, Windows layers
**Benefits**: Clear separation of concerns, easier testing
**Drawbacks**: Performance overhead, rigid structure

## Process and Thread Concepts

### Processes
**Definition**: Program in execution with associated resources

**Process Components**:
- **Program Code**: Instructions to execute
- **Program Counter**: Current instruction location
- **Registers**: CPU register values
- **Stack**: Function calls and local variables
- **Heap**: Dynamically allocated memory
- **Data Section**: Global variables

**Process States**:
- **New**: Process being created
- **Ready**: Waiting to be assigned to processor
- **Running**: Instructions being executed
- **Waiting**: Waiting for event (I/O completion)
- **Terminated**: Process has finished execution

### Threads
**Definition**: Lightweight processes sharing same address space

**Thread Types**:
- **User Threads**: Managed by user-level library
- **Kernel Threads**: Managed by operating system kernel
- **Hybrid**: Combination of user and kernel threads

**Benefits**:
- **Parallelism**: Multiple threads can run simultaneously
- **Responsiveness**: UI remains responsive during long operations
- **Resource Sharing**: Threads share memory and files
- **Economy**: Less overhead than creating processes

## Memory Management Concepts

### Virtual Memory
**Concept**: Abstraction that gives each process illusion of large, private memory

**Benefits**:
- **Isolation**: Processes cannot interfere with each other
- **Large Address Space**: Programs can use more memory than physically available
- **Sharing**: Safe sharing of memory between processes
- **Security**: Memory protection and access control

### Memory Hierarchy
1. **CPU Registers**: Fastest, smallest capacity
2. **Cache Memory**: Very fast, small capacity
3. **Main Memory (RAM)**: Fast, medium capacity
4. **Secondary Storage**: Slower, large capacity

### Memory Allocation
**Static Allocation**: Memory allocated at compile time
**Dynamic Allocation**: Memory allocated at runtime
**Garbage Collection**: Automatic memory management in some languages

## File System Concepts

### File System Services
- **File Creation and Deletion**: Managing file lifecycle
- **Directory Management**: Organizing files in hierarchical structure
- **Access Control**: Permissions and security
- **Backup and Recovery**: Data protection and restoration

### File Operations
- **Open**: Prepare file for reading/writing
- **Read/Write**: Transfer data to/from file
- **Seek**: Position file pointer
- **Close**: Release file resources

### Directory Structure
- **Single-Level**: All files in one directory (simple but limited)
- **Two-Level**: User directories under root (basic organization)
- **Tree Structure**: Hierarchical organization (most common)
- **Acyclic Graph**: Shared subdirectories allowed

## Device Management

### Device Types
**Block Devices**: Data accessed in fixed-size blocks (hard drives, SSDs)
**Character Devices**: Data accessed as stream of characters (keyboards, mice)
**Network Devices**: Network interfaces and communication

### Device Drivers
**Purpose**: Hardware-specific code to control devices
**Interface**: Standardized interface between OS and hardware
**Location**: Usually run in kernel mode for direct hardware access

### I/O Methods
**Programmed I/O**: CPU directly controls I/O operations
**Interrupt-Driven I/O**: Device notifies CPU when operation complete
**DMA (Direct Memory Access)**: Device transfers data directly to/from memory

## Security and Protection

### Security Goals
- **Confidentiality**: Information accessible only to authorized users
- **Integrity**: Information and programs protected from unauthorized modification
- **Availability**: System resources available to authorized users

### Protection Mechanisms
- **Authentication**: Verify user identity
- **Authorization**: Control access to resources
- **Audit**: Track system usage and security events
- **Encryption**: Protect data in storage and transmission

### Access Control
**Discretionary Access Control (DAC)**: Owner controls access
**Mandatory Access Control (MAC)**: System enforces access policy
**Role-Based Access Control (RBAC)**: Access based on user roles

## Performance and Optimization

### Performance Metrics
- **Throughput**: Amount of work completed per unit time
- **Response Time**: Time from request to completion
- **Utilization**: Percentage of time resource is busy
- **Fairness**: Equal treatment of similar requests

### Optimization Strategies
- **Caching**: Store frequently accessed data in fast memory
- **Buffering**: Temporarily store data to smooth I/O operations
- **Scheduling**: Optimize order of operations
- **Load Balancing**: Distribute work across multiple resources

## Modern OS Trends

### Virtualization
- **Virtual Machines**: Multiple OS instances on single hardware
- **Containers**: Lightweight application isolation
- **Cloud Computing**: OS services delivered over network

### Distributed Systems
- **Cluster Computing**: Multiple machines working together
- **Grid Computing**: Geographically distributed resources
- **Edge Computing**: Processing near data sources

### Mobile and Embedded
- **Power Management**: Optimizing battery life
- **Real-Time Constraints**: Meeting timing deadlines
- **Resource Constraints**: Limited memory and processing power

### Security Enhancements
- **Trusted Computing**: Hardware-based security
- **Sandboxing**: Isolating untrusted code
- **Secure Boot**: Verifying system integrity during startup
