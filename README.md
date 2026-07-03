# Parametrized Fixed-Point Math Core Library (Verilog-2001 / SystemVerilog)

[![Platform](https://img.shields.io/badge/Platform-FPGA%20%7C%20ASIC-blue)](https://github.com/vortexxip-deeptech)
[![Target](https://img.shields.io/badge/Target-AMD%20Xilinx%20Artix--7%20%2F%207--Series-orange)](https://github.com/vortexxip-deeptech)
[![License](https://img.shields.io/badge/License-MIT-green)](LICENSE)

A production-grade, fully synthesizable, and highly parametrizable Fixed-Point Arithmetic Core (ADD, SUB, MUL, DIV) written in clean Verilog-2001. Designed specifically for resource-constrained edge-compute hardware and FPGA fabrics (such as AMD Xilinx Artix-7) where conserving dedicated DSP blocks and Look-Up Tables (LUTs) is critical.

---

## ⚡ Commercial Hub & Upgrades
> 🚀 **Looking for Production-Ready Verification Suites or Enterprise Integration?**
>
> This repository contains the fully functional baseline implementation. If you require **extended hardware verification compliance**, **UVM testbench environments**, or **commercial licensing rights**, check out our tiered packages on Gumroad:
> 
> * **[Get the Enterprise Tier Package on Gumroad](https://vortexxip.gumroad.com)** — Includes full UVM environment, pre-compiled Vivado IP sizing/constraints scripts (TCL), AXI4-Lite wrappers, and 24/7 dedicated deployment SLA.

---

## 🛠️ Key Architectural Features
- **Deterministic Latency Framework**: Fully synchronous, clocked design using active-low resets to avoid global routing fan-out stress and guarantee predictable timing closure.
- **DSP-Free Optimization**: 
  - **Multiplication**: Implements a sequential pipelined shift-and-add architecture (1 bit/cycle) to infer standard LUT/FF fabric instead of forcing expensive vendor DSP48E1 blocks.
  - **Division**: Implements a classic non-restoring shift-and-subtract hardware divider, bypassing behavioral `/` operators that fail synthesis or bloat gate count.
- **Industry Standard Handshaking**: Uses robust `start`, `ready`, and `valid` control signaling for seamless clock-domain and data-bus integration.
- **Math Precision**: Operates on Sign-Magnitude conversion internally to prevent asynchronous critical path delays.

## 📊 Core Specifications

| Param | Description | Default Value |
| :--- | :--- | :--- |
| `TOTAL_WIDTH` | Total bit-width of operands and outputs | `32` |
| `FRACTIONAL_WIDTH` | Number of fractional bits assigned (Q-format) | `16` (Q16.16) |

### Approximate Latency Profile:
- **ADD / SUB**: 2 Clock Cycles
- **MUL**: `TOTAL_WIDTH` + 2 Clock Cycles
- **DIV**: `TOTAL_WIDTH` + `FRACTIONAL_WIDTH` + 2 Clock Cycles

---

## 📁 Repository Structure
- `fp_math_core.v` : Synthesizable RTL core module implementing core mathematical finite state machines (FSM).
- `tb_fp_math_core.sv` : Self-checking behavioral SystemVerilog testbench utilizing randomized stimulus generators (`std::randomize()`) and quantization-aware golden reference scoring models.

---

## 🚀 Simulation & Verification
The test suite drives both directed boundary vectors (zeros, absolute fractional splits, maximum boundaries) and randomized distributions to validate arithmetic precision within a $2/\text{LSB}$ error tolerance.

### To Run Simulation (Using Xilinx Vivado / ModelSim / Verilator):
```bash
# Example compile script for standard tools
vlog -sv tb_fp_math_core.sv fp_math_core.v
vsim -c tb_fp_math_core -do "run -all; quit"
