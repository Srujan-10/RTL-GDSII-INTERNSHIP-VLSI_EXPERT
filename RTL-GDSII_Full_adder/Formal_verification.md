#  Formality Equivalence Checking Report: `full_adder` Module

## What is Synopsys Formality?

**Synopsys Formality** is a formal verification tool used to perform **equivalence checking** between RTL (Register Transfer Level) and synthesized gate-level netlists. It ensures that synthesis tools have not introduced unintended functional changes during the synthesis process. Formality uses mathematical proofs rather than simulation to guarantee that the logic behavior of both versions is equivalent.

Formality is typically used after synthesis to:

- Validate that the synthesized netlist preserves the RTL functionality  
- Catch synthesis bugs or missed constraints  
- Ensure design correctness before place-and-route  
- Improve verification confidence in tape-out-ready designs  

## Objective of this Formality Equivalence Checking Run

The purpose of this Formality run is to verify **functional equivalence** between:

- RTL description of the `full_adder` module (`full_adder.v`)
- Synthesized gate-level netlist (`full_adder.mapped.v`)

Key goals of this run include:

- Ensuring logical equivalence between pre- and post-synthesis designs  
- Detecting mismatches in combinational or sequential logic  
- Validating synthesis constraints and mapping accuracy  
- Establishing a clean and traceable formal verification workflow  

## Files Used

- RTL Source: `full_adder.v`  
- Netlist: `results/full_adder.mapped.v`  
- Standard Cell Library: `saed32rvt_tt0p78vn40c.db` (CCS Liberty format)

## TCL Script for Formality (`formality_script.tcl`)


###  Read RTL into reference container
```
read_verilog -container r -libname WORK -01 {./full_adder.v}
```
```
set_top r:/WORK/full_adder
```
### Read synthesized netlist into implementation container
```
read_verilog -container i -libname WORK -01 {./results/full_adder.mapped.v}
```
```
set_top i:/WORK/full_adder
```
### Load the standard cell library for mapping
```
read_db {/data/pdk/pdk32nm/SAED32_EDK/lib/stdcell_rvt/db_ccs/saed32rvt_tt0p78vn40c.db}
```
### Reassert top module (optional but best practice)
```
set_top i:/WORK/full_adder
```
### Match design structures
```
match

```
![WhatsApp Image 2025-05-12 at 02 10 20_6b237f33](https://github.com/user-attachments/assets/45fde378-44f6-4b3f-83c9-97bc1a64a770)


![WhatsApp Image 2025-05-12 at 02 10 20_742dec9f](https://github.com/user-attachments/assets/25e72005-98b7-4450-853b-53e5388f250a)

### Run formal equivalence check
```
verify
```
![WhatsApp Image 2025-05-12 at 02 10 20_a0d9a9b9](https://github.com/user-attachments/assets/1feb35b3-2d5a-4645-91c7-f6ec615084ca)


![WhatsApp Image 2025-05-12 at 02 10 20_015a7d3f](https://github.com/user-attachments/assets/80024f4c-8bb9-47de-8145-54682f344524)


![WhatsApp Image 2025-05-12 at 02 17 28_7ad5b605](https://github.com/user-attachments/assets/9e5a8f85-f584-4eb3-a870-22efe049b066)



