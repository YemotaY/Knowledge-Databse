# Transistors

**🏠 [Main](../../README.md)** | **🔧 [Hardware](../README.md)** | **🧮 [Logic Gates](../02-Logic-Gates/)** | **🔋 [Capacitors](Capacitors.md)** | **⚡ [Resistors](Resistors.md)** | **🔌 [Diodes](Diodes.md)**

## Overview
Transistors are the fundamental building blocks of all modern electronic devices. They act as switches and amplifiers, enabling the digital logic that powers computers.

## Types of Transistors

### Bipolar Junction Transistors (BJT)
- **NPN Transistors**: Current flows from collector to emitter when base is positive
- **PNP Transistors**: Current flows from emitter to collector when base is negative

### Field Effect Transistors (FET)
- **MOSFET (Metal-Oxide-Semiconductor FET)**
  - Most common in digital circuits
  - Low power consumption
  - Used in microprocessors and memory
- **JFET (Junction FET)**
  - Used in analog applications

## How Transistors Work
1. **As a Switch**: Small current at base/gate controls large current flow
2. **As an Amplifier**: Small input signal produces larger output signal

## Digital Logic Applications
- Binary states: ON (1) and OFF (0)
- Logic gates are built from transistor combinations
- Modern processors contain billions of transistors

## Key Properties
- **Switching Speed**: How fast it can turn on/off
- **Power Consumption**: Energy used during operation
- **Gain**: Amplification factor (β for BJT, gm for FET)
- **Threshold Voltage**: Gate voltage needed to turn on MOSFET

## MOSFET Operation in Detail
### N-Channel MOSFET
- **Gate voltage positive**: Creates conductive channel
- **Drain-Source current**: Flows when gate voltage > threshold
- **Enhancement mode**: Normally off, turned on by gate voltage

### P-Channel MOSFET
- **Gate voltage negative**: Creates conductive channel
- **Complementary to N-channel**: Used in CMOS logic
- **Lower performance**: Due to hole mobility vs electron mobility

## CMOS Technology
- **Complementary MOS**: Uses both N and P channel MOSFETs
- **Low power**: Only consumes power during switching
- **High noise immunity**: Clear distinction between 0 and 1
- **Scalability**: Can be made very small

## Transistor Scaling and Moore's Law
- **Feature Size**: Currently around 3-5nm in advanced processors
- **Challenges**: Quantum effects, power density, manufacturing cost
- **3D Structures**: FinFET and gate-all-around transistors

## Applications Beyond Logic
- **Memory Cells**: Single transistor DRAM cells
- **Analog Circuits**: Amplifiers and voltage regulators
- **Power Management**: Switch-mode power supplies
- **RF Circuits**: High-frequency amplification and switching

## Practical Considerations
- **Heat Generation**: Power dissipation creates heat
- **Leakage Current**: Small current even when "off"
- **Parasitic Capacitances**: Limit switching speed
- **Process Variation**: Manufacturing tolerances affect performance
- **Reliability**: Electromigration, hot carrier effects, gate oxide breakdown

## Future Developments
- **New Materials**: Graphene, carbon nanotubes
- **Quantum Effects**: Tunneling transistors
- **Neuromorphic**: Transistors that mimic neurons
- **Optical**: Light-based switching
