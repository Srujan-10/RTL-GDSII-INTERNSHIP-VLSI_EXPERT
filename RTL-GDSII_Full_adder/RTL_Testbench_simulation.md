# RTL Debugging and Simulation using Synopsys VCS and VERDI

## 📁 Overview

This repository contains the Verilog implementation and testbench for a **4-bit full adder** with clocked (synchronous) behavior. During simulation, the `SUM` and `C_out` outputs may appear to be **delayed** by one cycle. This document explains **why** that happens and includes details on compiling and running the simulation using **Synopsys VCS** and **Verdi** for waveform debugging.

## RTL  Testbench
code:
```
module tb_full_adder;
    reg [3:0] A, B;
    reg C_in;
    reg Clock;
    wire [3:0] SUM;
    wire C_out;

    // Instantiate DUT
    full_adder uut (
        .A(A),
        .B(B),
        .C_in(C_in),
        .Clock(Clock),
        .SUM(SUM),
        .C_out(C_out)
    );

    // Clock generation: 10-time unit period
    initial begin
        Clock = 0;
        forever #5 Clock = ~Clock;
    end

    // Stimulus generation with added delay after last case
    initial begin
        // Header
        $display("--------------------------------------------------");
        $display("| Time |   A   |   B   | C_in |  SUM  | C_out |");
        $display("--------------------------------------------------");

        // Initialize inputs
        A = 0; B = 0; C_in = 0;
        #2;

        // Apply and observe test cases
        apply_and_wait(4'd1,  4'd2,  1'b0);   // Time ~10
        apply_and_wait(4'd4,  4'd5,  1'b1);   // Time ~20
        apply_and_wait(4'd7,  4'd8,  1'b0);   // Time ~30
        apply_and_wait(4'd10, 4'd11, 1'b0);   // Time ~40
      
        // Add extra delay to observe final value
        #50;

        $display("--------------------------------------------------");
        $finish;
    end

    // Task to apply input and wait for result
    task apply_and_wait;
        input [3:0] a, b;
        input c;
        begin
            A = a; B = b; C_in = c;
            @(posedge Clock); // Wait for posedge to latch inputs
            #1; // Let output settle
            $display("| %4t | %4d | %4d |  %b   |  %4d  |   %b   |", 
                     $time, A, B, C_in, SUM, C_out);
        end
    endtask

    // Dump FSDB for waveform analysis
    initial begin
        $fsdbDumpfile("dump.fsdb");
        $fsdbDumpvars(0, tb_full_adder);
    end
endmodule

```
## RTL Debugging using VCS
🔧 Simulation & Debug Setup

To compile and simulate using **VCS**, and view waveforms using **Verdi**, use the following commands:

```bash
vcs -sverilog full_adder.v tb.v -full64 -kdb -lca -debug_access+all
./simv -verdi
```
|Flag              | Description                                                     |
|------------------|-----------------------------------------------------------------|
|-sverilog         | Enables SystemVerilog support.                                  |
|full_adder.v tb.v | Source files: main design and testbench.                        |
|-full64           | Enables 64-bit compilation for performance.                     |
|-kdb              | Enables kernel debugging (for waveform debug in Verdi).         |
|-lca              | Enables support for Licensing Control Architecture.             |
|-debug_access+all | Allows full signal visibility in waveform viewers like Verdi.   |
|./simv -verdi     | Runs the simulation and launches Verdi GUI with waveform access.|

output :


![image](https://github.com/user-attachments/assets/f3d1bad8-8439-4d7a-8008-b56dfe9a1cda)


```
--------------------------------------------------
| Time |   A   |   B   | C_in |  SUM  | C_out |
--------------------------------------------------
|    6 |    1 |    2 |  0   |     x  |   x   |
|   16 |    4 |    5 |  1   |     3  |   0   |
|   26 |    7 |    8 |  0   |    10  |   0   |
|   36 |   10 |   11 |  0   |    15  |   0   |
--------------------------------------------------

```
# 4-Bit Full Adder - Simulation Delay Explanation

## Overview

 During simulation, you may notice that the `SUM` and `C_out` outputs appear to be **delayed** by one cycle compared to input changes. 
 This document explains **why** this happens and how to interpret it correctly.

---

## 📌 Why is the `SUM` Output Delayed?

### ✅ Reason 1: Clock-Driven Output Updates

The full adder module uses a **sequential (clocked)** logic structure, meaning the outputs (`SUM` and `C_out`) are **only updated on the rising edge of the clock**:

```verilog
always @(posedge Clock) begin
    SUM   <= sum_i;
    C_out <= c_out;
end

🕒 Why is SUM Delayed?

✅ Reason 1: Clock-Driven Output Updates
The full adder uses sequential logic where outputs are only updated on the rising edge of the clock:

always @(posedge Clock) begin
    SUM   <= sum_i;
    C_out <= c_out;
end

So even if the result is computed earlier, it won't be visible at the output until the next clock edge.

✅ Reason 2: Registered Inputs
Inputs A, B, and C_in are also stored in internal registers:
always @(posedge Clock) begin
    reg1 <= A;
    reg2 <= B;
    c_in <= C_in;
end

The computation uses these registered values, which represent the inputs from one clock cycle ago.
Hence, SUM and C_out reflect previous input values.

✅ Reason 3: Observed Simulation Timing
In simulation logs, this results in output appearing one cycle after the inputs change. Example:

--------------------------------------------------
| Time |   A   |   B   | C_in |  SUM  | C_out |
--------------------------------------------------
|    6 |    1  |   2   |  0   |   x   |   x   |
|   16 |    4  |   5   |  1   |   3   |   0   |
|   26 |    7  |   8   |  0   |  10   |   0   |
|   36 |   10  |  11   |  0   |  15   |   0   |
--------------------------------------------------
Output at time 16 shows the sum of inputs applied around time 10, latched on rising edge at 15.

✅ How to Avoid or Handle Delay
🛠 Option 1: Use Combinational Logic (If Needed)
To avoid delay completely:

always @* begin
    {C_out, SUM} = A + B + C_in;
end

But this removes the clocked (synchronous) behavior.

🛠 Option 2: Adjust Testbench Timing
If keeping sequential logic, wait one clock cycle before observing outputs:

#10 A = 4; B = 5; C_in = 1;
#20 $display("Observe SUM and C_out now");  // allows time for clocked update

 ### Summary : 
-Delay is expected due to clocked, synchronous design.
-Inputs are registered, and computation reflects values from the previous clock edge.
-VCS + Verdi helps visualize and debug signal timing easily.
-Outputs (SUM, C_out) will appear one cycle after input changes.

```
## Schematic View in Synopsys Verdi

![image](https://github.com/user-attachments/assets/130ef8eb-2bd6-487c-a5f8-b2019f273c37)


![image](https://github.com/user-attachments/assets/b29c8b10-1d08-4b43-8a8f-50a49f22a59a)



## RTL Simulation Waveform  View using Synopsys Verdi 


![image](https://github.com/user-attachments/assets/e5065406-5e7b-42f3-b407-bd1dfe27a7e8)


### This is the waveform with all signals :

![image](https://github.com/user-attachments/assets/153ae31b-1ae9-4949-8a6c-17269f7b46c0)










