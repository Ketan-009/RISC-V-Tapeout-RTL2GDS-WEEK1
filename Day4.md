# Day 4: GLS, Blocking vs Non‑Blocking, and Sim–Synth Mismatch

## Table of Contents
- [1. Gate‑Level Simulation (GLS)](#1-gate-level-simulation-gls)
- [2. Synthesis–Simulation Mismatch](#2-synthesis–simulation-mismatch)
- [3. Blocking vs Non‑Blocking](#3-blocking-vs-non-blocking)
  - [3.1 Blocking](#31-blocking)
  - [3.2 Non‑Blocking](#32-non-blocking)
  - [3.3 Comparison](#33-comparison)
- [4. Labs](#4-labs)
- [5. Summary](#5-summary)

## 1. Gate‑Level Simulation (GLS)
GLS runs the synthesized netlist to validate functionality, reset behavior, and design‑for‑test structures beyond RTL abstraction.  
Two common modes are functional GLS (zero/unit delay) and timing GLS with SDF back‑annotation to expose setup/hold issues and hazards.

## 2. Synthesis–Simulation Mismatch
Mismatches arise from ambiguous or non‑synthesizable RTL, incomplete sensitivity lists, unintended latches, or reliance on initial/delay semantics not preserved in hardware.  
Adopt strict synthesizable coding styles, complete all decision paths, and keep combinational and sequential logic cleanly separated.

## 3. Blocking vs Non‑Blocking
Blocking updates immediately in statement order, fitting combinational modeling when ordering is correct.  
Non‑blocking schedules updates at the end of the timestep, matching flip‑flop semantics and avoiding read‑write races across clocked blocks.

### 3.1 Blocking
- Executes in source order and is order‑sensitive.  
- Preferred inside always @(*) for pure combinational logic.

### 3.2 Non‑Blocking
- Defers writes to the NBA phase to emulate registers.  
- Preferred inside always @(posedge clk …) for sequential logic.

### 3.3 Comparison

| Blocking (=) | Non‑Blocking (<=) |
|---|---|
| Immediate update within the active region | Deferred update at end of timestep |
| Order matters inside a block | Order largely irrelevant across flops |
| Best in always @(*) combinational | Best in @(posedge …) sequential |
| Can race if misused for flops | Avoids x‑prop/race across registers |

## 4. Labs

### Lab 1: Ternary MUX (RTL)
```verilog
module ternary_operator_mux (input i0, input i1, input sel, output y);
  assign y = sel ? i1 : i0;
endmodule
```

### Lab 2: Synthesize with Yosys
Create synth.ys:
```tcl
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
write_verilog mux_netlist.v
```
Run:
```sh
yosys -s synth.ys
```

### Lab 3: Functional GLS
Compile with standard cell models and primitives (example Sky130 paths):
```sh
iverilog $PDK_ROOT/libs.ref/sky130_fd_sc_hd/verilog/primitives.v \
         $PDK_ROOT/libs.ref/sky130_fd_sc_hd/verilog/sky130_fd_sc_hd.v \
         mux_netlist.v tb_mux.v
vvp a.out
```

### Lab 4: Bad MUX (fix pitfalls)
Problem (incomplete sensitivity + NBA in comb):
```verilog
module bad_mux (input i0, input i1, input sel, output reg y);
  always @(sel) begin
    if (sel) y <= i1; else y <= i0;
  end
endmodule
```
Fix:
```verilog
always @(*) begin
  if (sel) y = i1; else y = i0;
end
```

### Lab 5: GLS on Bad vs Fixed
Run GLS for both versions and compare waveforms to observe divergence caused by sensitivity omissions and scheduling differences.  
Expect alignment after adopting always @(*) with blocking for combinational logic.

### Lab 6: Blocking Order Caveat
Problem:
```verilog
always @(*) begin
  d = x & c;   // uses old x
  x = a | b;   // updates too late
end
```
Fix:
```verilog
always @(*) begin
  x = a | b;   // produce
  d = x & c;   // then consume
end
```

### Lab 7: Re‑Synthesize
Re‑run synthesis on the fixed combinational design to confirm no latch inference and expected gate structure:
```sh
yosys -p "read_verilog blocking_caveat_fixed.v; synth -top blocking_caveat; write_verilog caveat_netlist.v"
```

## 5. Summary
- GLS validates netlist functionality and timing intent; use functional GLS early and timing GLS (SDF) when supported.  
- Prevent sim–synth mismatches with complete sensitivity, explicit defaults, and synthesizable, unambiguous RTL.  
- Use blocking for combinational and non‑blocking for sequential, avoiding mixes within the same always block.
