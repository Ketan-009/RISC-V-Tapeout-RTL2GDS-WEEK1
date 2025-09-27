# Day 3: Advanced Circuit Optimization Techniques

This guide explores optimization methodologies for combinational and sequential digital circuits, focusing on industry-standard techniques that enhance performance and efficiency in VLSI design.

**Note :** Click the sections below to expand.

## Core Concepts

<details>
<summary>1. Introduction to Optimization</summary>
	
Digital circuit optimization is fundamental to achieving high-performance, power-efficient designs in modern VLSI systems.

**Primary Optimization Goals:**
- **Area Reduction:** Minimize gate count and silicon footprint
- **Timing Enhancement:** Reduce critical path delays  
- **Power Efficiency:** Lower static and dynamic power consumption
- **Design Simplification:** Eliminate redundant logic structures

<img width="330" height="231" alt="image" src="https://github.com/user-attachments/assets/6bc91d34-b9cb-45e0-9095-6ede1d3222dd" />

</details>

---

<details>
<summary>2. Constant Propagation Methodology</summary>

Constant propagation identifies and replaces variables holding constant values throughout the design's execution, significantly reducing logic complexity.

**Implementation Process:**
1. **Analysis Phase:** Examine data flow to identify invariant signals
2. **Substitution Phase:** Replace constant variables with their literal values
3. **Logic Reduction:** Simplify Boolean expressions using constant values
4. **Verification Phase:** Ensure functional equivalence post-optimization

**Example:**
```
Before: A = 1, B = variable, Y = A & B
After:  Y = B (A completely removed)
```

**Key Advantages:**
- Hardware reduction and speed enhancement
- Power savings through reduced switching activity
- Simplified routing with fewer nets

<img width="475" height="313" alt="image" src="https://github.com/user-attachments/assets/838790c7-f5b6-40c6-8404-5df258d7a756" />

</details>

---

<details>
<summary>3. State Machine Optimization</summary>

FSM optimization encompasses techniques to streamline state-based designs while maintaining behavioral correctness.

**Optimization Strategies:**

### State Minimization
- **Equivalent State Identification:** Detect states with identical output behavior
- **State Merging:** Combine functionally equivalent states
- **Unreachable State Elimination:** Remove inaccessible states

### Encoding Optimization
- **Binary Encoding:** Minimum bits for state representation
- **One-Hot Encoding:** Single bit active per state (faster transitions)
- **Gray Code Encoding:** Minimal bit changes between states

**Benefits:** Reduced flip-flop count, simplified logic, lower power consumption

<img width="259" height="259" alt="image" src="https://github.com/user-attachments/assets/f34ad161-2906-4b13-acc1-1938e74e2a67" />

</details>

---

<details>
<summary>4. Logic Cloning Techniques</summary>

Logic cloning strategically duplicates circuit elements to optimize system performance, particularly for timing violations and load balancing.

**Applications:**

### Load Distribution
```
Original: One driver → Multiple loads (high fanout)
Optimized: Multiple drivers → Distributed loads
```

**Implementation Steps:**
1. **Critical Path Analysis:** Identify performance bottlenecks
2. **Fanout Analysis:** Locate high-fanout nets causing delays
3. **Strategic Duplication:** Clone appropriate logic blocks
4. **Load Redistribution:** Balance electrical loading

**Trade-offs:** Performance gain vs. increased area and power

<img width="355" height="303" alt="image" src="https://github.com/user-attachments/assets/84914474-d7db-415a-b9ab-2cb8bfe437c0" />

</details>

---

<details>
<summary>5. Register Retiming</summary>

Register retiming optimizes sequential circuits by strategically relocating flip-flops without altering input-output relationships.

**Objectives:**
- **Clock Period Minimization:** Reduce maximum combinational delay
- **Pipeline Balancing:** Equalize stage delays
- **Power Optimization:** Minimize switching activity

**Implementation:**
1. **Graph Construction:** Model circuit as weighted directed graph
2. **Constraint Generation:** Ensure positive register counts
3. **Optimization Algorithm:** Apply linear programming methods
4. **Validation:** Verify timing and functional correctness

<img width="828" height="508" alt="image" src="https://github.com/user-attachments/assets/4df154d6-b76f-4ad0-8f38-6a3ab840c973" />

</details>

---



## Practical Implementation Labs

<details>
<summary>Lab 1: Basic Conditional Logic</summary>
### Verilog Code:

```verilog
module conditional_select (input a , input b , output y);
	assign y = a?b:0;
endmodule
```

**Analysis:** Implements enable gate functionality, optimizes to AND gate: `result = enable & data`

<img width="1919" height="671" alt="image" src="https://github.com/user-attachments/assets/89380fea-de0d-4039-9d1c-fb7e32874bef" />

**Synthesis Commands:**
```tcl
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v 
opt_clean -purge
synth -top opt_check
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

 ## Output
<img width="1919" height="791" alt="image" src="https://github.com/user-attachments/assets/e3faf41a-9c08-4e77-a1e1-5a32eb8ef538" />

</details>

---

<details>
<summary>Lab 2: Inverted Multiplexer</summary>

### Verilog Code:

```verilog
module inverted_mux (input select, input input_b, output result);
    assign result = select ? 1'b1 : input_b;
endmodule
```

**Analysis:** Simplifies to OR gate: `result = select | input_b`

<img width="1919" height="681" alt="image" src="https://github.com/user-attachments/assets/0c5f4885-4d48-4bed-b82d-069dc239af0a" />

## Output
<img width="1919" height="805" alt="image" src="https://github.com/user-attachments/assets/ce8a163f-dbae-46ba-8bf2-242933deb1ec" />

</details>

---

<details>
<summary>Lab 3: Complex Boolean Logic</summary>

### Verilog Code:

```verilog
module opt_check3 (input a , input b, input c , output y);
	assign y = a?(c?b:0):0;
endmodule
```

**Analysis:** Nested conditional optimizes to: `y = a & (b | c)`

<img width="1919" height="704" alt="image" src="https://github.com/user-attachments/assets/469860bb-a1e9-4e1d-976f-3502fba5f90b" />

## Output
<img width="1919" height="881" alt="image" src="https://github.com/user-attachments/assets/703fb9ac-f2b6-4a12-a6a7-e1a13b1e1e0d" />

</details>

---

<details>
<summary>Lab 4: Multi-Input Optimization</summary>

### Verilog Code:

```verilog
module _opt_check4 (input p, input q, input r, output z);
    assign z = p ? (q ? (p & r) : r) : (~r);
endmodule
```

**Analysis:** Complex ternary simplifies to: `z = p ? r : ~r`

<img width="1919" height="663" alt="image" src="https://github.com/user-attachments/assets/2a14d396-b91e-46a5-8a91-46e2e4450c1d" />

## Output
<img width="1919" height="888" alt="image" src="https://github.com/user-attachments/assets/faef8221-9d94-401b-afff-c6c33893ab7c" />

</details>

---

<details>
<summary>Lab 5: Sequential Constant Assignment</summary>

### Verilog Code:

```verilog
module dff_const1 (input clk, input rst_n, output reg q);
    always @(posedge clk or negedge rst_n) begin
        if (!rst_n)
            q <= 1'b0;
        else
            q <= 1'b1;
    end
endmodule
```

**Analysis:** After reset, output remains high on every clock cycle

<img width="1919" height="655" alt="image" src="https://github.com/user-attachments/assets/438a20cf-a262-46c4-b8c3-8f68b353750d" />

## Output 
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/2606d7cb-ed7f-48b7-a0c1-28119565c837" />

</details>

---

<details>
<summary>Lab 6: Always-High Output Register</summary>

### Verilog Code:

```verilog
module always_high_reg (input clk, input rst_n, output reg q);
    always @(posedge clk or negedge rst_n) begin
        if (!rst_n)
            q <= 1'b1;
        else
            q <= 1'b1;
    end
endmodule
```

**Analysis:** Constant high output, may optimize to direct VDD connection

<img width="1919" height="692" alt="image" src="https://github.com/user-attachments/assets/64646273-a2ea-4203-a78b-d956e4630e0f" />

## Output
<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/8f7425be-ff17-43a3-8248-c554ed209ace" />

</details>

---

<details>
<summary>Lab 7: Counter Optimization - LSB Output</summary>

### Verilog Code:

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];
always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end
endmodule
```

**Analysis:** Only LSB (count[0]) is used as output. Synthesis optimizes away unused upper bits, resulting in a single flip-flop toggle circuit.

<img width="1919" height="652" alt="image" src="https://github.com/user-attachments/assets/fbfc801c-4b0b-47a3-be23-49c0534781ef" />

## Output
<img width="1919" height="906" alt="image" src="https://github.com/user-attachments/assets/74636da4-67da-4f5e-9312-2b13412195ad" />

</details>

---

<details>
<summary>Lab 8: Counter Optimization - State Detection</summary>
	
### Verilog Code:

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0] == 3'b100);
always @(posedge clk ,posedge reset)
begin
	if(reset)
		count <= 3'b000;
	else
		count <= count + 1;
end
endmodule
```

**Analysis:** Output detects when counter equals 4 (3'b100). All counter bits are required for comparison logic, preventing optimization of unused flip-flops.

<img width="1919" height="650" alt="image" src="https://github.com/user-attachments/assets/0e65b9c1-e7e2-4774-b0ba-c40e7b493bfe" />

### Sythesis Commands:

```tcl
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog counter_opt2.v 
opt_clean -purge
synth -top counter_opt
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```
## Output
<img width="1919" height="1019" alt="image" src="https://github.com/user-attachments/assets/2dc16271-7508-4f68-9577-fc0621c25c71" />

</details>

---


## Key Learning Outcomes

- Constant Propagation: Eliminate redundant logic through compile-time analysis
- Sequential Optimization: Minimize flip-flops and combinational logic in FSMs
- Load Balancing: Strategic logic cloning for timing and fanout optimization
- Register Retiming: Pipeline balance without functional changes

**Workshop Progress**: Day 3 Complete | Next: Gate Level Simulation
