# Day 4: GLS, Blocking vs Non‑Blocking, and Sim–Synth Mismatch
This document explores critical aspects of digital design verification, focusing on post-synthesis validation, Verilog coding practices, and common design pitfalls encountered in VLSI workflows.

**Note:** Click the sections below to expand.

## Core Concepts 

<details>
<summary><strong>1. Gate‑Level Simulation (GLS)</strong></summary>

### What is Gate-Level Simulation?

Gate-Level Simulation (GLS) represents a crucial verification methodology where we simulate the synthesized netlist rather than the original RTL description. This process validates that the synthesis tool has correctly interpreted our design intent.

### Core Benefits of GLS

**Verification Objectives:**
- Confirm logical equivalence between RTL and netlist
- Validate timing constraints and signal propagation
- Verify power consumption estimates
- Test manufacturability features (DFT structures)

**Implementation Stages:**
- **Pre-layout GLS**: Functional verification with estimated delays
- **Post-layout GLS**: Accurate timing simulation with extracted parasitics

### GLS Workflow Integration

```
RTL Design → Synthesis → Gate-Level Netlist → GLS Verification → Physical Design
```

<img width="1069" height="720" alt="image" src="https://github.com/user-attachments/assets/6e23346c-f58d-472e-a2a6-87a521b8930d" />

---

</details open>

<details>
<summary><strong>2. Synthesis–Simulation Mismatch</strong></summary>

### Understanding Synthesis-Simulation Divergence

**Root Causes of Mismatches:**

1. **Language Construct Issues**
   - Simulation-only constructs (delays, initial blocks)
   - Non-synthesizable behavioral descriptions
   - Ambiguous timing specifications

2. **Coding Style Problems**
   - Incomplete case statements
   - Missing sensitivity list entries
   - Improper reset handling

3. **Tool Interpretation Variations**
   - Different optimization strategies
   - Vendor-specific synthesis rules
   - Library mapping differences

### Prevention Strategies

- Adopt synthesizable coding guidelines
- Maintain consistent simulation environments
- Validate with multiple tool chains
- Implement comprehensive testbenches

<img width="1283" height="925" alt="image" src="https://github.com/user-attachments/assets/c378766f-9525-475d-a7b6-c6b26b5cc4fc" />

---

</details>

<details>
<summary><strong>3. Blocking vs Non‑Blocking</strong></summary>

### Blocking Assignment Characteristics

**Operator:** `=`
**Execution Model:** Sequential, immediate evaluation
**Primary Use Cases:** Combinational logic implementation

```verilog
// Combinational logic example
always @(*) begin
    temp = input_a & input_b;
    output_signal = temp | input_c;
end
```

### Non-Blocking Assignment Properties

**Operator:** `<=`
**Execution Model:** Concurrent, end-of-timestep evaluation
**Primary Use Cases:** Sequential logic (registers, state machines)

```verilog
// Sequential logic example
always @(posedge clock) begin
    register_stage1 <= input_data;
    register_stage2 <= register_stage1;
end
```

### Comparison

| Blocking (=) | Non‑Blocking (<=) |
|---|---|
| Immediate update within the active region | Deferred update at end of timestep |
| Order matters inside a block | Order largely irrelevant across flops |
| Best in always @(*) combinational | Best in @(posedge …) sequential |
| Can race if misused for flops | Avoids x‑prop/race across registers |

<img width="1080" height="815" alt="image" src="https://github.com/user-attachments/assets/aa4b84e9-5f87-4e77-95b5-14ab28f45ab1" />

---

</details>

<details>
<summary><strong> 4. Caveats with Blocking Statements</strong></summary>

### The Order Dependency Problem

Blocking statements execute in the order they appear in the code, which can lead to unexpected behavior:

```verilog
// Problematic ordering
always @(*) begin
    y = x & enable;    // Uses old value of x
    x = a | b;         // Updates x after y calculation
end
```

In this example, `y` is calculated using the previous value of `x`, not the newly computed value.

### Corrected Implementation

```verilog
// Proper ordering
always @(*) begin
    x = a | b;         // Calculate intermediate signal first
    y = x & enable;    // Use updated value of x
end
```

### Best Practices for Blocking Statements

1. **Calculate Dependencies First**: Always compute intermediate values before using them
2. **Use Meaningful Names**: Clear variable names help identify dependencies
3. **Minimize Intermediate Variables**: Reduce complexity where possible
4. **Group Related Logic**: Keep related calculations together

<img width="2048" height="1536" alt="image" src="https://github.com/user-attachments/assets/3f3917d6-97db-49f3-98a8-0f656327335f" />


</details>

</details>

## Practical Implementation Labs

<details>
<summary><strong>Lab 1: Ternary MUX (RTL)</strong></summary>

### Verilog Code:

```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```

<img width="1919" height="712" alt="image" src="https://github.com/user-attachments/assets/47b22876-bce1-4361-b266-8f7a483f41a5" />

### Commands:

```tcl
iverilog ternary_operator_mux.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```
<img width="1919" height="928" alt="image" src="https://github.com/user-attachments/assets/27619f96-c28a-4d7f-a095-ae83d5e2012e" />

## Output:
<img width="1919" height="776" alt="image" src="https://github.com/user-attachments/assets/b3b18132-9c1f-412c-96af-17201c0ecf85" />


**Functional Description:** Output follows `i1` when `sel` is high, otherwise follows `i0`.

</details>

<details>
<summary><strong>Lab 2: Synthesize with Yosys</strong></summary>

## Synthesize the above MUX using Yosys.

```tcl
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog ternary_operator_mux.v  
opt_clean -purge
synth -top ternary_operator_mux
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
write_verilog -noattr ternary_operator_mux_net.v
show
```

**Objective:** Transform RTL description into gate-level implementation

## Output:
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/1e411fe0-8cfe-47e2-aa33-4cc30868ea02" />

</details>

<details>
<summary><strong>Lab 3: Functional GLS</strong></summary>

Compile with standard cell models and primitives (example Sky130 paths):
```tcl
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

**Verification Setup:** Compare RTL and gate-level simulation results

<img width="1919" height="903" alt="image" src="https://github.com/user-attachments/assets/e9affa36-495f-4940-8056-cd8cd0340b9b" />


## Output:
<img width="1919" height="682" alt="image" src="https://github.com/user-attachments/assets/a36384fb-fe24-4828-8514-41218cc521dc" />

</details>

<details>
<summary><strong>Lab 4: Bad MUX </strong></summary>

### Verilog Code:

```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @(sel) begin
    if (sel) y <= i1; else y <= i0;
  end
endmodule
```

**Faulty Implementation:** Incomplete sensitivity list demonstration

**Issues Identified:**
- Sensitivity list omits `in0` and `in1`
- Non-blocking assignments in combinational context
- Potential simulation-synthesis mismatch

## Waveform for **Bad_Mux** 

<img width="1916" height="646" alt="image" src="https://github.com/user-attachments/assets/fbd4e396-aa18-44d4-9a22-bda2b0dbd22a" />

## Synthesized Bad_Mux

<img width="1919" height="882" alt="image" src="https://github.com/user-attachments/assets/1cc2a816-6ca6-44a4-b09c-87aa671b6fdf" />

Fix:
```verilog
always @(*) begin
  if (sel) y = i1; else y = i0;
end
```
</details>

<details>
<summary><strong>Lab 5: GLS on Bad Mux</strong></summary>

## Commands

```tcl
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v bad_mux_net.v tb_bad_mux.v
./a.out
gtkwave tb_bad_mux.vcd
```

<img width="1919" height="703" alt="image" src="https://github.com/user-attachments/assets/9967d777-023f-4aab-b2a5-b279b7728ad7" />

## GLS of Bad_Mux
<img width="1918" height="682" alt="image" src="https://github.com/user-attachments/assets/5804b896-7e0f-4023-8949-8f0c0ac01ba3" />

</details>

<details>
<summary><strong>Lab 6: Blocking Order Caveat</strong></summary>

### Verilog Code:

```verilog
module blocking_caveat (input a , input b , input  c, output reg d); 
reg x;
always @ (*)
begin
	d = x & c;
	x = a | b;
end
endmodule
```

<img width="1919" height="685" alt="image" src="https://github.com/user-attachments/assets/7585df1a-9754-4cec-b3ea-83bc27ceff50" />

**Root Cause:** Variable used before assignment in blocking context

**Solution:** Reorder assignments for correct dependency

## Waveform Generated

<img width="1919" height="673" alt="image" src="https://github.com/user-attachments/assets/6179afee-eea5-4bf9-af61-7995704c7db6" />


</details>

<details>
<summary><strong>Lab 7: Synthesize</strong></summary>

## Synthesized Design

<img width="1919" height="1021" alt="image" src="https://github.com/user-attachments/assets/e4591cfe-a5fe-4e2b-a623-1b6d876b89db" />
**Objective :** Compare the GLS and RTL simulation 

## GLS:

<img width="1919" height="742" alt="image" src="https://github.com/user-attachments/assets/926e9b53-1ae6-477e-97de-a614dd64388b" />

</details>

</details>

## Key Learning Outcomes

- GLS validates netlist functionality and timing intent; use functional GLS early and timing GLS (SDF) when supported.  
- Prevent sim–synth mismatches with complete sensitivity, explicit defaults, and synthesizable, unambiguous RTL.  
- Use blocking for combinational and non‑blocking for sequential, avoiding mixes within the same always block.

**Workshop Progress**: Day 4 Complete | Next: Optimization in Synthesis
