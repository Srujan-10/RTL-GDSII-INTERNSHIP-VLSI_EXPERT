
#  Placement in Physical Design (Flat 4-Bit Full Adder)

This section covers the **placement stage** of the physical design flow for a flat 4-bit full adder. Placement is a critical step following floorplanning and power planning.

------------------------------------------------------------

### What is Placement?

Placement is the process of assigning exact physical locations to all standard cells on the chip within the defined core area. It impacts timing, congestion, power, and area efficiency.

------------------------------------------------------------

### Placement Process Overview

1. **Standard Design Constraints (SDC) Setup**
2. **Scenario, Mode, Corner Creation**
3. **Parasitic Technology File Setup**
4. **Cell Usage Control**
5. **Placement Optimization**
6. **Legalization of Placement**

------------------------------------------------------------

#### Placement Script Breakdown

![Screenshot 2025-05-12 234102](https://github.com/user-attachments/assets/8c16ef82-7679-48d1-92dd-a6ba0c852119)


1. Read the SDC File
```
read_sdc -echo $SDC_PATH
```
Loads the design timing constraints (clock, input delay, output delay, etc.)

------------------------------------------------------------

### Site Row : 
![Screenshot 2025-05-12 235252](https://github.com/user-attachments/assets/f77a0526-41c9-4166-acad-10025fa21242)


2. Set Placement and Optimization Options
```
set_app_options -name place.coarse.continue_on_missing_scandef -value true
set_app_options -name place_opt.flow.enable_ccd -value true
set_app_options -name place_opt.flow.clock_aware_placement -value true
set_app_options -name place_opt.place.congestion_effort -value high
```
These commands configure the placement engine:
- Ignore missing scan definitions
- Enable CCD (Clock Concurrent Design)
- Enable clock-aware placement
- Set high effort for congestion optimization

------------------------------------------------------------

3. Create Mode, Corner, and Scenario
```
set mode1 "func"
set corner1 "slow"
set scenario "${mode1}_${corner1}"
create_mode $mode1
create_corner $corner1
create_scenario -name $scenario -mode $mode1 -corner $corner1
```
Defines a specific functional operating condition (`func_slow`) to analyze the chip under slow corner conditions.

------------------------------------------------------------

4. Read Parasitic Technology and Map Layers
```
set parasitic "p0"
read_parasitic_tech \
  -tlup "/data/pdk/pdk32nm/SAED32_EDK/tech/star_rcxt/saed32nm_1p9m_Cmax.tluplus" \
  -layermap "/data/pdk/pdk32nm/SAED32_EDK/tech/star_rcxt/saed32nm_tf_itf_tluplus.map" \
  -name p0

set_parasitic_parameters -late_spec $parasitic -early_spec $parasitic
```
Reads technology-specific resistance/capacitance models (TLU+ files) and maps layers, used for accurate placement analysis.

------------------------------------------------------------

5. Run Initial Placement
```
create_placement -effort high
```
Performs placement with high optimization effort to reduce congestion and improve timing.

------------------------------------------------------------

6. Set Don’t Use Cells
```
set_dont_use [get_lib_cells */*HADD*]
set_dont_use [get_lib_cells */MUX*]
set_dont_use [get_lib_cells */AO*]
set_dont_use [get_lib_cells */OA*]
set_dont_use [get_lib_cells */NAND*]
set_dont_use [get_lib_cells */XOR*]
set_dont_use [get_lib_cells */NOR*]
set_dont_use [get_lib_cells */XNOR*]
set_dont_use [get_lib_cells */FADD*]
```
Prevents the placer from using specific complex or custom logic gates that may affect timing, area, or power.

------------------------------------------------------------

7. Activate Scenario for Analysis
```
set_scenario_status func_slow \
  -hold true -setup true \
  -leakage_power true -max_capacitance true \
  -min_capacitance true -dynamic_power true \
  -max_transition true -active true
```
This sets the timing scenario as active and enables multiple checks like setup, hold, and power.

------------------------------------------------------------

8. Optimize Placement
```
place_opt
```
Performs advanced optimization including timing-driven placement, reordering, and clustering of cells.

------------------------------------------------------------

9. Legalize Placement
```
legalize_placement
```

![Screenshot 2025-05-12 234418](https://github.com/user-attachments/assets/05795d76-9ae6-4ae2-8a4d-ad7436d83a4d)

Ensures all placed cells conform to legal design rules (spacing, orientation, and row alignment).

![Screenshot 2025-05-12 234942](https://github.com/user-attachments/assets/c6f1ed15-adfa-41c3-8637-d8e78f43e9d6)

![Screenshot 2025-05-12 234954](https://github.com/user-attachments/assets/44f83b1a-969b-4dc5-a833-5f6e5b0c9130)

------------------------------------------------------------

Summary of Placement Process

| Step              | Description                                                      |
|------------------|------------------------------------------------------------------|
| SDC Import        | Loads timing constraints from .sdc file                         |
| Placement Effort  | High effort with congestion and clock-aware options             |
| Scenario Setup    | func_slow mode, corner = slow                                   |
| TLU+ RC Model     | SAED32nm parasitic model files for layout-aware optimization    |
| Cell Restrictions | Disallows complex gates (MUX, XOR, AO, etc.)                   |
| Optimization      | Timing and congestion-aware placement via `place_opt`           |
| Legalization      | Finalizes legal cell positions with `legalize_placement`        |

------------------------------------------------------------

🔧 Tools Used

- Physical Design Tool: ICC2
- Scripting Language: TCL


