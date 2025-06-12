# USB Standards

**🏠 [Back to Main](../../README.md)** | **🔧 [Hardware Section](../README.md)** | **🔌 [Input-Output Section](README.md)** | **💿 [Storage](../05-Storage/)** | **🖥️ [Modern Hardware](../07-Modern-Hardware/)**

## Overview
Universal Serial Bus (USB) is the dominant interface for connecting peripherals to computers. It has evolved through multiple generations, increasing speed, power delivery, and functionality while maintaining backward compatibility.

## USB Evolution and Versions

### USB 1.0/1.1 (1996/1998)
- **Speed**: Low Speed (1.5 Mbps), Full Speed (12 Mbps)
- **Connectors**: USB-A, USB-B
- **Power**: 5V, up to 500mA (2.5W)
- **Applications**: Keyboards, mice, basic peripherals

### USB 2.0 (2000)
- **Speed**: High Speed (480 Mbps)
- **Backward Compatibility**: Works with USB 1.1 devices
- **Mini/Micro Connectors**: Smaller form factors introduced
- **On-The-Go (OTG)**: Mobile devices can act as hosts
- **Applications**: External storage, cameras, mobile devices

### USB 3.0/3.1/3.2 (2008/2013/2017)
**USB 3.0 (SuperSpeed)**:
- **Speed**: 5 Gbps
- **Connectors**: USB-A and USB-B with additional pins
- **Power**: Up to 900mA (4.5W)

**USB 3.1**:
- **Gen 1**: 5 Gbps (same as USB 3.0)
- **Gen 2**: 10 Gbps (SuperSpeed+)
- **USB-C**: New reversible connector introduced

**USB 3.2**:
- **Gen 1**: 5 Gbps
- **Gen 2**: 10 Gbps  
- **Gen 2x2**: 20 Gbps (dual-lane USB-C only)

### USB4 (2019)
- **Speed**: Up to 40 Gbps
- **Protocol**: Based on Thunderbolt 3
- **Connector**: USB-C exclusively
- **Tunneling**: Can carry DisplayPort, PCIe signals
- **Power Delivery**: Up to 100W

## USB Connectors and Form Factors

### USB-A (Standard)
- **Type**: Host connector (computers)
- **Orientation**: Not reversible
- **Versions**: USB 1.1, 2.0, 3.0+ (blue interior)
- **Applications**: Desktop/laptop computers, hubs

### USB-B (Standard)
- **Type**: Device connector (printers, etc.)
- **Size**: Larger, square-ish shape
- **Versions**: USB 1.1, 2.0, 3.0 (additional pins)
- **Applications**: Printers, external hard drives

### Mini-USB
- **Size**: Smaller than standard USB
- **Types**: Mini-A, Mini-B, Mini-AB
- **Applications**: Older cameras, MP3 players
- **Status**: Largely obsolete

### Micro-USB
- **Size**: Even smaller than Mini-USB
- **Types**: Micro-A, Micro-B
- **USB 3.0**: Additional pins for SuperSpeed
- **Applications**: Smartphones, tablets (before USB-C)

### USB-C
- **Reversible**: Can be inserted either way
- **Size**: Similar to Micro-USB but thicker
- **Pins**: 24 pins for maximum functionality
- **Features**: Power delivery, alternate modes, high speed
- **Applications**: Modern laptops, smartphones, tablets

## USB Power Delivery (USB-PD)

### Standard USB Power
**USB 2.0**: 5V @ 500mA (2.5W)
**USB 3.0**: 5V @ 900mA (4.5W)
**USB-BC (Battery Charging)**: Up to 1.5A (7.5W)

### USB Power Delivery Specification
**Power Profiles**:
- **Profile 1**: 5V @ 2A (10W)
- **Profile 2**: 5V @ 3A, 12V @ 1.5A (15W)
- **Profile 3**: 5V @ 3A, 12V @ 3A, 20V @ 1.5A (36W)
- **Profile 4**: 5V @ 3A, 12V @ 3A, 20V @ 3A (60W)
- **Profile 5**: 5V @ 3A, 12V @ 3A, 20V @ 5A (100W)

**Extended Power Range (EPR)**:
- **28V @ 5A**: 140W
- **36V @ 5A**: 180W
- **48V @ 5A**: 240W

### Power Delivery Features
**Negotiation**: Device and charger negotiate optimal power
**Bi-directional**: Power can flow either direction
**Dynamic**: Power requirements can change during operation
**Safety**: Built-in protection against overload

## USB Device Classes

### Human Interface Device (HID)
- **Applications**: Keyboards, mice, game controllers
- **Driver**: Generic HID drivers in OS
- **Protocol**: Standardized report descriptors
- **Features**: Boot protocol for BIOS compatibility

### Mass Storage Class (MSC)
- **Applications**: USB flash drives, external hard drives
- **Protocol**: SCSI commands over USB
- **Compatibility**: Works with standard file system drivers
- **Limitations**: One LUN (Logical Unit) per interface

### Communication Device Class (CDC)
- **Applications**: USB-to-serial adapters, modems
- **Subtypes**: Abstract Control Model (ACM), Ethernet
- **Virtual Serial**: Creates virtual COM port
- **Networking**: USB-to-Ethernet adapters

### Audio Class
- **Applications**: USB headphones, speakers, microphones
- **Versions**: Audio Class 1.0, 2.0, 3.0
- **Features**: Digital audio streaming, MIDI support
- **Latency**: Low-latency audio for professional applications

### Video Class (UVC)
- **Applications**: USB webcams, video capture devices
- **Driverless**: Works without additional drivers
- **Formats**: Support for various video formats and resolutions
- **Streaming**: Real-time video streaming capability

## USB Architecture and Protocols

### USB Topology
**Tiered Star**: Host at center, devices and hubs branching out
**Maximum Tiers**: 7 levels (including root hub)
**Devices per Hub**: Up to 127 devices total per host controller
**Cable Length**: 5 meters maximum per segment

### USB Transfer Types
**Control Transfers**:
- **Purpose**: Device configuration and control
- **Reliability**: Error detection and retry
- **Bandwidth**: Guaranteed bandwidth allocation

**Bulk Transfers**:
- **Purpose**: Large data transfers (storage devices)
- **Reliability**: Error detection and retry
- **Bandwidth**: Uses available bandwidth

**Interrupt Transfers**:
- **Purpose**: Regular, small data transfers (HID devices)
- **Timing**: Guaranteed maximum latency
- **Polling**: Host polls device at regular intervals

**Isochronous Transfers**:
- **Purpose**: Real-time data (audio, video)
- **Timing**: Guaranteed bandwidth and timing
- **No Retry**: No error correction for timing preservation

### USB Descriptors
**Device Descriptor**: Basic device information
**Configuration Descriptor**: Device capabilities and power requirements
**Interface Descriptor**: Functional interfaces provided
**Endpoint Descriptor**: Communication endpoints and properties
**String Descriptors**: Human-readable device information

## USB-C Alternate Modes

### DisplayPort Alternate Mode
- **Purpose**: Video output over USB-C
- **Bandwidth**: Up to 32.4 Gbps (DisplayPort 1.4)
- **Features**: 4K/8K video, audio, multi-stream
- **Compatibility**: Works with USB-C to DisplayPort cables

### Thunderbolt 3/4 Alternate Mode
- **Speed**: 40 Gbps bidirectional
- **Protocols**: PCIe, DisplayPort tunneling
- **Daisy Chaining**: Up to 6 devices
- **eGPU Support**: External graphics cards

### HDMI Alternate Mode
- **Purpose**: HDMI output over USB-C
- **Versions**: HDMI 1.4b support
- **Applications**: Direct connection to HDMI displays
- **Limitation**: Less common than DisplayPort Alt Mode

## USB Performance Optimization

### Bandwidth Considerations
**Shared Bandwidth**: USB devices share bus bandwidth
**Hub Impact**: Hubs can create bottlenecks
**Transfer Type**: Different types have different priorities
**USB 3.0+ Duplex**: Full-duplex communication possible

### Electrical Considerations
**Cable Quality**: High-speed signals require quality cables
**Length Limitations**: Longer cables may not support full speed
**EMI Shielding**: Important for reliable high-speed operation
**Power Drop**: Voltage drop over long cables

### Driver and Software
**Native Drivers**: OS-provided drivers usually optimal
**Device Specific**: Some devices need specialized drivers
**Power Management**: USB selective suspend for power saving
**Latency**: Real-time applications need low-latency drivers

## USB Testing and Compliance

### USB-IF Compliance
**Certification**: USB Implementers Forum testing
**Logos**: Authorized USB logos for compliant devices
**Interoperability**: Ensures devices work together
**Electrical**: Signal integrity and power requirements

### Common Issues
**Enumeration Problems**: Device not recognized
**Power Issues**: Insufficient power delivery
**Speed Negotiation**: Falling back to lower speeds
**Cable Problems**: Non-compliant or damaged cables

## Future of USB

### USB4 Version 2.0
- **Speed**: Up to 80 Gbps
- **Backward Compatibility**: With existing USB-C devices
- **Applications**: High-bandwidth professional applications

### Emerging Applications
**USB-C Everywhere**: Replacing proprietary connectors
**Power Delivery**: Higher power for larger devices
**Docking Solutions**: Single cable for everything
**IoT Devices**: USB-C for Internet of Things applications

### Industry Trends
**Connector Standardization**: EU mandating USB-C for phones
**Wireless USB**: Wi-Fi and wireless protocols
**Automotive**: USB-C in vehicles
**Industrial**: USB in industrial and embedded applications
