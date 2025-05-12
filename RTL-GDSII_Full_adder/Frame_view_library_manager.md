
# Frame View Generation – 4-Bit Full Adder (Flat Design)

This section explains the purpose and creation of a **frame view** for the 4-bit full adder block. A frame view is useful for visual inspection and design packaging, and can be opened in the **Library Manager** of Synopsys ICC2.

---

## What is a Frame View?

A **frame view** is a graphical abstraction of a block or cell that includes:

- Placement and routing boundaries
- I/O pin placement
- Power ring/mesh structures
- Cell and macro outlines

The frame helps designers **visualize the final layout**, **inspect pin assignments**, and **verify die/core settings** without reloading the full design into ICC2.

---

## Purpose of Frame View

| Feature               | Description                                                                 |
|-----------------------|-----------------------------------------------------------------------------|
| Block-Level Visualization | Visualizes layout boundaries, pin locations, power rings, etc.           |
| Design Reusability    | Enables exporting the block as a physical hard macro                        |
| Integration Aid       | Useful when integrating this block into a top-level SoC or multi-block design |
| Pin Planning Review   | Helps confirm correct pin directions and symmetry before GDS export        |

---

## TCL Command to Create Frame

```tcl
create_frame full_adder.frame
```

This command generates a `.frame` file for the currently loaded design. This file includes:

- Die/Core dimensions
- Pin geometry and direction
- Placement outlines
- Power structure outlines

> **Note**: The design must already be floorplanned and routed before generating a valid frame.

---

## Viewing Frame in Synopsys ICC2 Library Manager

Once the frame is generated, you can view it using the **Library Manager** GUI in ICC2.

### Steps to View in Library Manager:

1. invke lm_shell
2. start_gui.
3. Locate and load the file `full_adder.frame`.
4. The frame will render as a graphical layout of the block:
   - Core/die outline
   - Pins with directions (input/output)
   - Any visible power or cell structures

![Screenshot 2025-05-13 005501](https://github.com/user-attachments/assets/96f91502-d2f0-4322-8fd4-c3f4ee5b73a1)

Layput View in library manager : 

![Screenshot 2025-05-13 005524](https://github.com/user-attachments/assets/fa0cf545-de69-4215-a26f-34e148856dd4)

This allows you to verify layout structure **without opening the full database**.

---

## Best Practices

| Tip                                   | Benefit                                                        |
|--------------------------------------|----------------------------------------------------------------|
| Generate frame after full routing    | Ensures final pin and routing info is included                 |
| Use consistent naming (`<block>.frame`) | Helps manage versions for hierarchical designs                |
| Validate pin directions and names    | Prevents integration issues in top-level layout                |
| Use in documentation or reviews      | Provides a visual reference for collaborators or reviews       |

---

## Tool and Flow Info

- Tool Used: Synopsys ICC2
- View Type: Frame view (.frame)
- Creation Command: `create_frame full_adder.frame`
- Viewer: Library Manager (lm_shell).
