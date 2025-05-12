# Clock Tree Synthesis (CTS) – 4-Bit Full Adder (Flat Design)

This section documents the Clock Tree Synthesis (CTS) stage in the physical design flow of a 4-bit full adder using Synopsys ICC2.

---

## What is Clock Tree Synthesis?

Clock Tree Synthesis (CTS) is the process of inserting buffers/inverters and routing clock signals so that all clocked elements (flip-flops, latches) receive the clock with minimal skew and acceptable insertion delay.

### Key Goals of CTS:

- Distribute the clock signal efficiently across the design.
- Minimize clock skew (difference in clock arrival times).
- Control insertion delay (delay from clock source to clock sinks).
- Balance timing for hold and setup constraints.

---

## CTS Agenda

| Step                      | Description                                                               |
|---------------------------|---------------------------------------------------------------------------|
| Pre-CTS Checks            | Ensure the design is ready for CTS with proper netlist and constraints.   |
| Define Clock Routing      | Limit CTS routing layers for better control.                              |
| Clock Tree Synthesis      | Automatically insert buffers and build a clock distribution network.      |
| Clock Optimization        | Improve skew and fix setup/hold violations related to clocks.             |

---

## CTS TCL Script Breakdown

#### 1. Perform Pre-CTS Design Checks
```
check_design -checks pre_clock_tree_stage
```
- Validates the design before clock tree insertion, including connectivity,
- netlist integrity, and floorplan readiness.

#### 2. Set Scenario Status
```
set_scenario_status func_slow -hold true -setup true -leakage_power true \
  -max_capacitance true -min_capacitance true -dynamic_power true \
  -max_transition true -active true
```
 Activates the "func_slow" timing scenario for CTS.
 Ensures hold/setup, leakage, dynamic power, and other constraints are considered.

#### 3. Define Clock Routing Rules
```
set_clock_routing_rules -min_routing_layer M3 -max_routing_layer M4 -default_rule
```
 Specifies that the clock tree must be routed between Metal3 (M3) and Metal4 (M4),
 avoiding lower layers like M1 or M2 to reduce congestion and cross-talk.

#### 4. Perform Clock Tree Synthesis
```
synthesize_clock_trees
```
Virtual routes of clock are done.

![Screenshot 2025-05-13 000316](https://github.com/user-attachments/assets/7a38c86e-e94d-4adf-ad66-9c4eb521ac80)

 Automatically generates the clock tree using buffers/inverters to ensure all
clock sinks receive the clock with minimal skew and controlled insertion delay.

#### 5. Optimize the Clock Network
```
clock_opt
```
![Screenshot 2025-05-13 000550](https://github.com/user-attachments/assets/12f98b51-4836-4831-8a8f-92ff7da4891a)

![Screenshot 2025-05-13 000557](https://github.com/user-attachments/assets/cae1d440-864c-4829-a3dd-9e2a06fc3454)

Clock route is done and virtual route of signal is done.


![Screenshot 2025-05-13 000429](https://github.com/user-attachments/assets/c2334bae-af10-41b4-b343-63b55e422b5e)

 Optimizes the clock tree further for hold/setup timing, transition violations,
 and overall skew minimization.

## Summary Table

| Step                    | Command                          | Purpose                                  |
|-------------------------|----------------------------------|------------------------------------------|
| Pre-CTS Check           | check_design -checks pre_clock_tree_stage | Validate readiness for CTS     |
| Scenario Activation     | set_scenario_status              | Enable timing/power constraints          |
| Routing Layer Control   | set_clock_routing_rules          | Limit CTS layers to M3–M4                |
| CTS Execution           | synthesize_clock_trees           | Build clock tree                         |
| Optimization            | clock_opt                        | Refine skew and fix timing issues        |


## Tool Used

- Tool: Synopsys ICC2
- Flow: Flat Design Flow (4-bit Full Adder)
- Language: TCL

