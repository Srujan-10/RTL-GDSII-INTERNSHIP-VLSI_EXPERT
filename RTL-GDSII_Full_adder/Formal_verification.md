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
![WhatsApp Image 2025-05-14 at 13 54 32_4ccad3b7](https://github.com/user-attachments/assets/620151e7-1e11-4a55-a0a8-2341e70bbdd7)


### Run formal equivalence check
```
verify
```
![WhatsApp Image 2025-05-14 at 13 55 33_76ae0a07](https://github.com/user-attachments/assets/1b33ce00-86f1-4643-9929-01e5b0f02b18)


![WhatsApp Image 2025-05-14 at 13 59 22_ba186d45](https://github.com/user-attachments/assets/fa719ec5-7fc5-4038-8f06-1031ae46893e)

![WhatsApp Image 2025-05-14 at 13 55 33_2a762f8e](https://github.com/user-attachments/assets/ea456a9c-55be-4d52-a5fe-a2a1137cb7b3)

![WhatsApp Image 2025-05-14 at 14 03 59_704ecb9c](https://github.com/user-attachments/assets/505950c9-9e08-413e-b282-562bdb6054ab)

![WhatsApp Image 2025-05-14 at 14 04 40_1647acf8](https://github.com/user-attachments/assets/5e7ef7c0-60be-43b2-abfb-073cbf834bc1)

