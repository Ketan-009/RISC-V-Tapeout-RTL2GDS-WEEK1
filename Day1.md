# Day 1: RTL Design and Synthesis Fundamentals

This comprehensive guide covers RTL design methodology using Verilog HDL, simulation with Icarus Verilog, and logic synthesis using Yosys. Build foundational knowledge through structured hands-on exercises.

---

## Table of Contents

<details>
<summary>Core Concepts</summary>

### Simulator
A simulation engine validates digital circuit behavior by processing test vectors and generating outputs. Essential for pre-implementation verification.

### Design Module  
The primary Verilog implementation containing functional logic description of your digital circuit.

### Testbench Environment
A verification framework generating stimulus patterns and monitoring design responses for correctness validation.

```
Design Under Test (DUT) ←→ Testbench
        ↓
    Simulation Results
```

</details>

<details>
<summary>Icarus Verilog Setup</summary>

### Simulation Workflow
Icarus Verilog provides complete open-source simulation environment following this flow:

```
Verilog Source → iverilog Compiler → Executable → VCD Output → GTKWave Viewer
```

### Making new Directory
```bash
# Install required simulation tools
mkdir -p vsd/vlsi
cd vsd/vlsi
```

### Environment Preparation
```bash
# Clone workshop repository
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop/verilog_files
```

## Snapshot
<img width="1750" height="529" alt="RTL Design Flow Overview" src="https://github.com/user-attachments/assets/46977a0b-63fb-4ab4-8c76-c1d4881737e7" />

</details>

<details>
<summary>Practical Lab: 2-to-1 Multiplexer</summary>

### Simulation Execution Steps

```bash
# Compile design and testbench
iverilog good_mux.v tb_good_mux.v

# Execute simulation
./a.out

# Launch waveform viewer
gtkwave tb_good_mux.vcd
```

### Expected Simulation Results
The simulation generates timing diagrams showing multiplexer switching behavior based on select signal transitions.

<img width="1919" height="980" alt="GTKWave Waveform Analysis" src="https://github.com/user-attachments/assets/a030863c-a07f-4c28-9dc1-a8963547544a" />

</details>

<details>
<summary>Verilog Code Analysis</summary>

### Multiplexer Implementation
```verilog
module good_mux (input i0, input i1, input sel, output reg y);
always @ (*)
begin
    if(sel)
        y <= i1;
    else 
        y <= i0;
end
endmodule
```

## Snapshot 

<img width="1409" height="984" alt="Screenshot 2025-09-26 042945" src="https://github.com/user-attachments/assets/a6469a53-df3d-4b8c-b3bd-2d4c7e0639fe" />


### Functional Breakdown
- **Control Logic**: Select signal determines output routing
- **Data Path**: Two input channels (i0, i1) multiplexed to single output  
- **Implementation**: Behavioral modeling using always block with sensitivity list
- **Combinational Logic**: Output responds immediately to input changes

</details>

<details>
<summary>Yosys Synthesis Framework</summary>

### Core Capabilities
Yosys transforms RTL descriptions into gate-level implementations through:

- **Frontend Processing**: Verilog parsing and elaboration
- **Logic Optimization**: Area and timing improvements
- **Technology Mapping**: Cell library instantiation  
- **Backend Generation**: Netlist output in various formats

### Standard Cell Library Variations

| Parameter | Implementation Options |
|-----------|----------------------|
| Drive Strength | Low, Medium, High current capabilities |
| Threshold Voltage | Standard VT, Low VT, High VT variants |
| Power Profile | Low Power optimized vs High Performance |
| Area Optimization | Compact, Balanced, Speed-optimized layouts |

Libraries contain multiple gate implementations addressing diverse design requirements including speed, power, and area constraints.

### Yosys Command Sequence

```tcl
# Initialize Yosys environment
yosys

# Load technology library
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Import RTL design
read_verilog good_mux.v

# Execute synthesis process
synth -top good_mux

# Apply technology mapping
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib

# Generate schematic visualization  
show

# Export gate-level netlist
write_verilog good_mux_netlist.v
```

## Snapshot

<img width="1919" height="1022" alt="Yosys Initialization" src="https://github.com/user-attachments/assets/a51f9068-2ae2-40a8-bdd4-bfc85747383c" />
<img width="1919" height="1021" alt="Synthesis Process Output" src="https://github.com/user-attachments/assets/57467f6f-4b32-4336-9411-9dc692d78cec" />
<img width="1919" height="1021" alt="Gate-Level Schematic" src="https://github.com/user-attachments/assets/3db3b225-1eab-4bf7-8cf2-c325f9a1ca82" />

### Synthesis Output Analysis
The synthesis tool generates optimized gate-level representation using standard cells from target library, meeting specified design constraints.

</details>

# Key Takeaways

### Skills Developed
- **Simulation Methodology**: Established verification workflow using open-source tools
- **RTL Design Principles**: Validated design functionality through testbench-driven simulation
- **Synthesis Process**: Converted behavioral description to gate-level implementation  
- **Tool Proficiency**: Gained experience with industry-standard EDA tools
- **Design Flow**: Understood complete RTL-to-gates transformation process

### Technical Achievements
- Successfully simulated 2-to-1 multiplexer design
- Analyzed waveforms and verified functional correctness
- Performed logic synthesis and technology mapping
- Generated optimized gate-level netlist
- Visualized synthesized circuit schematic

### Next Steps
Advance to complex sequential designs, timing analysis, and physical implementation in subsequent workshop sessions.


---

**Workshop Status**: Day 1 Complete | **Next Topic**: Advanced Synthesis Techniques and Timing Analysis

---
