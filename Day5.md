# Day 5: Advanced Synthesis Concepts and Optimization

This guide explores into advanced synthesis concepts, focusing on timing optimization, area optimization, power optimization, and advanced Verilog constructs. This session includes practical labs to understand synthesis trade-offs and optimization techniques.

**Note:** Click the sections below to expand.

## Core Concepts 

<details>
<summary><strong> 1. Overview of Synthesis Optimization</strong></summary>

### Synthesis Optimization

Synthesis optimization involves transforming RTL code into efficient gate-level implementations while maintaining functional correctness. Key aspects include:

### Optimization Goals

- Area minimization: Reducing gate count and silicon footprint
- Timing optimization: Meeting clock frequency requirements
- Power efficiency: Minimizing dynamic and static power consumption
- Testability: Ensuring design-for-test compatibility

### Critical Considerations

- Synthesis tool interpretation: Understanding how tools translate RTL constructs
- Hardware inference: Controlling what hardware structures are generated
- Coding style impact: How different coding approaches affect synthesis results

---

</details>

<details>
<summary><strong>2.If Else Implementation</strong></summary>

### Understanding Conditional Logic Implementation
Conditional statements form the backbone of decision-making logic in digital designs. Proper implementation is crucial for predictable synthesis results.

### Basic Conditional Structure:

```verilog
if (expression) begin
    // Execute when condition is true
end else begin
    // Execute when condition is false
end
```

### Multi-Level Conditional Logic

```verilog
if (primary_condition) begin
    // Primary execution path
end else if (secondary_condition) begin
    // Secondary execution path
end else if (tertiary_condition) begin
    // Tertiary execution path
end else begin
    // Default execution path
end
```

**Best Practice:** Always provide complete coverage of all possible conditions to avoid unintended hardware inference.

</details>

<details>
<summary><strong>3. Latch Inference</strong></summary>

### Understanding Latch Inference
Latch inference occurs when synthesis tools detect incomplete signal assignments in combinational logic blocks, resulting in memory elements that may not be intended.

### Common Causes of Latch Inference

- Missing else clauses in conditional statements
- Incomplete case coverage in case statements
- Partial signal assignments across different execution paths


### Problematic Example

```verilog
module latch_example (
    input wire enable, data_in,
    output reg data_out
);
    always @(enable, data_in) begin
        if (enable)
            data_out = data_in;
        // Missing else clause - latch inferred!
    end
endmodule
```

### Corrected Implementation

```verilog
module no_latch_example (
    input wire enable, data_in,
    output reg data_out
);
    always @(enable, data_in) begin
        if (enable)
            data_out = data_in;
        else
            data_out = 1'b0; // Explicit default assignment
    end
endmodule
```

</details>

<details>
<summary><strong>4. For Loops </strong></summary>

### Understanding For Loops in Verilog
For loops help you repeat code multiple times in your Verilog designs. Think of them as a way to avoid writing the same code over and over again.

### Syntax
```
for (start_value; keep_going_condition; next_step) begin
    // Your repeated code goes here
end
```

### Important Rules:
- You can only use for loops inside always, initial, or function blocks
- The loop must run a fixed number of times that the compiler can figure out beforehand
- They're great for creating repetitive hardware patterns

<img width="448" height="192" alt="image" src="https://github.com/user-attachments/assets/9e9ebd35-9bb1-4470-b10a-4b1fbedf1915" />

</details>

<details>
<summary><strong>5. Generate Blocks </strong></summary>

### Understanding Generate Blocks in verilog

Generate blocks let you create multiple copies of the same hardware automatically. They're like using a template to build repetitive circuits.

### Syntax
```
genvar loop_variable;
generate
    for (loop_variable = 0; loop_variable < 4; loop_variable = loop_variable + 1) begin : unique_name
        // Create hardware components here
        and_gate my_gate (.input_a(signal_in[loop_variable]), 
                         .input_b(signal_in[loop_variable+1]), 
                         .output_y(signal_out[loop_variable]));
    end
endgenerate
```

<img width="503" height="265" alt="image" src="https://github.com/user-attachments/assets/e3e172d5-ab53-423b-aec2-cf753047bbb2" />

</details>

<details>
<summary><strong>6. Ripple Carry Adder (RCA) </strong></summary>

### Understanding Ripple Carry Adder (RCA) in Verilog
A ripple carry adder is like a chain of calculators working together to add large numbers.

### Concept

- Each "calculator" (full adder) handles one bit position
- When adding two 8-bit numbers, you need 8 full adders
- The carry from one position feeds into the next position
- It's called "ripple" because the carry signal moves like a wave from right to left

<img width="1070" height="525" alt="image" src="https://github.com/user-attachments/assets/47684313-062a-4b2c-932e-0bf1a5ce68e8" />

</details>

## Practical Implementation Labs

<details>
<summary><strong>Lab 1. Incomplete Conditional Logic</strong></summary>

## Verilog Code:

```verilog
module incomp_if (input i0 , input i1 , input i2 , output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
end
endmodule
```

<img width="1919" height="721" alt="image" src="https://github.com/user-attachments/assets/1838cd4e-3624-4b37-b4ea-d1608225305c" />

**Analysis:** This code will infer a latch because `y` is not assigned when `i0` is false.

## Simulation Steps:

```
iverilog incomp_if.v tb_incomp_if.v
./a.out
gtkwave tb_incomp_if.vcd
```

## Output:
<img width="1919" height="662" alt="image" src="https://github.com/user-attachments/assets/ca2d2f2c-1ccb-47b6-99a9-cfbfa334802a" />

</details>

<details>
<summary><strong>Lab 2. Synthesized Logic of Incomplete If Else</strong></summary>

## Command for Synthesizing

```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if.v
opt_clean -purge
synth -top incomp_if
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

## Synthesized Design
<img width="1919" height="1019" alt="image" src="https://github.com/user-attachments/assets/46313c13-7633-45d7-b90b-ab5079215ee1" />

**Note:** We aimed and wrote the code for MUX but we got Latch due to Incomplete If Case.

</details>

<details>
<summary><strong>Lab 3.Nested If-Else</strong></summary>

## Verilog Code:

```verilog

module incomp_if2 (input i0 , input i1 , input i2 , input i3, output reg y);
always @ (*)
begin
	if(i0)
		y <= i1;
	else if (i2)
		y <= i3;
end
endmodule
```
<img width="1919" height="668" alt="image" src="https://github.com/user-attachments/assets/672e1ce3-2e1f-49ad-a93f-7bcfa39b4a7f" />

## Simulation Steps:

```
iverilog incomp_if2.v tb_incomp_if2.v
./a.out
gtkwave tb_incomp_if2.vcd
```

## Output:
<img width="1918" height="645" alt="image" src="https://github.com/user-attachments/assets/b5ddb39a-b4d4-4e96-8463-392b08235196" />

</details>

<details>
<summary><strong>Lab 4. Synthesized Analysis of Nested If-Else</strong></summary>

## Command for Synthesizing

```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog incomp_if2.v
opt_clean -purge
synth -top incomp_if2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

## Synthesized Design
<img width="1919" height="1016" alt="image" src="https://github.com/user-attachments/assets/f4abb63f-e6bf-4059-af5c-f4ef63e9c3d7" />

**Note:** Even with multiple conditions, incomplete coverage still results in latch inference.

</details>

<details>
<summary><strong>Lab 5. Complete Case</strong></summary>

## Verilog Code:

```verilog
module comp_case (input i0 , input i1 , input i2 , input [1:0] sel, output reg y);
always @ (*)
begin
	case(sel)
		2'b00 : y = i0;
		2'b01 : y = i1;
		default : y = i2;
	endcase
end
endmodule
```

<img width="1919" height="678" alt="image" src="https://github.com/user-attachments/assets/208d002e-2cd8-4e89-8989-012866d6fa69" />

## Simulation Steps:

```
iverilog comp_case.v tb_comp_case.v
./a.out
gtkwave tb_comp_case.vcd
```

## Output:
<img width="1919" height="696" alt="image" src="https://github.com/user-attachments/assets/99f3c5f2-090b-4cc2-bc67-f4450bb17274" />

</details>

<details>
<summary><strong>Lab 6. Synthesized Result of Complete Case</strong></summary>

## Command for Synthesizing

```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog comp_case.v
opt_clean -purge
synth -top comp_case
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

## Synthesized Design
<img width="1919" height="1017" alt="image" src="https://github.com/user-attachments/assets/336fd824-060d-46c1-83ba-8dd91ffe1bdf" />

**Note:** Complete case coverage results in clean combinational logic without latches.

</details>

<details>
<summary><strong>Lab 7. Incomplete Case Handling</strong></summary>

## Verilog Code:

```verilog
module bad_case (input i0 , input i1, input i2, input i3 , input [1:0] sel, output reg y);
always @(*)
begin
	case(sel)
		2'b00: y = i0;
		2'b01: y = i1;
		2'b10: y = i2;
		2'b1?: y = i3;
		//2'b11: y = i3;
	endcase
end
endmodule
```

<img width="1919" height="682" alt="image" src="https://github.com/user-attachments/assets/d1e1b45a-e796-4205-bf38-a54cedad34a6" />

## Simulation Steps:

```
iverilog bad_case.v tb_bad_case.v
./a.out
gtkwave tb_bad_case.vcd
```

## Output:
<img width="1919" height="667" alt="image" src="https://github.com/user-attachments/assets/a01f136a-a36b-48a2-9b08-416e7bc89f12" />

</details>

<details>
<summary><strong>Lab 8. Partial Assignments in Case</strong></summary>

## Verilog Code:

```verilog
module partial_case_assign (input i0 , input i1 , input i2 , input [1:0] sel, output reg y , output reg x);
always @ (*)
begin
	case(sel)
		2'b00 : begin
			y = i0;
			x = i2;
			end
		2'b01 : y = i1;
		default : begin
		           x = i1;
			   y = i2;
			  end
	endcase
end
endmodule
```

<img width="1919" height="665" alt="image" src="https://github.com/user-attachments/assets/3702733b-12ca-4090-8a9f-afe73013105d" />

## Command for Synthesizing

```
yosys
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog partial_case_assign.v
opt_clean -purge
synth -top partial_case_assign
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

## Synthesized Design
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/1ec74182-3753-4ac1-a075-3a97fee30a9b" />

</details>

<details>
<summary><strong>Lab 9.  4-to-1 MUX</strong></summary>

## Verilog Code:

```verilog
module mux_generate (input i0 , input i1, input i2 , input i3 , input [1:0] sel  , output reg y);
wire [3:0] i_int;
assign i_int = {i3,i2,i1,i0};
integer k;
always @ (*)
begin
for(k = 0; k < 4; k=k+1) begin
	if(k == sel)
		y = i_int[k];
end
end
endmodule
```
<img width="1919" height="680" alt="image" src="https://github.com/user-attachments/assets/3a7e26f0-b58c-4c9e-b85f-254ec690ae23" />

## Simulation Steps:

```
iverilog mux_generate.v tb_mux_generate.v
./a.out
gtkwave tb_mux_generate.vcd
```

## Output:
<img width="1919" height="721" alt="image" src="https://github.com/user-attachments/assets/66d13eb8-4794-4e4b-9000-be62360619e3" />

</details>

<details>
<summary><strong>Lab 10. 8-to-1 Demux Using Case</strong></summary>

## Verilog Code:

```verilog
module demux_case (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
	case(sel)
		3'b000 : y_int[0] = i;
		3'b001 : y_int[1] = i;
		3'b010 : y_int[2] = i;
		3'b011 : y_int[3] = i;
		3'b100 : y_int[4] = i;
		3'b101 : y_int[5] = i;
		3'b110 : y_int[6] = i;
		3'b111 : y_int[7] = i;
	endcase

end
endmodule
```
<img width="1919" height="662" alt="image" src="https://github.com/user-attachments/assets/ffe6a9c2-0b6f-4657-abc1-22331098caec" />

## Simulation Steps:

```
iverilog demux_case.v tb_demux_case.v
./a.out
gtkwave tb_demux_case.vcd
```

## Output:
<img width="1919" height="753" alt="image" src="https://github.com/user-attachments/assets/6c34e7a4-3783-4c74-b220-58c6ef0339e9" />

</details>

<details>
<summary><strong>Lab 11. 8-to-1 Demux Using For Loop</strong></summary>

## Verilog Code:

```verilog

module demux_generate (output o0 , output o1, output o2 , output o3, output o4, output o5, output o6 , output o7 , input [2:0] sel  , input i);
reg [7:0]y_int;
assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;
integer k;
always @ (*)
begin
y_int = 8'b0;
for(k = 0; k < 8; k++) begin
	if(k == sel)
		y_int[k] = i;
end
end
endmodule
```
<img width="1919" height="669" alt="image" src="https://github.com/user-attachments/assets/f41df4ba-562f-40db-b033-4478d4e49563" />

## Simulation Steps:

```
iverilog demux_generate.v tb_demux_generate.v
./a.out
gtkwave tb_demux_generate.vcd
```

## Output:
<img width="1919" height="812" alt="image" src="https://github.com/user-attachments/assets/580b67a9-6cd8-417d-9b83-fbf3a6f8dc20" />

</details>

<details>
<summary><strong>Lab 12. 8-bit Ripple Carry Adder with Generate Block</strong></summary>

## Verilog Code[RCA]:

```verilog
module rca (input [7:0] num1 , input [7:0] num2 , output [8:0] sum);
wire [7:0] int_sum;
wire [7:0]int_co;

genvar i;
generate
	for (i = 1 ; i < 8; i=i+1) begin
		fa u_fa_1 (.a(num1[i]),.b(num2[i]),.c(int_co[i-1]),.co(int_co[i]),.sum(int_sum[i]));
	end

endgenerate
fa u_fa_0 (.a(num1[0]),.b(num2[0]),.c(1'b0),.co(int_co[0]),.sum(int_sum[0]));

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];
endmodule
```

<img width="1919" height="660" alt="image" src="https://github.com/user-attachments/assets/474191fb-d4ad-4bb8-8aaa-5e6ba3741fe1" />

## Verilog Code[Full Adder]:

```
module fa (input a , input b , input c, output co , output sum);
	assign {co,sum}  = a + b + c ;
endmodule
```

<img width="1919" height="662" alt="image" src="https://github.com/user-attachments/assets/ebb0f289-ac44-4629-ac0f-fa7bd6106617" />

## Simulation Steps:

```
iverilog rca.v fa.v tb_rca.v
./a.out
gtkwave tb_rca.vcd
```

## Output:
<img width="1919" height="670" alt="image" src="https://github.com/user-attachments/assets/bdd677e9-3727-497b-a957-a451051a7f24" />

</details>

## Key Takeaways:
- Synthesis Optimization – Balance performance, power, and area with realistic constraints; improve timing (logic restructuring, buffering), manage area (resource sharing), and reduce power (clock gating, multi-Vt).
- RTL Coding Style – Write intent-clear RTL; tool maps code to hardware (e.g., if/else → priority, case → mux). Always verify with reports/schematics.
- Latch Prevention – Avoid unintended latches by assigning outputs on all paths, using safe defaults, and complete sensitivity (always @(*)).
- Coding Templates – Use defaults + if/case in combinational blocks; non-blocking (<=) with reset in sequential blocks.
- Scalable Structures – Static for-loops unroll into hardware; use generate/for with parameters for scalable designs. RCA is simple but slow—pipeline or use faster adders if needed.

