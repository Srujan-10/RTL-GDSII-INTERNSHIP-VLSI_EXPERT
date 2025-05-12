#  Floorplanning of a 4-Bit Full Adder (Flat Design)

This project involves the floorplanning of a **4-bit full adder** using a flat design approach. Below is a complete overview of the floorplanning agenda, parameters used, and checks performed.

---

##  Objective

The **goal of floorplanning** is to map the synthesized netlist onto a physical chip layout efficiently and prepare for placement and routing. It ensures optimal area usage, performance, and routability.

---

## Floorplanning Agenda

### 1. Define Die and Core Area

- **Die Area**: `23.72 x 24.48`  
- **Core Area**: `16.72 x 17.48`

Sanity check : 
```
check_design -checks dp_pre_floorplan
```
![WhatsApp Image 2025-05-12 at 15 34 48_ad0238da](https://github.com/user-attachments/assets/59957b52-d332-4862-8a70-aac32daa2f8c)


These values define the overall chip boundary and the usable space for standard cell placement.

---

### 2. Set Core Offset

- **Offset**: `3.5` units from the die edge to core boundary  
- **Command:**
  ```
  initialize_floorplan -core_offset 3.5
```
```
![Screenshot 2025-05-12 233048](https://github.com/user-attachments/assets/add01caa-c80a-472b-9f34-506ac1e9d1d5)

![Screenshot 2025-05-12 232806](https://github.com/user-attachments/assets/5a77b782-4a44-4c5b-a283-f3cef1364816)

