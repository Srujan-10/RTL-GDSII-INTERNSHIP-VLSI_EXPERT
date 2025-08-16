```
####Script to run Design Compiler with Different Scenarios#####
##
set PDK_PATH ./../ref
#
source -echo -verbose ./rm_setup/dc_setup.tcl

#set RTL_SOURCE_FILES ./../rtl/full_adder.v
set RTL_SOURCE_FILES ./eight_bit_adder.v

# To define design lib to store intermediate design files
define_design_lib WORK -path ./WORK

set_app_var hdlin_enable_hier_map true

#SET_DONT_USE for excluding cells   

set_dont_use [get_lib_cells */FADD*]
set_dont_use [get_lib_cells */HADD*]
set_dont_use [get_lib_cells */MUX*]
set_dont_use [get_lib_cells */AO*]
set_dont_use [get_lib_cells */OA*]
set_dont_use [get_lib_cells */NAND*]
set_dont_use [get_lib_cells */XOR*]
set_dont_use [get_lib_cells */NOR*]
set_dont_use [get_lib_cells */XNOR*]

analyze -format verilog ${RTL_SOURCE_FILES}
elaborate ${DESIGN_NAME}
current_design
set_verification_top
read_sdc -echo ./../CONSTRAINTS/full_adder.sdc

compile 

#compile_ultra

#report_timing
#
### To write results ###
write -format verilog -hierarchy -output ${RESULTS_DIR}/${DCRM_FINAL_VERILOG_OUTPUT_FILE}
#
#write_sdc ./${RESULTS_DIR}/${DCRM_FINAL_SDC_OUTPUT_FILE}
#
### To Writes the files needed to load the design in IC Compiler II.
#write_icc2_files -output icc2_files -force
#
```
