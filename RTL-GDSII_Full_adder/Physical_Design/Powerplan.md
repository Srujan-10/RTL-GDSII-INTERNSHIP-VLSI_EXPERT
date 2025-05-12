
#  Power Planning in Physical Design (Flat 4-Bit Full Adder)

This section outlines the power planning process for a 4-bit full adder using a flat design approach. Power planning is critical to ensure reliable power delivery across the chip during operation.

------------------------------------------------------------

 What is Power Planning?

Power planning is a crucial step in physical design that involves the creation and connection of power (VDD) and ground (VSS) networks to supply all components of the IC. This includes building power distribution networks using rings, meshes, and rails.

------------------------------------------------------------

 Power Delivery Components

1. Power Rings
- Surround the core area and serve as the main supply source.
- Distribute VDD and VSS around the core for tapping into mesh or rails.

2. Power Mesh
- A grid-like network over the core to deliver uniform power across the entire area.
- Helps reduce IR drop and improves power integrity.
- Built using multiple metal layers with controlled spacing and pitch.

3. Standard Cell Rails
- Horizontal stripes in the standard cell rows to deliver power directly to cells.
- Typically located in Metal 1 (M1).

------------------------------------------------------------

 Power Planning Script Breakdown

1. Automatic Power Net Connection
```
connect_pg_net -pg -automatic
```
Automatically connects standard power nets (VDD and VSS) to the design, preparing for shape and pattern generation.

------------------------------------------------------------

2. Create Rectangular Shapes for VDD and VSS
```
create_shape -shape_type rect -layer M7 -boundary {{0 3.5} {1.9 4.2}} -port VSS
create_shape -shape_type rect -layer M7 -boundary {{0 4.5} {3.15 5.4}} -port VDD
```
These commands create custom rectangular power shapes on layer M7 using specified boundary coordinates.

------------------------------------------------------------

3. Create Power Ring Pattern
```
create_pg_ring_pattern core_ring \
  -horizontal_layer M7 -horizontal_width 0.7 -horizontal_spacing 0.4 \
  -vertical_layer M8 -vertical_width 0.8 -vertical_spacing 0.4
```
Defines the core_ring pattern with:
- Horizontal ring: M7, width = 0.7, spacing = 0.4
- Vertical ring: M8, width = 0.8, spacing = 0.4

------------------------------------------------------------

4. Set Power Ring Strategy
```
set_pg_strategy ring_stratergy -core \
  -pattern {{name:core_ring} {nets:{VDD VSS}} {offset:{0.5 0.5}}}
```
Applies the core_ring pattern as a ring strategy around the core, targeting VDD and VSS with an offset of 0.5 units.

------------------------------------------------------------

5. Compile Ring Strategy
```
compile_pg -strategies ring_stratergy
```
Generates the physical power ring shapes in the layout.

![Screenshot 2025-05-12 233343](https://github.com/user-attachments/assets/1bc61982-a9b4-4235-a128-c827b72c0ac0)

------------------------------------------------------------

6. Create Power Mesh Pattern
```
create_pg_mesh_pattern mesh \
  -layers {
    {{vertical_layer:M6} {width:0.38} {spacing:interleaving} {pitch:1.3} {offset:0.45}} 
    {{horizontal_layer:M5} {width:0.34} {spacing:interleaving} {pitch:1} {offset:0.45}}
  }
```
Defines a power mesh pattern:
- Vertical mesh: Layer M6, width = 0.38, pitch = 1.3
- Horizontal mesh: Layer M5, width = 0.34, pitch = 1.0

------------------------------------------------------------

7. Set and Compile Mesh Strategy
```
set_pg_strategy core_mesh -pattern {{pattern:mesh} {nets:VDD VSS}} \
  -extension {stop:innermost_ring} -core
```
```
compile_pg -strategies core_mesh
```
Applies and compiles the mesh strategy:
- Connects to VDD and VSS
- Mesh stops at the innermost power ring

![Screenshot 2025-05-12 233632](https://github.com/user-attachments/assets/43aadb9e-e4ab-4cc5-b6f1-19d862fe5eaf)


------------------------------------------------------------

8. Create Standard Cell Rail Pattern
```
create_pg_std_cell_conn_pattern std_cell_rail -layers {M1} -rail_width 0.8
```
Defines rail power pattern in Metal 1 with 0.8 width.

------------------------------------------------------------

9. Set and Compile Rail Strategy
```
set_pg_strategy rail_stratergy -core -pattern {{name:std_cell_rail} {nets:VDD VSS}}
compile_pg -strategies rail_stratergy
```

![Screenshot 2025-05-12 233747](https://github.com/user-attachments/assets/a043a11c-7afa-44ad-bc68-c6ba21473750)


Applies the rail pattern strategy for standard cell rows and connects it to VDD and VSS.
```
check_pg_connectivity :
```
![Screenshot 2025-05-12 235837](https://github.com/user-attachments/assets/70f630a8-f132-4afe-bfb3-27718c2be042)

```
check_pg_drc :
```
![Screenshot 2025-05-12 233830](https://github.com/user-attachments/assets/bd40e813-7e03-4fd6-a907-e38a0fe222e1)

```
check_pg_missing_vias : 
```
![Screenshot 2025-05-12 233811](https://github.com/user-attachments/assets/1b2ca0b0-498a-4dc2-8658-281b941f567d)




------------------------------------------------------------

### Summary of Power Plan

| Element         | Description                           |
|----------------|----------------------------------------|
| Power Rings     | M7 (Horizontal) / M8 (Vertical)       |
| Power Mesh      | M5 (Horizontal) / M6 (Vertical)       |
| Power Rails     | M1, for standard cell power           |
| Power Nets      | VDD, VSS                              |
| Commands Used   | connect_pg_net, create_shape, compile_pg, etc. |

------------------------------------------------------------

##### Tools Used

- Physical Design Tool: ICC2
- Scripting Language: TCL

