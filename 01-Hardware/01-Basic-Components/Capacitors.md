# Capacitors

**🏠 [Back to Main](../../README.md)** | **🔧 [Hardware Section](../README.md)** | **🔋 [Basic Components](../01-Basic-Components/)** | **⚡ [Transistors](Transistors.md)** | **🔌 [Diodes](Diodes.md)** | **📍 [Resistors](Resistors.md)**

## Overview
Capacitors store electrical energy in an electric field between two conductive plates separated by an insulating material (dielectric).

## Basic Function
- **Charge Storage**: Accumulates electric charge when voltage is applied
- **Energy Storage**: Stores energy in the electric field
- **Filtering**: Smooths voltage fluctuations in circuits
- **Timing**: Creates time delays in circuits

## Types of Capacitors

### By Construction
- **Ceramic Capacitors**: Small, stable, common in digital circuits
- **Electrolytic Capacitors**: High capacitance, polarized, used for power supply filtering
- **Tantalum Capacitors**: Stable, compact, expensive
- **Film Capacitors**: Precise values, used in audio and precision circuits

### By Application
- **Decoupling Capacitors**: Filter noise in power supply lines
- **Bypass Capacitors**: Provide local energy storage for ICs
- **Timing Capacitors**: Used with resistors for RC timing circuits

## Key Parameters
- **Capacitance**: Measured in Farads (F), typically microfarads (μF) or picofarads (pF)
- **Voltage Rating**: Maximum voltage the capacitor can handle
- **ESR (Equivalent Series Resistance)**: Internal resistance affecting performance
- **Temperature Coefficient**: How capacitance changes with temperature

## Role in Digital Systems
- **Power Supply Filtering**: Smooth ripple in DC power supplies
- **Decoupling**: Provide local energy storage for ICs during switching
- **Signal Coupling**: Pass AC signals while blocking DC
- **Timing Circuits**: Create delays with resistors (RC circuits)

## Capacitor Behavior
- **Charging**: Voltage rises exponentially, current decreases
- **Discharging**: Voltage falls exponentially through load
- **AC Response**: Impedance decreases with frequency (Xc = 1/2πfC)
- **Energy Storage**: E = ½CV²

## Applications in Computing
### Power Management
- **Bulk Capacitors**: Large electrolytic caps for main power filtering
- **Ceramic Capacitors**: High-frequency noise filtering
- **Tantalum Capacitors**: Stable power for sensitive circuits

### Signal Processing
- **Coupling Capacitors**: Connect AC signals between stages
- **Bypass Capacitors**: Route high-frequency noise to ground
- **Timing Capacitors**: Generate clock signals and delays

### Memory Systems
- **DRAM Storage**: Each bit stored as charge on tiny capacitor
- **Refresh Circuits**: Maintain charge in dynamic memory
- **Sample and Hold**: Capture analog signals for ADC conversion

## Practical Considerations
- **Parasitic Effects**: ESL (inductance) and ESR affect high-frequency performance
- **Temperature Stability**: Capacitance varies with temperature
- **Aging**: Electrolytic capacitors dry out over time
- **Polarity**: Electrolytic and tantalum caps must be connected correctly
- **Voltage Derating**: Use capacitors rated well above operating voltage

## Testing and Troubleshooting
- **Capacitance Measurement**: Use LCR meter or multimeter
- **ESR Testing**: Important for power supply capacitors
- **Visual Inspection**: Look for bulging or leaking electrolytics
- **In-Circuit Testing**: May require disconnection for accurate measurement
