# Verification Summary

> **Important Note:** Original simulation waveforms, detailed testbench implementation, and other project-specific artifacts are not included in this repository. The following is a sanitized summary of the verification methodology and reported simulation results.

---

## Verification Methodology

The RTL design was verified using **Vivado 2020.2** simulation with a custom Verilog testbench. Controlled USB input data was applied to the design, while system behavior was observed through simulation waveforms and console logging.

The implemented RTL operated with a **48 MHz system clock** and an active-low reset.

The verification process evaluated functional data transfer, error handling, redundancy switching, retransmission behavior, and performance-related measurements.

---

## Test Case 1 — 12-Byte Transmission with Error Injection

### Scenario

* **Payload:** 12 bytes
* **Error injection:** Enabled
* **Fault-handling mechanisms:** Error detection, retransmission and redundancy switching

### Purpose

This test evaluated the behavior of the communication architecture under a controlled fault condition, including error detection, recovery and redundant-channel operation.

### Reported Results

| Parameter               |                 Result |
| ----------------------- | ---------------------: |
| Bytes Sent              |                     12 |
| Bytes Received          |                     12 |
| Error Count             |       0 after recovery |
| Redundancy Activation   |        Yes — CH1 → CH2 |
| Average Latency         | 1,102,880 ns (~1.1 ms) |
| Throughput              |             76.95 Kbps |
| Total Transmission Time |               1.247 ms |
| Test Status             |             **PASSED** |

### Observation

The simulated fault condition triggered the documented error-handling and recovery mechanisms. The transmission completed with the expected number of bytes received, while the redundancy mechanism switched from Channel 1 to Channel 2 during the simulated failure condition.

---

## Test Case 2 — 100-Byte Transmission under Normal Operation

### Scenario

* **Payload:** 100 bytes
* **Error injection:** Disabled
* **Operating condition:** Normal simulated transmission

### Purpose

This test evaluated data transfer behavior and performance for a larger payload without intentional fault injection.

### Reported Results

| Parameter               |                  Result |
| ----------------------- | ----------------------: |
| Bytes Sent              |                     100 |
| Bytes Received          |                     100 |
| Error Count             |                       0 |
| Redundancy Activation   |                      No |
| Average Latency         | 9,190,080 ns (~9.19 ms) |
| Throughput              |              82.65 Kbps |
| Total Transmission Time |                 9.68 ms |
| Test Status             |              **PASSED** |

### Observation

The complete 100-byte payload was successfully transmitted and received in simulation with zero reported errors. The test also provided the basis for the reported throughput and latency measurements.

---

## Performance Summary

| Metric          |     12-Byte Test | 100-Byte Test |
| --------------- | ---------------: | ------------: |
| Throughput      |       76.95 Kbps |    82.65 Kbps |
| Average Latency |          ~1.1 ms |      ~9.19 ms |
| Error Count     | 0 after recovery |             0 |
| Test Status     |       **PASSED** |    **PASSED** |

> **Note:** The reported performance values are simulation-based measurements from the documented verification scenarios.

---

## FPGA Resource Utilization

The reported synthesis results showed the following resource usage:

| Resource   |  Used | Available | Utilization |
| ---------- | ----: | --------: | ----------: |
| LUT        | 5,695 |    41,000 |      13.89% |
| Flip-Flops | 8,803 |    82,000 |      10.74% |
| DSP Blocks |     5 |       240 |       2.08% |

The reported utilization indicates that the implemented design used a relatively small fraction of the available FPGA resources, leaving resource headroom within the evaluated configuration.

---

## Verification Summary

The reported simulation results demonstrated:

* Successful bidirectional communication-path verification.
* Controlled error-injection and recovery behavior.
* Redundant-channel failover from Channel 1 to Channel 2 under the simulated fault condition.
* Retransmission and watchdog-related verification.
* Successful 12-byte and 100-byte transmission scenarios.
* Measured simulation-based latency and throughput.
* Relatively low reported FPGA resource utilization.

### Validation Scope

The results presented here represent **RTL simulation and synthesis-level evaluation** documented in the project report. They should not be interpreted as physical field validation or clinical/operational deployment testing.

Original source code, detailed testbench implementation, internal project artifacts and other non-public material are intentionally excluded from this repository.

