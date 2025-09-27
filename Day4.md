# Day 4: Gate-Level Simulation (GLS), Blocking vs. Non-Blocking in Verilog, and Synthesis-Simulation Mismatch

This session explores three interconnected aspects of digital design verification: Gate-Level Simulation (GLS), Verilog assignment semantics, and synthesis-simulation mismatches.

---

## 1. Gate-Level Simulation (GLS) Fundamentals

GLS is a crucial post-synthesis verification step where the synthesized **netlist** is validated against actual gate primitives and interconnect delays. It represents a realistic verification of the design in silicon.

### Purpose and Significance

GLS ensures comprehensive validation:

* **Functional Accuracy:** Verifies synthesis correctly translated the RTL.
* **Timing Verification:** Uses **SDF (Standard Delay Format)** annotation to check for setup and hold time violations.
* **Power & Testability:** Provides switching data for power analysis and confirms **DFT** (scan chains) operation.

### Implementation Flow

The process involves: Netlist generation $\rightarrow$ **Delay Annotation** (SDF) $\rightarrow$ Testbench reuse $\rightarrow$ Comparison analysis.

---

## 2. Assignment Semantics in Verilog

The choice between blocking and non-blocking assignments dictates hardware inference and simulation behavior.

### Blocking Assignment (`=`)

* **Execution:** Sequential, with immediate evaluation. Each statement completes before the next begins.
* **Appropriate Usage:** **Combinational logic** (`always @(*)`) and temporary variables within procedural code.
* **Characteristic:** **Order-dependent** behavior.

### Non-Blocking Assignment (`<=`)

* **Execution:** Deferred; all right-hand side expressions are evaluated, but updates occur **concurrently at the end of the time step**.
* **Appropriate Usage:** **Sequential logic** (registers/flip-flops) in clocked blocks (`always @(posedge clk)`).
* **Characteristic:** **Order-independent** behavior.

| Assignment | Operator | Execution | Hardware Inference |
| :--- | :--- | :--- | :--- |
| **Blocking** | `=` | Immediate (Sequential) | Combinational (Gates) |
| **Non-Blocking**| `<=` | Scheduled (Concurrent) | Sequential (Flip-Flops) |

---

## 3. Synthesis-Simulation Mismatch Analysis

Mismatches occur when simulation and synthesis tools interpret Verilog differently.

### Root Causes

1.  **Sensitivity List Incompleteness:** Simulators strictly observe the list, while synthesis infers the full list, causing behavioral divergence (e.g., using `always @(sel)` instead of `always @(*) `for combinational logic).
2.  **Assignment Ordering Issues:** Blocking assignments create dependencies that synthesis might optimize differently.
3.  **Initialization/Reset Handling:** Reliance on implicit initialization in simulation that is not translated to synchronous resets in synthesis.

### Prevention Strategies

* Use `always @(*)` for all combinational blocks.
* Use **blocking (`=`) exclusively for combinational logic**.
* Use **non-blocking (`<=`) consistently for sequential logic**.
* Implement comprehensive, synchronous reset mechanisms.

---

## Practical Examples (Pitfalls & Resolutions)

### Problem 1: Bad MUX Pattern (Combinational Logic)

| Problematic Code | Issue | Corrected Code |
| :--- | :--- | :--- |
| `always @(sel) begin y <= i1; ...` | Incomplete sensitivity list; Non-blocking (`<=`) inappropriate for combinational logic. | `always @(*) begin y = i1; ...` |

### Problem 2: Assignment Order Dependency (Blocking Caveat)

| Problematic Code | Issue | Resolution |
| :--- | :--- | :--- |
| `d = intermediate & c; intermediate = a | b;` | `d` uses the stale (previous) value of `intermediate` because blocking updates are immediate and sequential. | Compute `intermediate` first, then use it: `intermediate = a | b; d = intermediate & c;` |

---

## Summary

**GLS** bridges the gap between RTL abstraction and silicon reality. **Disciplined coding**—using blocking assignments for combinational logic and non-blocking for sequential logic—is essential to prevent **synthesis-simulation mismatches** and ensure a robust VLSI implementation.
