# Lab 1 — SystemVerilog: Build & Test a Simple 4-bit ALU

## Learning objectives

After completing this lab you will be able to:

1. Write synthesizable combinational logic in SystemVerilog using `module`, `always_comb`, and packed vectors.
2. Create a self-checking testbench that drives the design and checks outputs automatically.
3. Simulate a design, view waveforms, and interpret results.
4. Understand how to write parameterized code and use `localparam`/`parameter` for flexible width.
5. Prepare code for basic synthesis (coding style and simple linting ideas).

---

## Lab overview (what you'll build)

You will implement a simple **4-bit Arithmetic Logic Unit (ALU)** in SystemVerilog that supports the following operations, chosen by a 3-bit `op` code:

| op (bin) | Operation                                                    |                |
| -------- | ------------------------------------------------------------ | -------------- |
| 000      | A + B (add) — produces 4-bit result and carry out            |                |
| 001      | A - B (subtract) — produces 4-bit result and borrow (/carry) |                |
| 010      | A & B (bitwise and)                                          |                |
| 011      | A                                                            | B (bitwise or) |
| 100      | A ^ B (bitwise xor)                                          |                |
| 101      | A << 1 (logical left shift)                                  |                |
| 110      | A >> 1 (logical right shift)                                 |                |
| 111      | Pass-through A (output = A)                                  |                |

Inputs: `logic [3:0] A, B; logic [2:0] op;`
Outputs: `logic [3:0] Y; logic C` (carry/borrow flag). Optionally a `zero` flag.

---

## Design: `alu.sv` (what to implement)

Implement a clean, synthesizable SystemVerilog module with the following API:

```systemverilog
module alu #(
  parameter WIDTH = 4
)(
  input  logic [WIDTH-1:0] A,
  input  logic [WIDTH-1:0] B,
  input  logic [2:0] op,
  output logic [WIDTH-1:0] Y,
  output logic C,        // carry out for add, borrow for subtract
  output logic zero      // optional: high when Y == 0
);

  // combinational implementation here

endmodule
```

Implementation hints:

* Use `always_comb` for combinational logic.
* For add/sub use `logic [WIDTH:0] tmp;` to capture carry-out (WIDTH+1 bits).
* For subtraction, compute `A - B` and interpret MSB as borrow (or define C = ~borrow depending on your chosen convention; state your convention in comments).
* Drive `zero = (Y == '0);`.
* Keep code short, readable, and commented.

---

## Testbench: `alu_tb.sv` (self-checking)

Create a self-checking testbench that:

1. Instantiates the ALU.
2. Iterates through a useful subset (or full set) of inputs: all A and B from `0..15` and all `op` codes (3 bits) — this is 16*16*8 = 2048 vectors (fine for simulation).
3. Computes the *expected* result in the testbench (use SystemVerilog expressions) and compares to the DUT outputs.
4. Prints a helpful message for mismatches and increments an `error_count`.
5. At end of simulation, prints `PASS` if `error_count==0` or `FAIL` otherwise, and exit with an appropriate status.

Testbench tips:

* Use `$display` for short messages and `$fatal` or `$finish` for ending.
* Use `foreach` or nested `for` loops to generate stimulus.
* Use `#1` or `#0` carefully; prefer zero-delay stimulus and let `always_comb` settle; you can run a timestep (`#1ns`) between tests.

Minimal skeleton:

```systemverilog
module alu_tb;
  logic [3:0] A, B;
  logic [2:0] op;
  logic [3:0] Y;
  logic C;

  alu dut(.A(A), .B(B), .op(op), .Y(Y), .C(C));

  initial begin
    $dumpfile("alu.vcd");
    $dumpvars(0, alu_tb);

    // Simple tests
    A = 4'd3; B = 4'd2; op = 3'b000; #5; // Add
    ...
    ...
    ...
    ...

    $display("Simulation finished");
  end
endmodule
```


Good luck — have fun! If you want, I can also generate a ready-to-run `alu.sv` and `alu_tb.sv` starter kit for the class. Would you like that?
