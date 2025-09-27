# Day 4 Enhancements

## Enhanced Theory Sections

### Gate-Level Simulation
- **Definition**: Gate-level simulation is a type of simulation that models the behavior of a digital circuit at the gate level, allowing for a detailed verification of the circuit's functionality.
- **Purpose**: It helps in verifying the logical correctness of the design post-synthesis, ensuring that the synthesized netlist behaves as intended.
- **Steps Involved**:
  - Create a gate-level netlist from RTL code.
  - Apply test vectors to the gate-level model.
  - Observe outputs and compare them with expected results.
- **Advantages**:
  - Provides a more accurate representation of the circuit's timing and logic.
  - Can identify issues related to the synthesis process that might not be visible in RTL simulation.
- **Disadvantages**:
  - More time-consuming than RTL simulations.
  - Requires additional resources for simulation.

### Synthesis-Simulation Mismatch
- **Definition**: This refers to discrepancies between the behavior of a simulation model and the synthesized hardware.
- **Common Causes**:
  - Differences in timing due to optimization techniques used during synthesis.
  - Variations in the logic due to synthesis tools making assumptions.
- **Techniques to Resolve**:
  - Perform static timing analysis to identify timing violations.
  - Utilize assertions to check for expected behaviors during simulation.

### Blocking vs Non-Blocking
- **Definition**: 
  - **Blocking**: Assignments that occur sequentially and can lead to unintended behavior if not used carefully.
  - **Non-Blocking**: Assignments that allow for concurrent execution and are essential for accurate modeling of sequential logic.
- **When to Use**:
  - Use blocking assignments for combinational logic.
  - Use non-blocking assignments for sequential logic to avoid race conditions.
- **Comparison**:
  - Blocking assignments can lead to issues in a simulation that does not reflect the real hardware behavior, while non-blocking assignments can correctly model the flow of data in a clocked system.

## Existing Lab Content
- ... (Keep existing lab content and structure intact)