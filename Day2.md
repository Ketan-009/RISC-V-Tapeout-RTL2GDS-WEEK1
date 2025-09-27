# Day 2: Technology Libraries, Design Hierarchies, and Sequential Circuit Implementation

This comprehensive guide explores timing characterization files, synthesis methodologies, and robust flip-flop design patterns essential for digital circuit implementation.

**Note:** Click the sections below to expand.

---

## Table of Contents

<details>
<summary>SKY130 Technology Library Analysis</summary>

### Library Naming Convention Breakdown

The `sky130_fd_sc_hd__tt_025C_1v80.lib` filename encodes critical process parameters:

| Component | Meaning | Impact |
|-----------|---------|---------|
| `tt` | Typical-Typical corner | Nominal performance characteristics |
| `025C` | 25°C operating temperature | Temperature-dependent timing |
| `1v80` | 1.8V supply voltage | Voltage-dependent delays |

### Library File Structure Exploration

```bash
# Navigate to library directory
cd /path/to/sky130_libs

# Open library file for inspection
gedit sky130_fd_sc_hd__tt_025C_1v80.lib

# Search for specific cell definitions
grep -n "cell.*and" sky130_fd_sc_hd__tt_025C_1v80.lib
```

<img width="1919" height="995" alt="image" src="https://github.com/user-attachments/assets/7712f71f-1bee-4426-bc8d-71de388a90db" />
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/b48ef9e7-5c2e-43c5-976c-67aab1fd69ef" />



The library contains cell definitions with timing arcs, power models, and physical constraints essential for accurate synthesis.
</details>

<details>
<summary>Synthesis Methodology Comparison</summary>

### Hierarchical Synthesis Implementation

Hierarchical synthesis preserves module boundaries, enabling modular optimization and easier debugging.

```tcl
# Yosys hierarchical synthesis flow
yosys

# Read technology library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Import hierarchical design
read_verilog multiple_modules.v

# Maintain hierarchy during synthesis
synth -top multiple_modules

# Technology mapping with hierarchy preservation
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Generate hierarchical netlist
write_verilog -noattr multiple_modules_hier.v
show \multiple_modules
```

### Generation of Hierarchical Netlist
<img width="1919" height="1020" alt="image" src="https://github.com/user-attachments/assets/307a169b-2e4a-4a96-82ca-806d2a651471" />
<img width="1919" height="1022" alt="image" src="https://github.com/user-attachments/assets/4fbf0fe7-80a1-4fad-8644-74f79570f3cb" />


**Benefits:**
- Faster compilation for large designs
- Module-level constraint application
- Simplified incremental design changes
- Enhanced debugging capabilities

### Flattened Synthesis Approach

Flattened synthesis eliminates hierarchy boundaries, enabling global optimization across module interfaces.

```tcl
# Continue from hierarchical flow
flatten

# Re-optimize flattened design
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Export flattened netlist
write_verilog -noattr multiple_modules_flat.v
show
```

### Generation of Flatten Netlist
<img width="1853" height="696" alt="Screenshot 2025-09-26 051646" src="https://github.com/user-attachments/assets/2f4cd483-03a8-48f3-acf4-356a3b21f3f3" />
<img width="1919" height="750" alt="image" src="https://github.com/user-attachments/assets/6c9eb40e-f32b-4153-8fd6-f8870b27d62c" />

**Advantages:**
- Cross-hierarchy optimization opportunities
- Reduced gate count through logic sharing
- Simplified static timing analysis
- Enhanced area optimization

### Synthesis Results Comparison

| Metric | Hierarchical | Flattened | Impact |
|--------|-------------|-----------|---------|
| Compilation Time | Faster | Slower | Development efficiency |
| Gate Count | Higher | Lower | Area optimization |
| Debug Complexity | Lower | Higher | Verification effort |
| Optimization Scope | Local | Global | Performance potential |

</details>

<details>
<summary>Sequential Circuit Design Patterns</summary>

### Asynchronous Reset Flip-Flop
```verilog
module dff_asyncres (
    input clk,
    input async_reset,
    input d,
    output reg q
);
    always @(posedge clk or posedge async_reset) begin
        if (async_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```

## Snapshot
<img width="978" height="437" alt="image" src="https://github.com/user-attachments/assets/1bbc1959-ae22-4dcf-a87a-60865a231ba3" />


**Characteristics:**
- Immediate reset response independent of clock
- Priority given to reset over data input
- Used in critical control path applications

### Asynchronous Set Flip-Flop
```verilog
module dff_async_set (
    input clk,
    input async_set,
    input d,
    output reg q
);
    always @(posedge clk or posedge async_set) begin
        if (async_set)
            q <= 1'b1;
        else
            q <= d;
    end
endmodule
```

## Snapshot
<img width="967" height="444" alt="image" src="https://github.com/user-attachments/assets/28dddb1e-5689-4f0b-94dc-4b17615f7987" />

**Applications:**
- Power-on state initialization
- Emergency system activation
- Default state establishment

### Synchronous Reset Implementation
```verilog
module dff_syncres (
    input clk,
    input sync_reset,
    input d,
    output reg q
);
    always @(posedge clk) begin
        if (sync_reset)
            q <= 1'b0;
        else
            q <= d;
    end
endmodule
```
## Snapshot 
<img width="970" height="440" alt="Screenshot 2025-09-26 053347" src="https://github.com/user-attachments/assets/d3d077d9-4b34-4ab7-8bf1-272a9b059466" />



**Benefits:**
- Clock-synchronized reset timing
- Reduced metastability risk
- Simplified timing constraint specification

</details>

<details>
<summary>Simulation and Verification Workflow</summary>

### Testbench-Driven Simulation

```bash
# Compile design with testbench
iverilog dff_asyncres.v tb_dff_asyncres.v -o dff_sim

# Execute simulation
./dff_sim

# Launch waveform analyzer
gtkwave tb_dff_asyncres.vcd &
```

## Snapshot
<img width="1919" height="1023" alt="image" src="https://github.com/user-attachments/assets/eab07ff0-b331-4bf2-a29d-9074f5c7fe85" />

### Synthesis Verification Flow

```tcl
# Start synthesis environment
yosys

# Load standard cell library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Import RTL design
read_verilog dff_asyncres.v

# Perform synthesis
synth -top dff_asyncres

# Map sequential elements
dfflibmap -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Complete technology mapping
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Visualize implementation
show

# Export gate-level netlist
write_verilog dff_asyncres_synth.v
```

## Snapshot
<img width="1919" height="1015" alt="image" src="https://github.com/user-attachments/assets/fd517304-7884-434a-9a50-5ae360a9ddea" />
<img width="1919" height="746" alt="image" src="https://github.com/user-attachments/assets/3b0fc3dc-9a4b-474a-884c-2998957c835d" />

### Waveform Analysis Results

The simulation demonstrates proper flip-flop behavior with asynchronous reset functionality, showing immediate output response to reset assertions independent of clock transitions.

</details>

<details>
<summary>Advanced Synthesis Techniques</summary>

### Multi-Module Design Example

```verilog
module sub_module1 (input a, input b, output y);
    assign y = a & b;
endmodule

module sub_module2 (input a, input b, output y);
    assign y = a | b;
endmodule

module multiple_modules (
    input a,
    input b,
    input c,
    output y
);
    wire net1;
    
    sub_module1 u1 (.a(a), .b(b), .y(net1));
    sub_module2 u2 (.a(net1), .b(c), .y(y));
endmodule
```

## Snapshot
<img width="1293" height="519" alt="image" src="https://github.com/user-attachments/assets/9e542ad5-044c-4685-b233-3dfdfb8f216f" />

### Synthesis Statistics Analysis

```tcl
# Generate synthesis statistics
stat

# Report cell usage
stat -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Analyze optimization results
check
```

## Snapshots
<img width="1919" height="1024" alt="image" src="https://github.com/user-attachments/assets/58600a2e-b68a-45ce-8a8b-6883f2d925fb" />
<img width="1919" height="968" alt="image" src="https://github.com/user-attachments/assets/fff48829-59d7-49cd-8a7b-b9070ef500f8" />


The synthesis reports provide insights into resource utilization, critical path delays, and optimization effectiveness.

</details>

## Key Learning Outcomes

- **Library Characterization**: Understanding technology library structure and parameter encoding
- **Synthesis Strategies**: Comparing hierarchical versus flattened implementation approaches  
- **Sequential Design**: Implementing reliable flip-flop variants with different reset behaviors
- **Verification Methodology**: Establishing simulation and synthesis verification workflows
- **Tool Proficiency**: Mastering Yosys synthesis commands and optimization techniques

---

**Workshop Progress**: Day 2 Complete | Next: Combinational Logic Optimization
