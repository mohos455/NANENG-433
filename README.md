# Lab 1 — SystemVerilog: Build & Test a Simple 4-bit ALU

---

## Overview

You will design and test a simple **4-bit Arithmetic Logic Unit (ALU)** in SystemVerilog.

---

## ALU Specification

Inputs:

* `logic [3:0] A, B`
* `logic [2:0] op`

Outputs:

* `logic [4:0] Y` (result is WIDTH+1 to hold carry/borrow)

| op (bin) | Operation      |   |
| -------- | -------------- | - |
| 000      | A + B          |   |
| 001      | A - B          |   |
| 010      | A & B          |   |
| 011      | A              | B |
| 100      | A ^ B          |   |
| 101      | A << 1         |   |
| 110      | A >> 1         |   |
| 111      | Pass-through A |   |

---

## ALU Module (`alu.sv`)

```systemverilog
module alu #(
  parameter WIDTH = 4
)(
  input  logic [WIDTH-1:0] A,
  input  logic [WIDTH-1:0] B,
  input  logic [2:0] op,
  output logic [WIDTH:0] Y,   // WIDTH+1 result
  output logic zero            // high when Y == 0
);

  always_comb begin
    Y = '0;
    case(op)
      3'b000: Y = A + B;
      ...
      ...
      ...

    endcase
    zero = (Y == '0);
  end
endmodule
```

---

## Simple Self-Checking Testbench (`alu_tb.sv`)

```systemverilog
module alu_tb;
  logic [3:0] A, B;
  logic [2:0] op;
  logic [4:0] Y;
  logic zero;

  // Testbench Signals
  int errors = 0;
  // Simple self-checking tests
  logic [4:0] expected;

  alu dut(.A(A), .B(B), .op(op), .Y(Y), .zero(zero));
  initial begin

    // Test ADD
    A=3; B=2; op=3'b000; #1; expected=A+B;
    if (Y!==expected) begin $display("Mismatch ADD: got %0d expected %0d", Y, expected); errors++; end

    // Test SUB
    A=7; B=4; op=3'b001; #1; expected=A-B;
    if (Y!==expected) begin $display("Mismatch SUB: got %0d expected %0d", Y, expected); errors++; end

    // Test AND
      ....
      ....
    // Test OR
      ....
      ....
    // Test XOR
      ....
      ....
    // Test Shift Left
     ....
     ....
    
    // Test Shift Right
     ....
     ....
    // Test Pass-through
     ....
     ....

    if (errors==0) $display("TEST PASS");
    else $display("TEST FAIL: %0d errors", errors);
  end
endmodule
```

---



✅ That’s it — the ALU now produces a WIDTH+1 result and the testbench is simple, **self-checking**, and prints PASS/FAIL automatically!
