#  SystemVerilog Verification Methodology – Theory

This document explains a **SystemVerilog-based testbench architecture of full adder ** using key verification components: `mailbox`, `packet`, `generator`, `driver`, `monitor`, `interface`, `DUT (slave)`, and `scoreboard`. These components work together to form a modular, reusable, and scalable environment to verify digital designs.

---

## Overall Flow

---

![image](https://github.com/user-attachments/assets/b7eb98e8-5481-4b69-a91c-ca2dc3984101)



## Packet (Transaction Object)

- A `packet` is a class that encapsulates transaction-level information like address, data, and control signals.
- It abstracts low-level signal details into a high-level object for easier stimulus generation and checking.
- Used as the common data format passed between generator, driver, monitor, and scoreboard.

```
  class full_adder_packet;
  rand bit [3:0] A, B;
  rand bit C_in;
  bit [3:0] SUM;
  bit C_out;

  function void print();
    $display("[PKT] A=%0d B=%0d C_in=%0d => SUM=%0d C_out=%0d", A, B, C_in, SUM, C_out);
  endfunction
endclass
```
  

---

##  Generator

- The **generator** creates randomized or directed `packet` objects.
- It feeds these transactions into a `mailbox` that the **driver** can consume.
- Promotes *constrained random testing* for thorough verification coverage.
```
  class full_adder_generator;
  mailbox #(full_adder_packet) gen2drv;

  function new(mailbox #(full_adder_packet) mbox);
    gen2drv = mbox;
  endfunction

  task generate_test_vectors();
    full_adder_packet pkt;
    repeat (10) begin
      pkt = new();
      assert(pkt.randomize());
      gen2drv.put(pkt);
    end
  endtask
endclass

  ```

```
```

---

##  Mailbox

- A SystemVerilog synchronization primitive used for inter-process communication.
- Acts as a buffer between **generator** and **driver**, or **monitor** and **scoreboard**.
- Provides thread-safe data transfer and decouples component execution.


---

##  Driver

- The **driver** reads packets from the `mailbox` and converts them into pin-level signal activity.
- Drives the DUT inputs through the **interface**.
- Mimics how a real-world master or controller would communicate with the DUT.

```
class full_adder_driver;
  virtual full_adder_if vif;
  mailbox #(full_adder_packet) gen2drv;

  function new(virtual full_adder_if vif, mailbox #(full_adder_packet) mbox);
    this.vif = vif;
    gen2drv = mbox;
  endfunction

  task drive();
    full_adder_packet pkt;
    forever begin
      gen2drv.get(pkt);
      vif.A    <= pkt.A;
      vif.B    <= pkt.B;
      vif.C_in <= pkt.C_in;
      @(posedge vif.Clock);
    end
  endtask
endclass
```

---

##  Interface

- Bundles together DUT input/output signals into a reusable block.
- Simplifies connectivity between testbench and DUT.
- May include `modports` and `clocking blocks` for structured signal access.

```
interface full_adder_if(input logic Clock);
  logic [3:0] A, B;
  logic C_in;
  logic [3:0] SUM;
  logic C_out;
endinterface

```

---

##  DUT (Design Under Test - Slave)

- The actual design module being verified and in this case its the full adder verilog code that I have written in the RTL description file in this repository.
- Receives signal-level stimulus from the **driver** via the **interface**.
- Responds according to its functional behavior.

---

##  Monitor

- The **monitor** passively observes signal activity on the interface (does not drive signals).
- Converts signal-level activity back into `packet` objects.
- Sends reconstructed packets to the **scoreboard** via another `mailbox`.
```
class full_adder_monitor;
  virtual full_adder_if vif;
  mailbox #(full_adder_packet) mon2sb;

  function new(virtual full_adder_if vif, mailbox #(full_adder_packet) mbox);
    this.vif = vif;
    mon2sb = mbox;
  endfunction

  task observe();
    full_adder_packet pkt;
    forever begin
      @(posedge vif.Clock);
      pkt = new();
      pkt.A     = vif.A;
      pkt.B     = vif.B;
      pkt.C_in  = vif.C_in;
      pkt.SUM   = vif.SUM;
      pkt.C_out = vif.C_out;
      mon2sb.put(pkt);
    end
  endtask
endclass

```

---

##  Scoreboard

- The **scoreboard** receives transactions from the **monitor**.
- Checks for correctness against expected results (from a reference model or known-good behavior).
- Reports mismatches and helps ensure DUT functionality.
```
class full_adder_scoreboard;
  mailbox #(full_adder_packet) mon2sb;

  function new(mailbox #(full_adder_packet) mbox);
    mon2sb = mbox;
  endfunction

  task compare();
    full_adder_packet pkt;
    bit [4:0] expected_sum;
    forever begin
      mon2sb.get(pkt);
      expected_sum = pkt.A + pkt.B + pkt.C_in;
      if ((pkt.SUM !== expected_sum[3:0]) || (pkt.C_out !== expected_sum[4])) begin
        $display("[FAIL] Got: SUM=%0d, C_out=%0d | Expected: SUM=%0d, C_out=%0d",
                 pkt.SUM, pkt.C_out, expected_sum[3:0], expected_sum[4]);
      end else begin
        $display("[PASS]");
        pkt.print();
      end
    end
  endtask
endclass

```

---

##  Key Advantages of This Methodology

- **Modularity**: Components are separate and reusable.
- **Randomization**: Supports constrained random stimulus for higher coverage.
- **Synchronization**: Mailboxes ensure clean communication between parallel threads.
- **Reusability**: Classes and interfaces allow testbench reuse across projects.
- **Scalability**: Easily extended for more complex protocols and DUTs.

---

##  Summary Table

| Component   | Role                                   |
|-------------|----------------------------------------|
| `packet`    | Encapsulates transaction data          |
| `generator` | Creates test transactions              |
| `mailbox`   | Transfers data between components      |
| `driver`    | Drives DUT inputs                      |
| `interface` | Connects testbench to DUT signals      |
| `DUT`       | The design being tested                |
| `monitor`   | Observes and reconstructs transactions |
| `scoreboard`| Checks DUT output correctness          |

---

##  Best Practices

- Keep stimulus generation and checking separate for cleaner debug.
- Start with directed tests, then move to constrained random testing.


---

### SVM simulation using VCS and Verdi 



