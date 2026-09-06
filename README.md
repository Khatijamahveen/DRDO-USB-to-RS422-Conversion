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
