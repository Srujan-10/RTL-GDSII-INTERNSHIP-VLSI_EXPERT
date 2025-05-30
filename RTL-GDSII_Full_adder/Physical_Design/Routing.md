# Routing – 4-Bit Full Adder (Flat Design)

This section describes the **Routing** stage in the physical design flow of a 4-bit full adder using Synopsys ICC2. It includes the key concepts, routing script explanation, and post-routing timing reports for setup and hold paths.

---

## What is Routing in Physical Design?

Routing is the process of physically connecting all nets in the design by placing metal wires and vias on different metal layers. It consists of:

1. **Global Routing** – Determines rough paths on a routing grid.
2. **Track Assignment** – Maps nets to specific metal tracks.
3. **Detailed Routing** – Finalizes all physical routes and checks for DRC violations.

---
## Reference Design Inforamtion :


![Screenshot 2025-05-13 001405](https://github.com/user-attachments/assets/c601452c-a401-4a4b-9093-0054a676bb0b)


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

#### Congestion Report : 

![image](https://github.com/user-attachments/assets/237cb87f-9433-45d5-a4ba-22c93e4c9001)

### Layer wise report  ;

![image](https://github.com/user-attachments/assets/d2936144-2c21-47f2-b1e6-99f82063756e)

![image](https://github.com/user-attachments/assets/b5a55dc4-62d3-4153-b762-b84b591c7ca5)

![image](https://github.com/user-attachments/assets/b9952ec4-ae6e-456c-a062-68fafca4121a)

![image](https://github.com/user-attachments/assets/b53bae3e-5773-4616-938c-0d48c7e1e166)

![image](https://github.com/user-attachments/assets/aa259318-fe90-4142-9621-d275373d660c)

![image](https://github.com/user-attachments/assets/2f7b3c0f-7df6-4aad-879f-d29aaf16911a)

![image](https://github.com/user-attachments/assets/19e334f1-83c6-4af2-8ced-766844c9f616)

![image](https://github.com/user-attachments/assets/a656f7e8-912a-447f-ba67-933cb3e69564)

![image](https://github.com/user-attachments/assets/006bec15-2159-4997-a61d-1ab18ad705f1)

 As we know most of the congestion occurs in the lower layers like M4, M3 , M2 ,M1 due to high cell density and pins so more routes are required to connect.
So the congestion in upper layers abve M4 or M5dont have much congestion .

Report Cells :

![image](https://github.com/user-attachments/assets/e3eb011c-9505-469a-b6ba-4d646c82e933)

### Qor report : 

![image](https://github.com/user-attachments/assets/5b47f789-51b9-4675-acbc-2315423a31d7)

![image](https://github.com/user-attachments/assets/68c63175-7d85-4c7d-a39e-2cdc0142b5ef)

![image](https://github.com/user-attachments/assets/a4684290-42e6-42e7-bd14-db80ea14aa70)

### Utilization Report :

![image](https://github.com/user-attachments/assets/a71c1180-d27a-44e3-84b0-5cc1e6907b2c)

### report Scenarios : 

![image](https://github.com/user-attachments/assets/58a556a8-c0d4-4d4b-9e19-d02ebbf2fc25)


### Output File Generation

```tcl
write_lef $LEF_FILE
write_parasitics -format spef -output $SPEF_FILE
write_verilog $routed_netlist
write_sdc -output $SDC_FILE
```

![Screenshot 2025-05-13 004323](https://github.com/user-attachments/assets/d066fa96-3f53-4280-a1dc-b5ec7fc23a77)



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

![Screenshot 2025-05-13 000816](https://github.com/user-attachments/assets/68ae1dc4-a2f9-466e-9ff6-50e10db7c74a)


- Checks the **longest paths** in the design.
- Validates **setup timing violations**.
- Uses parasitic data from the routed design.

### Minimum (Hold) Path Report – func_slow

```tcl
report_timing -scenario func_slow -delay_type min 
```

![Screenshot 2025-05-13 000831](https://github.com/user-attachments/assets/8bd5b0b6-bf29-43a3-9b54-0157ea2cc280)


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

