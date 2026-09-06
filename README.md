# Application-Driven Development for USB-to-RS-422 Conversion

### FPGA-Based Communication Architecture • Embedded Systems • RTL Design • Fault-Tolerant Systems • Simulation & Verification

> **A simulation-verified FPGA-based communication architecture developed during my DRDO-RCI internship to bridge a USB host interface with an RS-422 serial communication link, with emphasis on structured data handling, buffering, error detection, redundancy, retransmission, deterministic timing, and verification.**

---

## 🔒 Confidentiality Notice
This repository presents a public technical summary of work carried out during my internship at DRDO-RCI. Original implementation details, internal source material, and confidential project artifacts are **intentionally not reproduced here**.

---

## 🏆 DRDO Internship Selection (Paid Internship Scheme)
I was selected through a **merit-based national selection process** for the **DRDO 2025 Paid Internship Scheme** at **Research Centre Imarat (RCI)**, Hyderabad. 

This is one of India's premier defense research organizations, and I was honored to work directly under the guidance of a **Scientist 'G'** from **September 2025 to February 2026**. 

---

## 📌 Project at a Glance

| Category                      | Details                                                               |
| ----------------------------- | --------------------------------------------------------------------- |
| **Internship Organization**   | DRDO – Research Centre Imarat (RCI), Hyderabad                        |
| **Project**                   | Application-Driven Development for USB to RS-422 Conversion           |
| **Domain**                    | Embedded Systems / FPGA / Digital Communication                       |
| **Primary Platform**          | Xilinx Kintex-7 / KC705                                               |
| **USB Interface**             | Cypress FX2LP                                                         |
| **HDL**                       | Verilog                                                               |
| **System Clock Used in RTL**  | 48 MHz                                                                |
| **Serial Interface**          | RS-422                                                                |
| **UART Baud Rate**            | 115200 bps                                                            |
| **Buffering**                 | 1 KB FIFO architecture                                                |
| **Integrity Mechanism**       | 32-bit XOR-based checksum                                             |
| **Reliability Features**      | Error injection, retransmission, redundancy/failover, watchdog        |
| **Primary Engineering Focus** | Reliable and controlled interface conversion using programmable logic |

---

## 💼 My Technical Work
- **FPGA-side development:** Worked on the logic and architecture for USB-to-serial data flow.
- **Verilog-based design:** Developed RTL modules for data handling, buffering, and serialization.
- **FIFO implementation:** Implemented dual-clock FIFO buffering to handle safe clock-domain crossing.
- **Communication protocols:** Implemented UART serialization (115200 baud) and understood differential RS-422 signaling.
- **Error detection:** Implemented a 32-bit XOR checksum for data integrity verification.
- **Redundancy control:** Worked on automatic channel failover logic to ensure fault tolerance.
- **Verification & debugging:** Conducted simulation-based validation, error-injection testing, and system-level debugging.

---

## 📐 RTL Architecture
The FPGA design is organized into modular functional blocks:

### 1. USB Receiver Interface
- Captures 8-bit parallel data from the Cypress FX2LP controller.
- Manages data validity and flow control signals.
- Handles burst-mode data reception without loss.

### 2. Dual-Clock FIFO (1KB)
- Provides safe clock-domain crossing between the 48 MHz USB domain and internal processing.
- Prevents overflow and underflow conditions.
- Ensures continuous data flow during asynchronous clock operation.

### 3. Packet Framer
- Structures raw data with Start-of-Frame (SOF) and End-of-Frame (EOF) markers.
- Supports variable payload sizes.
- Maintains packet boundary identification.

### 4. 32-bit XOR Checksum Engine
- Provides lightweight integrity verification.
- Detects corrupted packets during verification.
- Generates status signals indicating match or mismatch.

### 5. UART Transmitter
- Serializes packets at 115200 baud with start/stop bits.
- Generates deterministic bit timing using FPGA clock division.
- Drives differential RS-422 output signals.

### 6. Redundancy Controller
- Monitors channel health (CH1/CH2).
- Performs automatic failover upon channel failure.
- Ensures continuous transmission during fault conditions.

### 7. Performance Monitor & Watchdog
- Tracks metrics (latency, throughput, errors).
- Detects stalled transmissions via watchdog timeout.
- Provides console-based simulation reporting.

---

## ✅ Verification Summary
RTL functionality was evaluated through simulation using a custom Verilog testbench. Verification covered normal data transmission, controlled error injection, retransmission behavior, redundant-channel switching, and performance measurements.

**Key Metrics:**
- **12-byte test (Error injection):** 100% data integrity, 76.95 Kbps throughput, 1.1 ms latency, **PASSED**
- **100-byte test (Normal):** 100% data integrity, 82.65 Kbps throughput, 9.19 ms latency, **PASSED**
- **FPGA Utilization:** 14% LUTs, 11% Flip-Flops

---

## 🛡️ Fault-Tolerance Mechanisms
- **Error Injection:** Controlled fault injection was used in simulation to validate error detection and recovery.
- **Retransmission:** Supports up to 3 retry attempts upon error detection.
- **Redundant Channels:** Automatic switching from Channel 1 to Channel 2 during simulated failure.
- **Watchdog Timer:** Resets the system if transmission activity stalls.

---

## 📊 Detailed Verification Results

### Test Case 1: 12-Byte Transmission (Error Injection)
- **Payload:** 12 bytes
- **Error Injection:** Yes
- **Redundancy Activation:** Yes (CH1 → CH2)
- **Data Integrity:** 100%
- **Average Latency:** 1.1 ms
- **Throughput:** 76.95 Kbps
- **Status:** **PASSED**

### Test Case 2: 100-Byte Transmission (Normal Operation)
- **Payload:** 100 bytes
- **Error Injection:** No
- **Data Integrity:** 100%
- **Average Latency:** 9.19 ms
- **Throughput:** 82.65 Kbps
- **Status:** **PASSED**

---

## 📈 FPGA Resource Utilization

| Resource | Used | Available | Utilization |
| :--- | :--- | :--- | :--- |
| LUT | 5,695 | 41,000 | 13.89% |
| Flip-Flops | 8,803 | 82,000 | 10.74% |
| DSP Blocks | 5 | 240 | 2.08% |

---

## 🎓 Why This Matters for Research
This project allowed me to apply core embedded systems theory to a real-world, mission-critical application. The hands-on experience with:
- **FPGA-based digital design**
- **High-speed data communication**
- **Fault-tolerant system architecture**
- **Hardware verification and simulation**

has provided me with a strong foundation for advanced research in **Intelligent Embedded Systems, Real-Time Communication Protocols, and Reliable IoT Architectures**, which I aim to explore further in my doctoral studies.

---

## 📂 Repository Structure
```text
DRDO-USB-to-RS422-Conversion/
│
├── README.md                    # Project summary (You are here)
│
└── docs/
    ├── technical-notes.md       # Full 24-section detailed engineering report
    ├── verification-summary.md  # Verification results & methodology
    └── diagrams/                # Sanitized architecture diagrams

## 📚 Full Engineering Report
For the complete, 24-section detailed engineering report (including System Architecture, RTL Design, Verification Methodology, Fault-Tolerance Mechanisms, Limitations, and Future Scope), **click here: [docs/technical-notes.md](docs/Technical-Notes.md)**

---

## 🧑‍💻 Author
**Khatija Mahveen**
- M.E. Embedded Systems & IoT | PhD Aspirant
- Research Intern at DRDO - Research Centre Imarat (RCI)
- Gold Medalist (First Rank in M.E. Embedded Systems)

## 📜 License
This project is licensed for academic and research purposes.
