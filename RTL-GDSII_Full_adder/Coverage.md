#  Understanding Coverage in Design Verification and implementing Functional and code coverage for 4 bit full adder.

##  What is Coverage?

Coverage is a **generic term used to measure the progress of completing design verification**. Think of it like painting a canvas—your simulations try to touch every corner of your design space. The goal is to **ensure all legal combinations and scenarios** are tested in the design.

**Coverage tools** collect data during simulation and generate a **coverage report** post-simulation. This report helps identify **coverage holes**, prompting engineers to:

- Modify existing testbenches
- Add new test cases
- Improve constraints or stimuli

This process is **iterative**—you repeat until the **desired coverage level** is achieved.

---

##  Types of Coverage

1. **Functional Coverage**
   - Focuses on **what the design is supposed to do**.
   - Defined by the user using SystemVerilog constructs.
   - Checks if all **scenarios, corner cases, and features** have been tested.

2. **Code Coverage**
   - Measures how much of the **RTL code** has been exercised by the simulation.
   - Subtypes:
     - **Line Coverage**
     - **Statement Coverage**
     - **Block Coverage**
     - **Branch Coverage**
     - **Toggle Coverage**
     - etc.

---

##  Why Do We Need Verification?

Once an RTL is written, it contains:
- Many **conditions**
- Various **expressions and assignments**
- **Toggling functions**
- Multiple **always blocks**

All of these must be **verified** to check whether the design behaves as expected.

###  Example:

If we design a **4-bit full adder**, it should:
- Take two 4-bit inputs `A` and `B`
- Output a 4-bit `Sum` and a `Carry`

Now consider an **ALU with 20 operations**—each operation might have at least 5 unique test conditions. The number of test vector combinations grows rapidly, and it becomes impossible to apply them **manually**.

We may require **1000+ stimuli**, which must be:
- **Randomized**
- **Constrained** to valid scenarios

Hence, **automation with verification tools** and **random stimulus generation** is a must!

---

##  Importance of Coverage

Let’s assume a circuit with **40 inputs**. If a test only verifies 20 inputs, the **code coverage** would be 20/40 = 0.5 = **50%**. That’s only **half-verified**.

Take an example of a **smartphone (e.g., iPhone)**—they go through **intense verification** and testing to ensure stability, which is why they are expensive and reliable.

 A good rule in industry is to **target 90% or higher coverage** before **verification signoff**. The more coverage, the higher the **confidence in design stability**.

---

##  Code Quality & Optimization

Redundant or inefficient RTL:
- Increases **area**
- Consumes more **power**
- Affects **timing**

A **good RTL design** is:
- **Concise**
- **Precise**
- Optimized for **Area, Power, and Timing (APT)**

Verification ensures this **optimized logic** still fulfills the design intent.

---

##  Code Coverage Types Explained

### 1. Line Coverage

Line coverage checks if each **line of code** has been executed during simulation.

 **Example:**

```verilog
always @(posedge clk) begin
    a = b + c;  // Line 1
    d = a + 1;  // Line 2
end
```
2. Statement Coverage
Statement coverage checks if each complete statement has been executed. It's similar to line coverage but considers statements, even if they span multiple lines.

 Example:

```
if (x > y)
    z = x;  // Statement 1
else
    z = y;  // Statement 2
```
Both paths (if and else) need to be taken during simulation to get 100% statement coverage.

3. Block Coverage
Block coverage checks whether entire blocks of code (e.g., begin-end blocks, case blocks) are executed.

 Example:

```
always @(posedge clk) begin
    case (op)
        2'b00: result = a + b;
        2'b01: result = a - b;
        2'b10: result = a & b;
        2'b11: result = a | b;
    endcase
end
```
If only 2 out of 4 operations are tested, then block coverage = 50%.

 Summary
Coverage is a crucial metric in verification.

Helps in identifying untested parts of the RTL.

Reduces the time-to-market risk by improving stability.

You need to aim for high functional + code coverage to ensure a robust, optimized, and well-verified design.

### Code for funtional and code coverage : 
```
module full_adder (
    input wire [3:0] A,        // 4-bit input A
    input wire [3:0] B,        // 4-bit input B
    input wire Clock,          // Clock signal
    input wire C_in,           // Carry input
    output reg [3:0] SUM,      // 4-bit output sum
    output reg C_out           // Carry output
);

    // Internal signals
    reg [3:0] reg1, reg2, sum_i;
    reg c_in, c_out;

    // Separate signals for coverage
    wire [3:0] coverage_SUM_primary;
    wire [3:0] coverage_SUM_secondary;
    wire coverage_C_out_primary;
    wire coverage_C_out_secondary;

    assign coverage_SUM_primary = SUM;
    assign coverage_SUM_secondary = SUM;
    assign coverage_C_out_primary = C_out;
    assign coverage_C_out_secondary = C_out;

    // Register input values on positive clock edge
    // CODE COVERAGE: this always block will be checked for block and line coverage
    always @(posedge Clock) begin
        reg1 <= A;
        reg2 <= B;
        c_in <= C_in;
    end

    // Register outputs on positive clock edge
    // CODE COVERAGE: ensures outputs get assigned
    always @(posedge Clock) begin
        SUM <= sum_i;
        C_out <= c_out;
    end

    // Combinational block for addition
    // CODE COVERAGE: execution of this always block will trigger statement coverage
    always @* begin
        {c_out, sum_i} = reg1 + reg2 + c_in;
    end

    // Functional Coverage Group – Primary
    covergroup cg_full_adder @(posedge Clock);
        coverpoint A {
            bins A_values[] = {4'b0000, 4'b1111};
        }

        coverpoint B {
            bins B_values[] = {4'b0000, 4'b1111};
        }

        coverpoint C_in {
            bins C_in_values = {1'b0, 1'b1};
        }

        coverpoint coverage_SUM_primary {
            bins SUM_values[] = {4'b0000, 4'b1111};
        }

        coverpoint coverage_C_out_primary {
            bins C_out_values = {1'b0, 1'b1};
        }
    endgroup

    cg_full_adder cg_inst = new();

    // Functional Coverage – Cross Coverage between SUM and C_out
    covergroup cg_sum_carry @(posedge Clock);
        cross coverage_SUM_secondary, coverage_C_out_secondary;
    endgroup

    cg_sum_carry cg_sum_carry_inst = new();

endmodule

```
Testbench:

```
`timescale 1ns / 1ps

module testbench;

    // Inputs
    reg [3:0] A, B;
    reg C_in, Clock;

    // Outputs
    wire [3:0] SUM;
    wire C_out;

    // Instantiate the full_adder module
    full_adder uut (
        .A(A),
        .B(B),
        .C_in(C_in),
        .Clock(Clock),
        .SUM(SUM),
        .C_out(C_out)
    );

    // Clock generation
    initial begin
        Clock = 0;
        forever #5 Clock = ~Clock;  // 10ns period
    end

    // Test vector generation
    initial begin
        // Initialize inputs
        A = 0;
        B = 0;
        C_in = 0;

        // Wait for global reset
        #10;

        // Apply a few interesting test vectors to hit both functional and code coverage
        apply_input(4'b0000, 4'b0000, 1'b0); // Zero case
        apply_input(4'b1111, 4'b0001, 1'b0); // Max + 1
        apply_input(4'b1010, 4'b0101, 1'b1); // Carry in
        apply_input(4'b1111, 4'b1111, 1'b1); // Overflow case
        apply_input(4'b0001, 4'b0001, 1'b0); // Simple case
        apply_input(4'b0100, 4'b0011, 1'b1); // Mid-range with carry
        apply_input(4'b0110, 4'b1001, 1'b0); // More carry-out possibility
        apply_input(4'b0011, 4'b1100, 1'b1); // Random pattern

        // Run for a bit
        #100;
        $display("Simulation done.");
        $finish;
    end

    // Task to apply stimulus
    task apply_input(input [3:0] a, input [3:0] b, input c);
        begin
            @(posedge Clock);
            A = a;
            B = b;
            C_in = c;
        end
    endtask

endmodule
```
# Commands for execution : 
```
vcs -sverilog full_adder.sv tb.sv -full64 -kdb -lca -debug_access+all -cm line+tgl+cond+fsm
```

-sverilog: Enables SystemVerilog support.

-full_adder.sv tb.sv: Compiles both your design and testbench.

-full64: Targets 64-bit compilation (good for performance).

-kdb: Enables kernel debug database (needed for Verdi debug).

-lca: Low cost access license for Verdi (if your setup requires it).

-debug_access+all: Enables full debug visibility in Verdi.

-cm line+tgl+cond+fsm: Enables Code Coverage:

-line: Line coverage.

-tgl: Toggle coverage.

-cond: Condition coverage (for IFs, CASEs).

-fsm: FSM coverage (for state machines, if any).

```
./simv
```
Runs the compiled simulation binary (simv).

Coverage data gets saved in the default simv.vdb directory.
```
verdi -cov -covdir simv.vdb/
```
Launches Verdi Coverage GUI to explore:

Code coverage.

Functional coverage (from your covergroups).

Holes in coverage (highlighted for test improvement).

📊 What Coverage You Are Collecting

|Type	        |                Details                                                |
|-------------|-----------------------------------------------------------------------|
|Line	        |Checks whether each line in the code was executed.                     | 
|Toggle	      |Checks whether each bit of each register/wire toggled from 0→1 and 1→0.|
|Condition	  |Checks all boolean sub-expressions of IF/CASE/TERNARY conditions.      |
|FSM	        |If your design had FSMs, this checks all states and transitions.       |  
|Functional	  |From covergroup inside full_adder.sv:                                  | 
|Input values |(A, B, C_in)                                                           |
|Output       |(SUM, C_out)                                                           |

Cross-coverage between SUM and C_out

### What to Do After Running Verdi ?
In the Verdi GUI:

Use Coverage → Coverage Summary to see % values.

Explore Code Coverage View (highlighted code lines).

Open Functional Coverage View for your covergroup points.

Look for:

Unhit bins (in red or yellow): Modify or add testcases in tb.sv.

Missed toggle paths: Some signals not toggling? Try new input combinations.

Low FSM coverage: If applicable, add sequences to exercise transitions.

### Coverege analysis by vcs and verdi 


![image](https://github.com/user-attachments/assets/1a64a7a5-5f1c-462f-9d31-a11c5f716b10)

![Screenshot 2025-04-19 135130](https://github.com/user-attachments/assets/93fb7872-b919-437b-8c72-76db70b6be67)


