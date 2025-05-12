# Routing – 4-Bit Full Adder (Flat Design)

This section describes the **Routing** stage in the physical design flow of a 4-bit full adder using Synopsys ICC2. It includes the key concepts, routing script explanation, and post-routing timing reports for setup and hold paths.

---

## What is Routing in Physical Design?

Routing is the process of physically connecting all nets in the design by placing metal wires and vias on different metal layers. It consists of:

1. **Global Routing** – Determines rough paths on a routing grid.
2. **Track Assignment** – Maps nets to specific metal tracks.
3. **Detailed Routing** – Finalizes all physical routes and checks for DRC violations.

---

## Objectives of Routing

| Objective              | Description                                                |
|------------------------|------------------------------------------------------------|
| Timing-Driven Routing  | Meets setup and hold timing constraints.                   |
| Crosstalk Avoidance    | Reduces noise and coupling effects between wires.          |
| Antenna Rule Handling  | Avoids gate damage during fabrication.                     |
| Diode Insertion        | Fixes antenna violations with protection diodes.           |

---

## TCL Script Breakdown for Routing

### Enable Timing and Crosstalk Awareness

```tcl
set_app_options -name route.global.timing_driven -value true
set_app_options -name route.global.crosstalk_driven -value true
set_app_options -name route.track.timing_driven -value true
set_app_options -name route.track.crosstalk_driven -value true
set_app_options -name route.detail.timing_driven -value true
```

Enables timing and signal integrity optimization during all stages of routing.

---

### Enable Antenna Fixing Using Diodes

```tcl
set_app_options -name route.detail.antenna -value true
set_app_options -name route.detail.antenna_fixing_preference -value use_diodes
set_app_options -block [current_design] -name route.detail.diode_libcell_names -value {*/ANTENNA_RVT}
```

Automatically detects and fixes antenna violations by inserting diodes from the specified library.

---

### Routing Execution Commands

```tcl
route_global
```
Performs global routing to estimate paths and check routability.

```tcl
route_track
```

Assigns nets to specific routing tracks aligned with metal grids.

```tcl
route_detail
```
Finalizes the routing by placing actual wires and vias.
![Screenshot 2025-05-13 000954](https://github.com/user-attachments/assets/9b3d362a-61a4-42e2-8faa-d3d6914b1dbb)

---

### Enable Congestion & Crosstalk Optimization

```tcl
set_app_options -name route_opt.flow.enable_ccd -value true
route_opt
```
Runs post-routing optimization using congestion and crosstalk-driven logic to improve quality of results.

![Screenshot 2025-05-13 001042](https://github.com/user-attachments/assets/48481f0f-2264-4151-a041-cb74120ec2b2)

---

#### Final Design View : 

![Screenshot 2025-05-13 001147](https://github.com/user-attachments/assets/cea8dcb2-c8c1-4a74-ac0b-f0a4c3c55bc3)


### Output File Generation

```tcl
write_lef $LEF_FILE
write_parasitics -format spef -output $SPEF_FILE
write_verilog $routed_netlist
write_sdc -output $SDC_FILE
```


| Output File       | Description                                      |
|-------------------|--------------------------------------------------|
| LEF               | Physical abstract for top-level reuse            |
| SPEF              | Parasitic information for accurate timing        |
| Verilog           | Final routed netlist with connectivity           |
| SDC               | Updated timing constraints post-routing          |

---

## Post-Routing Timing Analysis

Once routing is complete, timing analysis is performed on the `func_slow` scenario using the routed SPEF file.

### Maximum (Setup) Path Report – func_slow

```tcl
report_timing -scenario func_slow -delay_type max 
```

- Checks the **longest paths** in the design.
- Validates **setup timing violations**.
- Uses parasitic data from the routed design.

### Minimum (Hold) Path Report – func_slow

```tcl
report_timing -scenario func_slow -delay_type min 
```

- Checks the **shortest paths**.
- Validates **hold timing violations** to ensure signals don’t arrive too early.

| Timing Report Type | Scenario     | Command                                    |
|--------------------|--------------|--------------------------------------------|
| Setup (Max Delay)  | `func_slow`  | `report_timing -delay_type max`            |
| Hold (Min Delay)   | `func_slow`  | `report_timing -delay_type min`            |

---

## Tool Used

- Tool: Synopsys ICC2
- Design: Flat RTL-to-GDSII flow
- Language: TCL Scripting

