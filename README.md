# Application-Driven Development for USB-to-RS-422 Conversion

### FPGA-Based Communication Architecture • RTL Design • Embedded Communication • Fault-Tolerant Systems • Simulation & Verification

> **A simulation-verified FPGA-based communication architecture developed during my DRDO-RCI internship to bridge a USB host interface with an RS-422 serial communication link, with emphasis on structured data handling, buffering, error detection, redundancy, retransmission, deterministic timing, and verification.**

---

## Project at a Glance

| Category                      | Details                                                               |
| ----------------------------- | --------------------------------------------------------------------- |
| **Internship Organization**   | DRDO – Research Centre Imarat (RCI), Hyderabad                        |
| **Project**                   | Application-Driven Development for USB to RS-422 Conversion           |
| **Domain**                    | Embedded Systems / FPGA / Digital Communication                       |
| **Primary Platform**          | Xilinx Kintex-7 / KC705                                               |
| **USB Interface**             | Cypress FX2LP                                                         |
| **HDL**                       | Verilog                                                               |
| **System Clock Used in RTL**  | 48 MHz                                                                |
| **USB Data Width**            | 8-bit parallel                                                        |
| **Serial Interface**          | RS-422                                                                |
| **UART Baud Rate**            | 115200 bps                                                            |
| **Buffering**                 | 1 KB FIFO architecture                                                |
| **Integrity Mechanism**       | 32-bit XOR-based checksum                                             |
| **Reliability Features**      | Error injection, retransmission, redundancy/failover, watchdog        |
| **Verification Environment**  | Vivado 2020.2                                                         |
| **Verification Method**       | RTL simulation, waveform analysis and console-based analysis          |
| **Payloads Evaluated**        | 12-byte and 100-byte test cases                                       |
| **Primary Engineering Focus** | Reliable and controlled interface conversion using programmable logic |

---

# 1. Project Overview

Modern embedded systems frequently need to communicate across interfaces that were designed for very different environments.

A host computer may naturally use **USB** for convenient, high-speed communication, while an embedded, industrial, aerospace, or defence-oriented subsystem may use a more deterministic and electrically robust serial interface such as **RS-422**.

These interfaces differ fundamentally in their communication characteristics, timing behaviour, data representation, and system-level requirements. A communication bridge therefore cannot simply forward bytes from one interface to another. It must manage data reception, buffering, sequencing, serialization, integrity checking, timing, and fault conditions in a controlled manner.

This project explored the design of such a bridge using **programmable logic and FPGA-based RTL architecture**.

The work focused on an application-driven USB-to-RS-422 communication system in which a Cypress FX2LP USB interface provides parallel data to a Kintex-7 FPGA. The FPGA performs the internal data handling and communication control before generating a serial output suitable for an RS-422 transmission path.

Rather than treating the FPGA as a simple data-forwarding element, the architecture incorporates several hardware-level mechanisms for structured communication and verification:

* USB data reception
* FIFO-based buffering
* packet framing
* checksum-based integrity verification
* UART serialization
* RS-422 differential output representation
* performance monitoring
* controlled error injection
* redundant-channel monitoring
* automatic failover
* retransmission control
* watchdog supervision

The complete design was evaluated through **Verilog-based RTL simulation using Vivado 2020.2**, with waveform and console analysis used to examine system behaviour under normal and controlled fault conditions.

The project therefore provided practical exposure to the complete engineering flow from **system requirements → architecture → RTL design → verification → performance analysis**.

---

# 2. Engineering Problem

USB is widely used as a host-side communication interface because of its convenience, standardization, and high data-transfer capability. However, its host-controlled communication model and short-range nature can make it less suitable as a direct interface for certain field-level or mission-oriented communication requirements.

RS-422, in contrast, uses differential signalling and is commonly associated with applications where communication robustness, signal integrity, and longer-distance serial transmission are important.

The engineering problem addressed in this project was therefore:

> **How can a host-oriented USB data interface be bridged to an FPGA-controlled RS-422 communication path while maintaining structured data handling, predictable serial timing, data integrity, and mechanisms for fault detection and recovery?**

The project approached this problem through a modular FPGA architecture rather than relying solely on a conventional fixed-function USB-to-serial converter.

The FPGA provides control over the internal communication pipeline, allowing data to be:

1. received from the USB interface,
2. buffered,
3. organized into structured packets,
4. checked for integrity,
5. serialized using UART logic,
6. transferred through the RS-422 transmission path,
7. monitored through internal status and performance signals.

This application-driven approach also provides visibility into the internal communication process, which is particularly useful during development and verification.

---

# 3. System Architecture

At a high level, the communication path can be represented as:

```text
                    HOST SYSTEM
                        │
                        │ USB
                        ▼
                 ┌───────────────┐
                 │ Cypress FX2LP │
                 │ USB Interface │
                 └───────┬───────┘
                         │
                    8-bit Data
                         │
                         ▼
              ┌─────────────────────┐
              │     Kintex-7 FPGA   │
              │                     │
              │  USB Reception      │
              │        ↓            │
              │  FIFO Buffering     │
              │        ↓            │
              │  Packet Handling    │
              │        ↓            │
              │  Checksum / Error   │
              │        ↓            │
              │  UART Serialization │
              │        ↓            │
              │  Redundancy Control │
              │        ↓            │
              │  Monitoring / WDOG  │
              └─────────┬───────────┘
                        │
                        │ Serial Data
                        ▼
               ┌──────────────────┐
               │   RS-422 Driver  │
               │ Differential Link│
               └────────┬─────────┘
                        │
                        ▼
                 TARGET DEVICE
```

The architecture is organized around the FPGA as the central programmable processing and control element.

The USB interface provides an 8-bit parallel data path to the FPGA. The FPGA manages buffering and packet-level processing before converting the data into a UART-style serial stream. The serial representation is then associated with the differential RS-422 transmission path.

The architecture also includes mechanisms for monitoring transmission behaviour and evaluating the response of the system to controlled fault conditions.

---

# 4. Internal FPGA Architecture

The internal design is divided into functional blocks, each responsible for a specific stage of the communication pipeline.

### USB Reception

The USB reception logic interfaces with the Cypress FX2LP and handles incoming 8-bit data.

Important interface concepts include:

* data validity
* readiness / flow control
* FIFO status
* byte sequencing
* controlled data capture

The design uses signals such as `fx2_data`, `fx2_valid`, and `fx2_ready` to coordinate data reception.

---

### FIFO Buffering

A FIFO-based buffering mechanism provides temporary storage between incoming USB data and subsequent serial transmission.

The implemented RTL describes a **1024-byte memory structure** with separate write and read pointers and status information for occupancy, full, and empty conditions.

The buffering stage is important because the USB-side data arrival pattern and UART-side serialization process do not necessarily operate at the same effective data rate.

The FIFO therefore provides an intermediate storage mechanism that helps manage burst-oriented input while the serial transmission stage processes data sequentially.

---

### Packet Framing

The communication architecture uses structured packet handling rather than treating the incoming byte stream as an unstructured sequence.

Packet-related control includes:

* packet start indication
* payload handling
* sequence information
* packet termination
* integrity information

This provides clearer packet boundaries and makes the internal communication process easier to observe and verify during simulation.

---

### Checksum and Integrity Verification

A **32-bit XOR-based checksum** is used for integrity checking within the design.

The checksum mechanism maintains a running accumulator over incoming data. The reported implementation initializes the accumulator to:

```text
32'hFFFFFFFF
```

and updates it using a byte-wise XOR operation.

The checksum mechanism is primarily used within the simulation-oriented verification framework to demonstrate the detection and handling of controlled data-integrity faults.

A deliberate error can be introduced during simulation, allowing the verification environment to evaluate whether the system detects the resulting integrity mismatch and initiates the intended recovery behaviour.

Importantly, this implementation should be understood as an **XOR-based checksum mechanism**, not as a conventional polynomial CRC-32 implementation.

---

# 5. UART-Based RS-422 Transmission

After internal processing, the FPGA converts the parallel byte stream into a serial representation using UART-style transmission logic.

The reported configuration uses:

* **115200 bps**
* start bit
* 8 data bits
* LSB-first transmission
* stop bit
* deterministic bit timing derived from the FPGA clock

The UART transmitter uses a baud-rate divider and bit counter to control serialization.

Each transmitted byte therefore follows a structured 10-bit UART frame:

```text
┌──────────┬────────────────────┬──────────┐
│ Start    │     8 Data Bits    │  Stop    │
│    0     │    LSB → MSB       │    1     │
└──────────┴────────────────────┴──────────┘
```

The serial signal is then represented as complementary differential outputs for the RS-422 transmission path.

This stage demonstrates the relationship between:

**parallel data → digital serialization → UART timing → differential communication interface**

---

# 6. Reliability and Fault-Tolerance Mechanisms

A major focus of the project was not simply transmitting data, but examining how the communication architecture behaves when faults are deliberately introduced.

The design therefore includes several reliability-oriented mechanisms.

## 6.1 Controlled Error Injection

The simulation environment provides a controlled error-injection mechanism.

During the 12-byte verification scenario, an error was deliberately introduced at the fifth byte. This allowed the design's integrity-checking and recovery mechanisms to be evaluated under a known fault condition.

The purpose was not to model every possible communication fault, but to create a repeatable verification scenario in which the system's response could be observed.

---

## 6.2 Retransmission

When an integrity error is detected, the design provides a retransmission mechanism.

The reported RTL supports up to **three retry attempts**.

The retransmission controller therefore demonstrates an important embedded communication concept:

```text
Transmission
     ↓
Integrity Check
     ↓
Error?
 ┌───┴────┐
 No       Yes
 ↓         ↓
Continue  Retry
           ↓
       Retry Limit?
       ┌────┴────┐
      No        Yes
      ↓          ↓
 Retransmit   Stop / Report
```

This creates a basic fault-recovery path rather than allowing an error condition to terminate communication immediately.

---

## 6.3 Redundant Communication Channels

The architecture also incorporates two monitored RS-422 transmission channels.

The redundancy controller monitors channel-health information and maintains an active channel.

The reported behaviour is:

```text
Channel 1 Active
       │
       ▼
Channel 1 Failure?
   │           │
  No          Yes
   │           │
   ▼           ▼
Continue   Check Channel 2
               │
               ▼
       Channel 2 Healthy?
          │          │
         Yes         No
          │          │
          ▼          ▼
     Switch CH2   Report Fault
```

During the fault-oriented simulation, Channel 1 failure was simulated and the architecture switched to Channel 2.

This provided an opportunity to examine **failover logic and redundant communication control** at the RTL level.

---

## 6.4 Watchdog Supervision

A watchdog mechanism is included to monitor transmission activity.

The watchdog observes system activity using an internal time counter and can assert a timeout indication if transmission activity remains inactive beyond the defined threshold.

This addresses another common embedded-system design concern:

> **What happens if a communication process stops progressing?**

The watchdog provides a hardware-level mechanism for identifying such stalled conditions.

---

# 7. Performance Monitoring

The design incorporates internal performance-monitoring logic to make simulation behaviour measurable rather than relying only on visual waveform inspection.

The monitored parameters include:

* transmitted byte count
* received byte count
* packet latency
* throughput
* error count
* bit error rate
* maximum latency
* minimum latency

Latency is derived from simulation clock-cycle measurements, while throughput is calculated from transmitted/received data and measured simulation time.

This provides a useful verification perspective:

```text
Functional Verification
          +
Timing Observation
          +
Performance Measurement
          ↓
Quantitative System Evaluation
```

Rather than simply determining whether the design works, the simulation environment provides numerical evidence that can be used to compare different payload conditions.

---

# 8. Verification Methodology

The system was verified using **Vivado 2020.2** and a custom Verilog simulation environment.

The verification methodology followed a structured sequence:

```text
System Requirements
        ↓
RTL Architecture
        ↓
Module Development
        ↓
Simulation Stimulus
        ↓
Waveform Observation
        ↓
Console Analysis
        ↓
Performance Measurement
        ↓
Fault Scenario Evaluation
```

The verification focused on:

* data sequencing
* FIFO behaviour
* packet handling
* UART serialization
* checksum validation
* error handling
* redundancy switching
* performance counters
* latency
* throughput
* successful completion status

Two primary payload scenarios were evaluated.

---

# 9. Verification Case 1 — 12-Byte Transmission with Error Injection

The first test was designed to examine system behaviour under a controlled fault condition.

A 12-byte payload was transmitted, with an intentional error introduced at the fifth byte during simulation.

The verification scenario evaluated:

* USB-side data reception
* FIFO handling
* packet sequencing
* checksum mismatch detection
* retransmission behaviour
* redundancy switching
* final data reception
* performance measurement

### Reported Results

| Parameter               |           Result |
| ----------------------- | ---------------: |
| Payload                 |         12 bytes |
| Bytes Sent              |               12 |
| Bytes Received          |               12 |
| Error Injection         |              Yes |
| Redundancy              |        CH1 → CH2 |
| Error Count             | 0 after recovery |
| Average Latency         |     1,102,880 ns |
| Throughput              |       76.95 Kbps |
| Total Transmission Time |         1.247 ms |
| Status                  |           PASSED |

The simulation demonstrated that the controlled error condition was detected and that the communication flow continued through the recovery mechanism.

This test is particularly valuable because it evaluates the architecture beyond nominal data transfer.

---

# 10. Verification Case 2 — 100-Byte Transmission

The second test evaluated the system under a larger continuous payload without deliberate error injection.

A 100-byte incremental data pattern was used:

```text
00 01 02 03 ... 60 61 62 63
```

The objective was to examine:

* larger payload handling
* continuous FIFO activity
* packet sequencing
* data integrity
* transmission completion
* throughput
* latency
* resource behaviour

### Reported Results

| Parameter               |       Result |
| ----------------------- | -----------: |
| Payload                 |    100 bytes |
| Bytes Sent              |          100 |
| Bytes Received          |          100 |
| Error Injection         |           No |
| Error Count             |            0 |
| Average Latency         | 9,190,080 ns |
| Throughput              |   82.65 Kbps |
| Total Transmission Time |      9.68 ms |
| Status                  |       PASSED |

The simulation showed successful transfer of the complete 100-byte payload without reported overflow or transmission interruption.

The packet sequence and byte counters reached the expected final values, providing additional verification visibility.

---

# 11. Performance Summary

The two simulation scenarios provide a compact view of the observed system behaviour.

| Metric                | 12-Byte Test | 100-Byte Test |
| --------------------- | -----------: | ------------: |
| Payload               |         12 B |         100 B |
| Throughput            |   76.95 Kbps |    82.65 Kbps |
| Average Latency       |   1.10288 ms |    9.19008 ms |
| Errors After Recovery |            0 |             0 |
| Test Status           |         PASS |          PASS |

The reported results show that latency increases with payload size, which is consistent with the additional serialization time required for larger packets.

The measured throughput also remained relatively stable across the evaluated payload sizes.

These measurements are **simulation-derived results**, rather than measurements from a physically deployed communication link.

---

# 12. FPGA Resource Utilization

Synthesis results reported for the design were:

| FPGA Resource |  Used | Available | Utilization |
| ------------- | ----: | --------: | ----------: |
| LUT           | 5,695 |    41,000 |      13.89% |
| Flip-Flops    | 8,803 |    82,000 |      10.74% |
| DSP Blocks    |     5 |       240 |       2.08% |

The reported utilization indicates that the RTL design occupies a relatively small portion of the available FPGA resources.

From an engineering perspective, this leaves architectural headroom for potential future additions such as:

* additional communication channels
* more sophisticated error-handling mechanisms
* higher payload capacity
* configurable communication parameters
* additional monitoring logic

These possibilities remain future extensions rather than features claimed as part of the current implementation.

---

# 13. My Technical Work & Engineering Exposure

This project provided hands-on exposure to several areas of FPGA and embedded communication engineering.

My work and technical exposure covered the following areas:

### FPGA & RTL Design

* Verilog-based RTL development
* modular digital architecture
* synchronous hardware design
* FPGA resource considerations
* hardware-driven communication control

### Communication Interfaces

* USB host-side communication concepts
* Cypress FX2LP interface
* parallel data reception
* UART serialization
* RS-422 differential communication

### Data Handling

* FIFO buffering
* write/read pointer management
* flow-control signalling
* packet framing
* byte sequencing
* payload handling

### Reliability Engineering

* checksum-based integrity verification
* controlled fault injection
* retransmission
* retry management
* redundant-channel monitoring
* failover control
* watchdog supervision

### Verification

* Verilog testbench-based simulation
* waveform analysis
* console-based verification
* functional test scenarios
* timing observation
* throughput analysis
* latency measurement
* error-rate evaluation

### FPGA Design Tools

* Xilinx Vivado
* RTL simulation
* waveform analysis
* synthesis/resource analysis
* FPGA-oriented design methodology

---

# 14. Engineering Insights

One of the most important aspects of this project was understanding that communication-interface design is not simply a matter of converting one signal format into another.

A reliable communication subsystem requires coordination between:

**data representation, buffering, timing, framing, integrity, recovery, and verification.**

Several engineering concepts became particularly important during the development and verification process.

### Buffering Is Part of Communication Architecture

A communication bridge may receive data in bursts while transmitting it serially. FIFO buffering therefore becomes an important mechanism for managing differences between data arrival and transmission.

### Verification Must Include Fault Conditions

A design that works only under nominal conditions does not provide enough evidence of robustness.

Controlled error injection creates a repeatable way to evaluate error-detection and recovery logic.

### Redundancy Requires Control Logic

Adding a second communication path alone does not provide fault tolerance. The system needs health monitoring, channel selection, and deterministic switching behaviour.

### Performance Needs Measurement

Latency and throughput should be measured rather than described qualitatively. Internal counters and simulation-time measurements provide a mechanism for evaluating communication behaviour quantitatively.

### Simulation Is an Engineering Stage, Not the Final Deployment Stage

RTL simulation allows communication logic, timing relationships, error handling, and control behaviour to be examined before committing to physical integration.

This project therefore reinforced the importance of progressing systematically from **architecture and RTL verification toward future hardware validation**.

---

# 15. Research Perspective

Although this work was developed as an applied engineering project, it also provided exposure to several research-oriented questions in embedded communication systems.

The project sits at the intersection of:

* FPGA-based embedded systems
* digital communication
* hardware-software interface design
* fault-tolerant architectures
* real-time data handling
* communication reliability
* hardware verification
* programmable logic
* system-level performance analysis

The application-driven architecture also provides a foundation for exploring future questions such as:

* How can communication bridges be optimized for higher throughput?
* How can error-detection mechanisms be strengthened?
* How can multiple communication channels be coordinated dynamically?
* How can longer-duration stress testing be automated?
* How can physical-layer effects be incorporated into verification?
* How can FPGA communication architectures be extended toward adaptive or intelligent embedded systems?

These directions connect the project naturally to broader research interests in **embedded systems, resilient communication, real-time architectures, and intelligent edge platforms**.

---

# 16. Scope of the Current Work

The current work focuses on the **design and simulation-based verification** of the USB-to-RS-422 communication architecture.

The implemented and evaluated scope includes:

* USB data reception
* FPGA-based data handling
* FIFO buffering
* packet organization
* checksum-based integrity checking
* UART serialization
* RS-422 transmission representation
* controlled error injection
* retransmission logic
* redundant-channel control
* watchdog supervision
* performance monitoring
* RTL simulation
* waveform and console analysis
* resource utilization analysis

The project establishes a foundation for further hardware-level implementation and validation.

---

# 17. Limitations

The limitations of the present work are important to state explicitly.

### Simulation-Level Validation

The system was verified using RTL simulation rather than complete physical FPGA-board and RS-422 transceiver testing.

Therefore, the current work does not claim physical validation of:

* cable effects
* signal integrity
* electromagnetic interference
* temperature effects
* real transceiver behaviour
* long-distance physical-link performance

### Communication Configuration

The reported UART configuration uses a fixed baud rate of **115200 bps**. Dynamic baud-rate adaptation was not implemented as part of the current scope.

### Fault Coverage

The controlled error-injection scenario focused on a specific byte position. Multiple simultaneous errors, burst errors, and errors at arbitrary payload positions were not comprehensively characterized.

### Security

The communication path described in the report does not implement encryption or authentication. Therefore, this project should not be interpreted as a complete secure or classified communication system.

### Timing Verification

The reported timing analysis is based on functional simulation. Complete post-place-and-route timing closure and extensive physical timing characterization remain outside the present scope.

### Long-Duration Stress Testing

The evaluated payload cases were limited to the reported 12-byte and 100-byte scenarios. Extended-duration and substantially larger stress tests remain future work.

---

# 18. Future Engineering Directions

The project provides several natural directions for continued development:

### Hardware Validation

Implement the architecture on a physical Kintex-7/KC705 platform and evaluate the system with actual RS-422 transceiver hardware.

### Higher-Speed USB Data Handling

Explore DMA-based or more optimized USB data paths to improve sustained throughput.

### Extended Verification

Expand verification to include:

* larger payloads
* long-duration traffic
* variable packet sizes
* repeated fault injection
* multiple fault locations
* burst-error scenarios
* channel-failure combinations

### Enhanced Error Handling

Investigate stronger integrity and error-correction mechanisms beyond the current simulation-oriented checksum implementation.

### Multi-Channel Communication

Extend redundancy from dual-channel operation toward scalable multi-channel communication architectures.

### Hardware-Aware Verification

Introduce physical-layer considerations and post-synthesis/post-place-and-route timing analysis to bridge the gap between RTL-level functional verification and hardware deployment.

---

# 19. Public Portfolio Scope

This repository is intentionally structured as a **public-facing technical portfolio** rather than a complete reproduction of the internship implementation.

The purpose is to demonstrate:

* the engineering problem
* the architecture
* the RTL concepts
* the verification methodology
* the performance analysis
* the engineering reasoning
* the technical skills developed through the internship

Implementation-specific material that is not appropriate for public distribution is intentionally excluded.

The repository therefore focuses on **technical understanding and engineering evidence**, rather than exposing the complete original development environment or implementation.

---

# 20. Confidentiality & Responsible Sharing

This repository presents a public technical summary of work carried out during my internship at DRDO-RCI.

Only material considered appropriate for public portfolio presentation is included.

Original organization-specific implementation details, internal source material, project-specific confidential information, or other restricted artifacts are intentionally not reproduced here.

The purpose of this repository is to communicate the **engineering concepts, architecture, verification methodology, and technical experience** gained through the project while respecting professional and organizational boundaries.

---

# 21. Repository Structure

The public repository is intentionally kept concise:

```text
.
├── README.md
│
├── docs/
│   ├── system-architecture.png
│   ├── rtl-architecture.png
│   ├── verification-flow.png
│   └── technical-notes.md
│
├── verification/
│   ├── waveform-12-byte.png
│   ├── waveform-100-byte.png
│   └── simulation-results.md
│
└── .gitignore
```

No original project source code, testbench, or complete internship report is required for understanding the engineering contribution presented here.

The selected architecture diagrams and simulation evidence provide a concise public representation of the work.

---

# 22. Technical Skills Demonstrated

```text
FPGA Design
├── Verilog RTL
├── Synchronous Digital Design
├── Xilinx Kintex-7
└── Vivado

Embedded Communication
├── USB
├── Cypress FX2LP
├── UART
└── RS-422

Data Handling
├── FIFO
├── Packet Framing
├── Flow Control
└── Serialization

Reliability
├── Error Detection
├── Fault Injection
├── Retransmission
├── Redundancy
├── Failover
└── Watchdog

Verification
├── RTL Simulation
├── Testbench-Based Verification
├── Waveform Analysis
├── Performance Measurement
├── Latency Analysis
└── Throughput Analysis
```

---

# 23. Why This Project Matters to My Engineering Journey

This internship provided an opportunity to move beyond textbook descriptions of embedded communication interfaces and work through the architecture of an FPGA-based communication system.

The project brought together several layers of embedded-system engineering:

```text
Communication Standards
        ↓
System Requirements
        ↓
Architecture
        ↓
RTL Implementation
        ↓
Fault Handling
        ↓
Simulation
        ↓
Performance Analysis
```

Working across these layers helped strengthen my understanding of how low-level digital logic contributes to complete embedded communication systems.

The experience also reinforced my interest in systems where **hardware architecture, communication reliability, real-time behaviour, and intelligent control** intersect.

---

# 24. Closing Summary

This project explored an FPGA-based approach to bridging a host-oriented USB interface with an RS-422 communication path.

The resulting architecture combines:

**USB reception + FIFO buffering + packet handling + checksum-based integrity checking + UART serialization + RS-422 transmission + redundancy + retransmission + watchdog supervision + performance monitoring**

within a programmable-logic framework.

The design was evaluated through RTL simulation using Vivado 2020.2 across both normal and controlled fault scenarios.

The 12-byte fault-injection test demonstrated the ability to detect and recover from a controlled integrity error while exercising redundant-channel failover. The 100-byte transmission test demonstrated successful larger-payload handling with stable reported throughput and zero reported errors.

The project therefore represents a practical exploration of **FPGA-based embedded communication, RTL design, verification, reliability engineering, and system-level performance analysis**.

It also establishes a foundation for future work involving physical FPGA implementation, real RS-422 transceiver validation, extended stress testing, enhanced error-handling mechanisms, and scalable redundant communication architectures.

---

### Technical Focus

**FPGA • Verilog • RTL Design • Embedded Systems • USB • Cypress FX2LP • UART • RS-422 • FIFO • Packet Framing • Error Detection • Fault Injection • Retransmission • Redundancy • Failover • Watchdog • Simulation • Verification • Performance Analysis**
