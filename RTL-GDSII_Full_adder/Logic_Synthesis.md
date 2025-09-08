# Full Adder Logical Synthesis (SAED 32nm – Design Compiler)
This repository documents the logical synthesis of a Full Adder using Synopsys Design Compiler (DC) and SAED 32nm PDK. It includes a Verilog RTL design, constraints file, a synthesis script, and output gate-level netlist.

###  Objective
1. Convert a RTL-level Full Adder (Verilog) into a gate-level netlist

2.Use GTECH representation after elaboration for optimization and design-ware libraries like the symbol libraries(.sldb) 

3.Perform technology mapping using SAED32 standard cell libraries and in this case I have used a library with PVT(process-voltage-temperature) corners of rvt , 0.75 and -40 Celsius. 

4.Generate a final synthesized gate level netlist and updated SDC


#### Project directory  Structure
```
full_adder_synthesis/
├── full_adder.v              # RTL Verilog design
├── full_adder.sdc            # Timing constraints
├── dc_full_adder.tcl         # Main synthesis script
├── rm_setup/
│   └── dc_setup.tcl          # Reference Methodology setup (libs, tech files)
├── WORK/                     # Compiled working library
└── results/                  # Synthesized output netlist and constraints
```
### Synthesis Script (dc_full_adder.tcl) with Line-by-Line Explanation

#### 1. Set the PDK path (used for loading technology libraries)
```
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
```
Explanation:
This variable points to the Process Design Kit (PDK), which includes liberty (.lib) files, LEF/DEF files, and technology information for synthesis and layout.

#### 2. Load DC Reference Methodology setup (sets libraries, search paths, etc.)
```
source -echo -verbose ./rm_setup/dc_setup.tcl
```
Explanation:
This script initializes the synthesis environment. It typically sets:

Search paths to libraries (set_app_var search_path)

Technology-specific libraries (set target_library, set link_library)

Default settings for optimization
The -echo -verbose prints all commands being sourced for transparency.


#### 3. Define your RTL Verilog source file
```
set RTL_SOURCE_FILES ./full_adder.v
```
Explanation:
This defines the input RTL file to be analyzed and synthesized.

#### 4. Create a working design library
```
define_design_lib WORK -path ./WORK
```
Explanation:
Defines a local compiled library (WORK) where Design Compiler stores intermediate design states during synthesis.


#### 5. Enable hierarchical mapping (optional for large designs)
```
set_app_var hdlin_enable_hier_map true
```
Explanation:
Enables hierarchical design elaboration which helps in managing large or modular RTL designs.

#### 6. Restrict usage of specific complex cells
```
set_dont_use [get_lib_cells */FADD*]
set_dont_use [get_lib_cells */HADD*]
set_dont_use [get_lib_cells */MUX*]
set_dont_use [get_lib_cells */AO*]
set_dont_use [get_lib_cells */OA*]
set_dont_use [get_lib_cells */NAND*]
set_dont_use [get_lib_cells */XOR*]
set_dont_use [get_lib_cells */NOR*]
set_dont_use [get_lib_cells */XNOR*]
```
Explanation:
These commands disable certain complex or power-hungry standard cells.
This encourages the tool to synthesize the logic using only basic gates or cells preferred for optimization (e.g., in educational projects or for area control).

#### 7. Analyze the RTL file
```
analyze -format verilog ${RTL_SOURCE_FILES}
```
Explanation:
This reads the RTL code and checks for syntax/semantic correctness, but does not build the design yet.

# 8. Elaborate the top-level design
```
elaborate ${DESIGN_NAME}
```
Explanation:

Converts the RTL into an intermediate GTECH netlist (generic technology-independent cells).

At this point, the design is not yet mapped to any specific library cells.

GTECH is Synopsys's internal generic library (e.g., GTECH_AND2, GTECH_XOR3) used for pre-technology optimizations.

GTECH Mapping occurs here

![WhatsApp Image 2025-05-12 at 00 17 53_021813ad](https://github.com/user-attachments/assets/9e9936c0-d363-4a2b-a119-a3b58d4c998b)


#### 9. Set the current design context
```
current_design
```
Explanation:
Sets the design under focus (e.g., full_adder) so subsequent commands apply to it.

#### 10. Set top-level for verification
```
set_verification_top
```
Explanation:
Defines the top-level module for verification and reporting purposes (optional).

#### 11. Load timing constraints (clock, input/output delays, etc.)

```
read_sdc -echo ./full_adder.sdc
```
```
create_clock -period 1 [get_ports Clock]
set_input_delay -max 0.5 -clock Clock [all_inputs]
set_input_transition 0.1 [all_inputs]
set_output_delay -max 0.5 -clock Clock [all_outputs]
set_clock_uncertainty -setup 0.300 [get_clocks Clock]
set_clock_uncertainty -hold 0.100 [get_clocks Clock]
set_max_transition 0.2 [current_design]
set_max_transition -clock_path 0.04 [get_clocks Clock]
```
Explanation :
Reads the Synopsys Design Constraints (SDC) file. This file defines timing constraints such as:

Clock definitions

Input/output delays

Load constraints

# 12. Run synthesis with aggressive optimization
```
compile
```

![image](https://github.com/user-attachments/assets/bb5a3f6f-b850-46a7-9a79-0d8b97cebd25)


For end to end optimization use :
```
compile_ultra
```

Explanation:

Performs synthesis, logic optimization, and technology mapping to standard cells.

Targets delay, area, and power optimization based on your constraints.

✅ Actual Technology Mapping happens here

Now the design is built using SAED 32nm standard cells (e.g., NAND2X1, INVX1, etc.), replacing GTECH components.

![WhatsApp Image 2025-05-13 at 17 02 32_68c30f71](https://github.com/user-attachments/assets/db6b9cf2-dfff-4ecb-9b8b-0a4f046ce843)



# 13. Export the final gate-level netlist
```
write -format verilog -hierarchy -output ${RESULTS_DIR}/${DCRM_FINAL_VERILOG_OUTPUT_FILE}
```
Explanation:
Writes the technology-mapped Verilog netlist to the results/ directory.
The -hierarchy flag ensures all modules are preserved in hierarchical form.


#### 14. Export the updated constraints file
```
write_sdc ./${RESULTS_DIR}/${DCRM_FINAL_SDC_OUTPUT_FILE}
```
Explanation:
Outputs the SDC after possible changes during synthesis (e.g., adjusted clock propagation, load adjustments).

Key Concepts
Stage	Description
GTECH Mapping	Happens after elaborate. The RTL is converted to generic gates like GTECH_AND, GTECH_XOR. These are not tied to any technology library.
Technology Mapping	Happens during compile_ultra. GTECH gates are replaced with actual library cells (e.g., NAND2X1, XOR3X2) based on SAED32's .lib files.
Optimization	Area, timing, and power are optimized using constraints (from .sdc) during compile_ultra.

✅ Output Files (inside results/)
full_adder_synth.v → Gate-level netlist

full_adder_out.sdc → Updated constraint file

### Reports : 

1. Setup Timing Report  :


Timing Report before compile command :


![WhatsApp Image 2025-05-12 at 00 24 18_00c5488a](https://github.com/user-attachments/assets/7b0b456a-082f-4791-8f56-66a1aba195f2)




Timing Report before after compile command 



![WhatsApp Image 2025-05-13 at 17 05 23_7c141fad](https://github.com/user-attachments/assets/3e7e2961-d7f0-4fab-9b58-157c43a25f19)






As we can see the setup slack is -0.17 and its not  met .

2. Hold timing report :

Timing Report before compile command : 



![WhatsApp Image 2025-05-12 at 00 28 08_af285402](https://github.com/user-attachments/assets/37259670-50ab-4939-bf14-43c83800c013)





Timing Report before after compile command :

![WhatsApp Image 2025-05-13 at 17 07 06_cf0b95de](https://github.com/user-attachments/assets/7c70fa2c-e1c5-4e4b-b27b-ce2c1505c682)



As we can see the hold timing is violated.

3.  qor   report :



![WhatsApp Image 2025-05-13 at 17 09 15_20fc239d](https://github.com/user-attachments/assets/e6b6fe23-babf-4b96-8c9d-aa74071e45a8)





4. Constraint report :

![WhatsApp Image 2025-05-12 at 00 30 19_0e41cead](https://github.com/user-attachments/assets/289ed581-4017-4eaa-ac44-800d7729411e)






