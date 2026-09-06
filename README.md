# FPGA-Based USB to Serial Communication

## 🔒 Confidentiality Notice
This repository serves as a **sanitized engineering portfolio** documenting my work on FPGA-based communication systems during my Master's research internship at **Defence Research and Development Organisation (DRDO) - Research Centre Imarat (RCI)**.

Due to strict DRDO security policies, the original RTL source code, internal schematics, and full project documentation are **classified and cannot be published**. The technical descriptions, architectural diagrams, and independently reconstructed Verilog snippets provided here are limited to information suitable for public academic presentation and do **not** represent the original confidential project source code.

## 📌 Project at a Glance
| Category          | Details                                               |
| ----------------- | ---------------------------------------------------- |
| **Context**       | DRDO Research Internship (Sept 2025 - Feb 2026)       |
| **Domain**        | Embedded Systems & Digital Design                     |
| **Application**   | USB-to-Serial Communication                           |
| **USB Interface** | Cypress FX2LP                                         |
| **FPGA Platform** | Xilinx Kintex-7 (KC705)                               |
| **HDL**           | Verilog                                               |
| **Communication** | RS-422 (Differential Serial)                          |
| **Core Concepts** | FIFO, USB Interface, FPGA Logic, Serial Communication |
| **My Role**       | FPGA-side development & verification                  |

## 🧠 Engineering Problem
Modern computing systems (like PCs) use USB for high-speed, short-range data transfer. However, industrial, aerospace, and defense equipment often rely on RS-422, a robust differential serial standard that supports long-distance communication and high noise immunity.

The core challenge was to **bridge these two fundamentally different protocols** using an FPGA. USB is host-controlled, high-speed, and parallel in nature, while RS-422 is deterministic, serial, and requires precise bit-level timing. The system must ensure that data flows reliably between these domains without loss, corruption, or timing violations, making it suitable for mission-critical applications.

## 🔬 Sanitized Data Path Architecture
The FPGA acts as the central processing unit, managing data reception, buffering, packet framing, error detection, and serialization.

```text
USB Host
   ↓
Cypress FX2LP Controller (USB to FIFO Bridge)
   ↓
[ 8-bit Parallel Data Interface ]
   ↓
FPGA: Dual-Clock Asynchronous FIFO
   ↓
FPGA: Packet Framing (Start/End Markers & Length)
   ↓
FPGA: Checksum Engine (Error Detection)
   ↓
FPGA: UART Transmitter (115200 baud)
   ↓
RS-422 Transceiver (Differential Signaling)
   ↓
Legacy Defense / Industrial Device
