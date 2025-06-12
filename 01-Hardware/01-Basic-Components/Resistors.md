# Resistors

## Overview
Resistors are passive electrical components that limit the flow of electric current in a circuit. They are fundamental components in virtually all electronic circuits.

## Basic Function
- **Current Limiting**: Controls the amount of current flowing through a circuit
- **Voltage Division**: Creates specific voltage levels from a higher voltage
- **Power Dissipation**: Converts electrical energy to heat
- **Signal Conditioning**: Shapes electrical signals

## Ohm's Law
The fundamental relationship governing resistor behavior:
- **V = I × R**
  - V = Voltage (Volts)
  - I = Current (Amperes)
  - R = Resistance (Ohms, Ω)

## Types of Resistors

### By Construction
- **Carbon Film**: Inexpensive, general purpose
- **Metal Film**: More precise, stable
- **Wire Wound**: High power applications
- **Thick/Thin Film**: Surface mount applications

### By Function
- **Fixed Resistors**: Constant resistance value
- **Variable Resistors (Potentiometers)**: Adjustable resistance
- **Thermistors**: Temperature-dependent resistance
- **Photoresistors**: Light-dependent resistance

## Key Parameters
- **Resistance Value**: Measured in Ohms (Ω)
- **Power Rating**: Maximum power it can dissipate (Watts)
- **Tolerance**: Accuracy of resistance value (±%)
- **Temperature Coefficient**: How resistance changes with temperature

## Color Code System
Standard color bands indicate resistance value:
- **4-Band**: Value, multiplier, tolerance
- **5-Band**: More precise values
- **6-Band**: Includes temperature coefficient

## Applications in Digital Systems
- **Pull-up/Pull-down**: Ensure defined logic levels
- **Current Limiting**: Protect LEDs and other components
- **Timing Circuits**: With capacitors for RC delays
- **Voltage Dividers**: Create reference voltages
- **Termination**: Prevent signal reflections

## Series and Parallel Combinations
- **Series**: Rtotal = R1 + R2 + R3 + ...
- **Parallel**: 1/Rtotal = 1/R1 + 1/R2 + 1/R3 + ...

## Practical Considerations
- **Power Dissipation**: P = I²R = V²/R
- **Heat Management**: Adequate cooling for high-power resistors
- **Parasitic Effects**: Inductance and capacitance at high frequencies
- **Noise**: Thermal noise in precision circuits
