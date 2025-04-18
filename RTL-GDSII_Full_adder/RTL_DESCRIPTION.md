This Verilog module is a 4-bit full adder designed to perform binary addition. It takes two 4-bit inputs (`A` and `B`), a carry-in bit (`C_in`), and a clock signal (`Clock`) to produce a 4-bit sum (`SUM`) and a carry-out bit (`C_out`). The module uses both sequential and combinational logic to ensure accurate addition.

## Key Features

### Inputs:
- **`A` (4 bits)**: First number to add.
- **`B` (4 bits)**: Second number to add.
- **`C_in` (1 bit)**: Carry-in bit for addition.
- **`Clock` (1 bit)**: Synchronizes the operation.

### Outputs:
- **`SUM` (4 bits)**: Result of the addition.
- **`C_out` (1 bit)**: Carry-out bit for overflow.

## How It Works

1. **Input Registration**:
    - Inputs (`A`, `B`, `C_in`) are stored in internal registers (`reg1`, `reg2`, `c_in`) on the rising edge of the clock. This ensures stable inputs for the addition.

2. **Addition Logic**:
    - The registered inputs are added using combinational logic:
      ```verilog
      {c_out, sum_i} = reg1 + reg2 + c_in;
      ```
    - This calculates the sum (`sum_i`) and carry-out (`c_out`).

3. **Output Update**:
    - The calculated sum and carry-out are stored in the output registers (`SUM`, `C_out`) on the next clock edge, ensuring synchronized outputs.

## Code Example

```verilog
module full_adder (
          input [3:0] A, B,
          input Clock, C_in,
          output reg [3:0] SUM,
          output reg C_out
);

          reg [3:0] reg1, reg2, sum_i;
          reg c_in, c_out;

          // Store inputs on clock edge
          always @ (posedge Clock) begin
                     reg1 <= A;
                     reg2 <= B;
                     c_in <= C_in;
          end

          // Update outputs on clock edge
          always @ (posedge Clock) begin
                     SUM <= sum_i;
                     C_out <= c_out;
          end

          // Perform addition
          always @ * begin
                     {c_out, sum_i} = reg1 + reg2 + c_in;
          end

endmodule
```

## Applications
- Adding binary numbers in digital circuits.
- Building blocks for arithmetic logic units (ALUs).
- Extending to larger adders by cascading multiple modules.
- Teaching basic digital design concepts.

This module is simple yet powerful, showcasing how sequential and combinational logic work together in digital systems.
