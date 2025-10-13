# 🧠 Lab 1 — SystemVerilog: CPU Core Components (Register File, ALU, and Memory)

---

## 🎯 Objective

In this lab, you will **design and test the main CPU hardware components** in SystemVerilog:
- **Register File**
- **ALU**
- **Data Memory**

By the end of this lab, you will have working modules that form the building blocks of a simple CPU datapath.

---

## 🧩 Part 1 — Register File

### 📘 Overview

A **Register File** stores multiple general-purpose registers that can be read and written simultaneously.

- It supports **two read ports** and **one write port**.
- The design is **synchronous write** (writes occur on the rising edge of the clock).

---

### 📐 Specification

**Parameters:**
- `NUM_REGS = 8`
- `WIDTH = 8`

**Ports:**
| Name | Direction | Width | Description |
|------|------------|--------|--------------|
| `clk` | input | 1 | Clock signal |
| `we` | input | 1 | Write enable |
| `waddr` | input | log₂(NUM_REGS) | Write address |
| `raddr1` | input | log₂(NUM_REGS) | Read address 1 |
| `raddr2` | input | log₂(NUM_REGS) | Read address 2 |
| `wdata` | input | WIDTH | Data to write |
| `rdata1` | output | WIDTH | Data read from port 1 |
| `rdata2` | output | WIDTH | Data read from port 2 |

---

### 🧱 Module (`regfile.sv`)

```systemverilog
module regfile #(
  parameter WIDTH = 8,
  parameter NUM_REGS = 8
)(
  input  logic clk,
  input  logic we,
  input  logic [$clog2(NUM_REGS)-1:0] waddr, raddr1, raddr2,
  input  logic [WIDTH-1:0] wdata,
  output logic [WIDTH-1:0] rdata1, rdata2
);

  logic [WIDTH-1:0] regs [NUM_REGS-1:0];

  // Read is asynchronous
  assign rdata1 = regs[raddr1];
 // add asynchronous for 

  // add synchronous Write 
  always_ff @(.........)
    if (...)
      regs[waddr] .... wdata;

endmodule

```

## Simple Self-Checking Testbench (`regfile_tb.sv`)

```systemverilog
module regfile_tb;
  logic clk, we;
  logic [2:0] waddr, raddr1, raddr2;
  logic [7:0] wdata, rdata1, rdata2;

  regfile dut(.clk(clk), .we(we), .waddr(waddr),
              .raddr1(raddr1), .raddr2(raddr2),
              .wdata(wdata), .rdata1(rdata1), .rdata2(rdata2));

  // Clock generation
  always #5 clk = ~clk;

  initial begin
    clk = 0; we = 0; waddr = 0; wdata = 0;

    // Write to registers
    repeat (8) begin
      @(posedge clk);
      we = 1; waddr = $random % 8; wdata = $random;
    end
    we = 0;

    // Read from registers
    @(posedge clk);
    raddr1 = 3'd2; raddr2 = 3'd5;
    #1 $display("R2=%0d, R5=%0d", rdata1, rdata2);

    $display("✅ Register File Test Completed");
    $finish;
  end
endmodule
```
