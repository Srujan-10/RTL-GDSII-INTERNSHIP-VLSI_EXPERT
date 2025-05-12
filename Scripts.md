```
set RTL_SOURCE_FILES /ve/ti_home/ti_srujan_658/all_rtl_files/full_adder.v
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
source ./rm_setup/dc_setup.tcl
define_design_lib WORK -path ./WORK
set_app_var hdlin_enable_hier_map true
set_dont_use [get_lib_cells */FADD*]
set_dont_use [get_lib_cells */INV*]
set_dont_use [get_lib_cells */HADD*]
set_dont_use [get_lib_cells */NAND*]
set_dont_use [get_lib_cells */OR*]
analyze -format verilog $RTL_SOURCE_FILES
elaborate $DESIGN_NAME
create_clock -period 1 [get_ports Clock]
set_input_delay -max 0.5 -clock Clock [all_inputs]
set_input_transition 0.5 [all_inputs]
set_output_delay -max 0.5 -clock Clock [all_outputs]
set_clock_uncertainty -setup 0.300 [get_clocks Clock]
set_clock_uncertainty -hold 0.100 [get_clocks Clock]
set_max_transition 0.250 [current_design]
set_max_transition -clock_path 0.150 [get_clocks Clock]
set_verification_top
compile
write -format verilog -hierarchy -output  ./${RESULTS_DIR}/${DCRM_FINAL_VERILOG_OUTPUT_FILE}
write_sdc /ve/ti_home/ti_srujan_658/all_rtl_files/SDC/mapped_FA_4.sdc

```

```
set MAPPED_NETLIST_FOLDER_PATH ./full_adder.mapped.v 
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
create_lib -ref_libs "/data/pdk/pdk32nm/SAED32_EDK/lib/stdcell_rvt/ndm/saed32rvt_c.ndm" FA_LIB
read_verilog {../DC/results/adder_32bit.mapped.v} -top adder_32bit -design adder_32bit -library FA_LIB
link_block
check_design -checks dp_pre_floorplan
initialize_floorplan -core_offset 3.5
create_port -direction in VDD
create_port -direction in VSS
create_net -power VDD
create_net -ground VSS
save_lib
save_block
connect_pg_net -pg  -automatic 
connect_pg_net -pg  -automatic 
create_shape -shape_type rect -layer M7 -boundary {{0 3.5} {1.9 4.2}} -port VSS
create_shape -shape_type rect -layer M7 -boundary {{0 4.5} {3.15 5.4}} -port VDD
create_pg_ring_pattern core_ring -horizontal_layer M7 -horizontal_width 0.7 -horizontal_spacing 0.4 -vertical_layer M8 -vertical_width 0.8 -vertical_spacing  0.4
set_pg_strategy ring_stratergy -core -pattern {{name:core_ring} {nets:{VDD VSS}} {offset:{0.5 0.5}}}
compile_pg -strategies ring_stratergy


create_pg_mesh_pattern mesh  -layers { {{vertical_layer:M6} {width:0.38} {spacing:interleaving} {pitch:1.3} {offset:0.45}} {{horizontal_layer:M5} {width:0.34} {spacing:interleaving} {pitch:1} {offset:0.45}} }
set_pg_strategy core_mesh -pattern {{pattern:mesh} {nets:VDD VSS}}  -extension {stop:innermost_ring} -core
compile_pg -strategies core_mesh


create_pg_std_cell_conn_pattern std_cell_rail -layers {M1} -rail_width 0.8
set_pg_strategy  rail_stratergy -core -pattern {{name:std_cell_rail} {nets:VDD VSS}}
compile_pg -strategies rail_stratergy
```

```
connect_pg_net -pg  -automatic 
create_shape -shape_type rect -layer M7 -boundary {{0 3.5} {1.9 4.2}} -port VSS
create_shape -shape_type rect -layer M7 -boundary {{0 4.5} {3.15 5.4}} -port VDD
create_pg_ring_pattern core_ring -horizontal_layer M7 -horizontal_width 0.7 -horizontal_spacing 0.4 -vertical_layer M8 -vertical_width 0.8 -vertical_spacing  0.4
set_pg_strategy ring_stratergy -core -pattern {{name:core_ring} {nets:{VDD VSS}} {offset:{0.5 0.5}}}
compile_pg -strategies ring_stratergy


create_pg_mesh_pattern mesh  -layers { {{vertical_layer:M6} {width:0.38} {spacing:interleaving} {pitch:1.3} {offset:0.45}} {{horizontal_layer:M5} {width:0.34} {spacing:interleaving} {pitch:1} {offset:0.45} }} {  {vertical_layer:M4} {width:0.3} {spacing:interleaving} {pitch:0.67} {offset:0.45}   }   }
set_pg_strategy core_mesh -pattern {{pattern:mesh} {nets:VDD VSS}}  -extension {stop:innermost_ring} -core
compile_pg -strategies core_mesh


create_pg_std_cell_conn_pattern std_cell_rail -layers {M1} -rail_width 0.75
set_pg_strategy  rail_stratergy -pg_regions ALU_CORE -pattern {{name:std_cell_rail} {nets:VDD VSS}}
compile_pg -strategies rail_stratergy
save_lib
save_block -as powerplan
check_pg_drc >> ./reports/pg_drc.rpt
check_pg_connectivity >> ./reports/pg_connectivity.rpt
check_legality >> ./reports/pg_legality.rpt
report_design -all >> ./reports/report_design_pg.rpt
report_timing >>  ./reports/powerplan_timing.rpt

create_pg_ring_pattern core_ring -horizontal_layer M7 -horizontal_width 1.5 -horizontal_spacing 0.4 -vertical_layer M8 -vertical_width 1.5 -vertical_spacing  0.4
     set_pg_strategy ring_stratergy -core -pattern {{name:core_ring} {nets:{VDD VSS}} {offset:{1 1}}}
     compile_pg -strategies ring_stratergy

create_pg_mesh_pattern mesh  -layers { {{vertical_layer:M6} {width:0.5} {spacing:interleaving} {pitch:2} {offset:0.7}} {{horizontal_layer:M7} {width:0.567} {spacing:interleaving} {pitch:1.56} {offset:0.8} } }
set_pg_strategy core_mesh -pattern {{pattern:mesh} {nets:VDD VSS}}  -extension {stop:innermost_ring} -pg_regions ALU_CORE

     compile_pg -strategies core_mesh

```
```
#open_lib ALU_LIB2
#open_block powerplan
link_block
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
set SDC_PATH "/ve/ti_home/ti_srujan_658/Desktop/Hierarchal_eight_bit/DC/results/full_adder.sdc"
read_sdc $SDC_PATH
set_app_options -name place.coarse.continue_on_missing_scandef -value true
set_app_options -name place_opt.flow.enable_ccd -value true
set_app_options -name place_opt.flow.clock_aware_placement -value true
set_app_options -name place_opt.place.congestion_effort -value high
set mode1 "func"
set corner1 "slow"
set scenalink_block
set PDK_PATH /data/pdk/pdk32nm/SAED32_EDK
set SDC_PATH "/ve/ti_home/ti_srujan_658/Desktop/Hierarchal_eight_bit/DC/results/full_adder.sdc"
read_sdc $SDC_PATH
set_app_options -name place.coarse.continue_on_missing_scandef -value true
set_app_options -name place_opt.flow.enable_ccd -value true
set_app_options -name place_opt.flow.clock_aware_placement -value true
set_app_options -name place_opt.place.congestion_effort -value high
set mode1 "func"
set corner1 "slow"
set scenarrio "${mode1}_${corner1}"
create_mode $mode1
create_corner $corner1
create_scenario -name $scenario -mode $mode1 -corner $corner1
set parasitic "p0"
read_parasitic_tech -tlup "/data/pdk/pdk32nm/SAED32_EDK/tech/star_rcxt/saed32nm_1p9m_Cmax.tluplus" -layermap "/data/pdk/pdk32nm/SAED32_EDK/tech/star_rcxt/saed32nm_tf_itf_tluplus.map" -name p0

set_parasitic_parameters -late_spec $parasitic -early_spec $parasitic
create_placement -effort high
set_dont_use [get_lib_cells */FADD*]
set_dont_use [get_lib_cells */HADD*]
set_dont_use [get_lib_cells */NAND*]
set_dont_use [get_lib_cells */OR*]
set_scenario_status func_slow -hold true -setup true -leakage_power true -max_capacitance true -min_capacitance true -dynamic_power true -max_transition true -active true
place_opt
legalize_placement
report_timing >> ./reports/report_timing_placement.rpt
report_design -all >>  ./reports/placement_report_design.rpt
report_constraints >> ./reports/constraints_place.rpt

```
```
set SDC_PATH "/ve/ti_home/ti_srujan_658/Desktop/Hierarchal_eight_bit/DC/results/full_adder.sdc"
read_sdc $SDC_PATH
check_design -checks pre_clock_tree_stage
set_scenario_status func_slow -hold true -setup true -leakage_power true -max_capacitance true -min_capacitance true -dynamic_power true -max_transition true -active true
synthesize_clock_trees
clock_opt
report_timing -delay_type min >> ./reports/hold_rpt_cts.rpt
save_block -as cts_complete

```
```
check_design -checks pre_route_stage
set_app_options -name route.global.timing_driven -value true
set_app_options -name route.global.crosstalk_driven -value true
set_app_options -name route.track.timing_driven -value true
set_app_options -name route.track.crosstalk_driven -value true
set_app_options -name route.detail.timing_driven -value true
set_app_options -name route.detail.antenna -value true
set_app_options -name route.detail.antenna_fixing_preference -value use_diodes
set_app_options -block [current_design ] -name route.detail.diode_libcell_names -value {*/ANTENNA_RVT}
route_global
route_track
route_detail
set_app_options -name route_opt.flow.enable_ccd -value true
route_opt
save_block -as routing_complete
save_block -as routing_complete
report_timing >> max_timing_post_route1.rpt
report_timing -delay_type min >> post_route_min_timing1.rpt
write_verilog  routed_netlist_full_adder.v
write_lef FULL_ADDER.lef
create_frame
write_parasitics -format spef -output FULL_ADDER.spef 
save_block -as routing_complete
report_congestion >> post_route_congestion1.rpt

```
```
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/adder32bit/ICC2/FA_LIB/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/subractor/ICC2/SUB/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/multiplier/ICC2/MULTIPLIER_LIB/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/mux/ICC2/MUX_8X1/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/divider/ICC2/DIVIDER/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/comparator/ICC2/COMP_LIB/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/shifter/ICC2/SHIFTER/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/flag_generator/ICC2/FLAG_GEN/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/clipper_unit/ICC2/CLIPPER_LIB/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/saturate_unit/ICC2/SATURATE_UNIT/
/ve/ti_home/ti_srujan_658/ALU_10_SOFT_MACRO/Synthesis/Clock_gen/ICC2/Clock_gen/

```
