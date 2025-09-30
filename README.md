# Lab 1 — SystemVerilog: Build & Test a Simple 4-bit ALU

**Target students:** Undergraduate digital design / HDL course (first SystemVerilog lab)

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
      3'b001: Y = A - B;
      3'b010: Y = {1'b0, A & B};
      3'b011: Y = {1'b0, A | B};
      3'b100: Y = {1'b0, A ^ B};
      3'b101: Y = {1'b0, A << 1};
      3'b110: Y = {1'b0, A >> 1};
      3'b111: Y = {1'b0, A};
      default: Y = '0;
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

  alu dut(.A(A), .B(B), .op(op), .Y(Y), .zero(zero));

  initial begin
    int errors = 0;

    // Simple self-checking tests
    automatic logic [4:0] expected;

    // Test ADD
    A=3; B=2; op=3'b000; #1; expected=A+B;
    if (Y!==expected) begin $display("Mismatch ADD: got %0d expected %0d", Y, expected); errors++; end

    // Test SUB
    A=7; B=4; op=3'b001; #1; expected=A-B;
    if (Y!==expected) begin $display("Mismatch SUB: got %0d expected %0d", Y, expected); errors++; end

    // Test AND
    A=5; B=6; op=3'b010; #1; expected={1'b0,A&B};
    if (Y!==expected) begin $display("Mismatch AND: got %0d expected %0d", Y, expected); errors++; end

    // Test OR
    A=5; B=6; op=3'b011; #1; expected={1'b0,A|B};
    if (Y!==expected) begin $display("Mismatch OR: got %0d expected %0d", Y, expected); errors++; end

    // Test XOR
    A=5; B=6; op=3'b100; #1; expected={1'b0,A^B};
    if (Y!==expected) begin $display("Mismatch XOR: got %0d expected %0d", Y, expected); errors++; end

    // Test Shift Left
    A=8; op=3'b101; #1; expected={1'b0,A<<1};
    if (Y!==expected) begin $display("Mismatch SHL: got %0d expected %0d", Y, expected); errors++; end

    // Test Shift Right
    A=8; op=3'b110; #1; expected={1'b0,A>>1};
    if (Y!==expected) begin $display("Mismatch SHR: got %0d expected %0d", Y, expected); errors++; end

    // Test Pass-through
    A=9; op=3'b111; #1; expected={1'b0,A};
    if (Y!==expected) begin $display("Mismatch PASS: got %0d expected %0d", Y, expected); errors++; end

    if (errors==0) $display("TEST PASS");
    else $display("TEST FAIL: %0d errors", errors);
    $finish;
  end
endmodule
```

---



✅ That’s it — the ALU now produces a WIDTH+1 result and the testbench is simple, **self-checking**, and prints PASS/FAIL automatically!
