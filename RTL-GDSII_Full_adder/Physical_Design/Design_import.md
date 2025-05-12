# ICC2 Design Import Report: `full_adder` Module

## What is ICC2?

**Synopsys IC Compiler II (ICC2)** is a full-flow digital physical implementation tool used for place-and-route (P&R) of synthesized RTL netlists. It handles the physical realization of a chip, transforming a synthesized gate-level netlist into a physically routed GDSII layout. ICC2 ensures that the design meets all timing, power, area, and manufacturability constraints.

Key features include:

- Advanced placement and routing  
- Clock tree synthesis (CTS)  
- Timing analysis and optimization  
- Floorplanning and congestion analysis  
- PPA-driven design closure  

## Objective of this ICC2 Run

This ICC2 flow focuses on importing a synthesized `full_adder` design for further physical design steps such as floorplanning, placement, and routing.

### Primary goals:

- Import gate-level netlist, constraints, and LEF/SPEF data into ICC2  
- Establish a working design environment and library references  
- Prepare for physical implementation steps including floorplanning and placement  

## Files Used

- Netlist: `results/full_adder.mapped.v`  
- SDC (timing constraints): `routed.full_adder.sdc`  
- SPEF (parasitic data): `full_adder.spef` (if post-route)  
- LEF (macro and IO definitions): `full_adder.lef`  
- Standard Cell Library: `saed32rvt_c.ndm` from SAED32 EDK

## ICC2 TCL Script (`icc2_import.tcl`)


### Set up paths
```
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
set LEF_FILE "full_adder.lef"
set SPEF_FILE "full_adder"
set routed_netlist "routed.full_adder.v"
set SDC_FILE "routed.full_adder.sdc"
set SDC_PATH "./results/full_adder.mapped.sdc"
```
### Create a design library and reference technology database
```
create_lib -ref_libs "$PDK_PATH/lib/stdcell_rvt/ndm/saed32rvt_c.ndm"  -technology "/data/pdk/pdk32nm/SAED32_EDK/tech/milkyway/saed32nm_1p9m_mw.tf"  FULLADDER
```
####  Understanding `.ndm` and LEF Data in ICC2

### What is an `.ndm` File?

The `.ndm` (New Data Model) is Synopsys' unified technology and cell library format used in ICC2 and other advanced digital implementation tools. It consolidates various traditional formats into a single compact binary model.

An `.ndm` file typically replaces the need for:

- `.lef` – Physical layout abstracts  
- `.lib` – Timing and logical library information  
- `.db` – Synthesized technology representation  
- `.tf` – Technology file for routing layers and rules  

---

### What Does `.ndm` Include?

| Data Type                     | Included in `.ndm`? | Source Format             |
|------------------------------|----------------------|-------------------------- |
| Standard cell abstracts      | ✅ Yes               | from `.lef`              |
| Logical and timing data      | ✅ Yes               | from `.lib`              |
| Technology layers and rules  | ✅ Yes               | from `.tf`               |
| Routing tracks, vias, etc.   | ✅ Yes               | from `.techlef` or `.tf` |

>  If your `.ndm` is generated properly by the PDK provider or your internal flow, it already includes LEF-equivalent data and more.

---

### When is `.lef` Still Required?

You may still need to manually provide `.lef` files in the following cases:

- Using **custom macros/IP blocks** not bundled in the `.ndm`
- Integrating **3rd-party hard IP** (e.g., memory macros, analog blocks)
- Early design stages where **abstracts are manually created**
- Verification of LEF content for debugging or floorplanning scripts

---

### How to Inspect the Contents of `.ndm` in ICC2

You can inspect what’s inside an `.ndm` using the following ICC2 commands:
```
report_lib FA
report_lib_cells FA/*
```

### Read the synthesized Verilog netlist
```
read_verilog {./results/full_adder.mapped.v} -top full_adder -design full_adder -library FULLADDER
```
After this command you can see the following on the terminal like how many top level ports ,modules instances  and nets   are there in your design :

![WhatsApp Image 2025-05-12 at 15 38 00_67ea2168](https://github.com/user-attachments/assets/0d8b819e-1a03-428c-9408-9d442ef5c669)



### Link the design block
```
link_block
```

![WhatsApp Image 2025-05-12 at 15 38 50_88677991](https://github.com/user-attachments/assets/51ddab81-a02b-4b51-83d2-758378a43fb7)


After this command you can see the following on the terminal like how many ports are there in your design : 





