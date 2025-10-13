# 🧠 Part 3 — SystemVerilog: Multiplexer (MUX) Design & Verification

---

## 🎯 Objective
In this lab, you will design and verify several **multiplexer (MUX)** circuits in SystemVerilog.  
Multiplexers are essential datapath elements used to select between inputs inside CPU components such as the **ALU**, **Register File**, and **Control Unit**.

By the end of this lab, you will:
- Understand how to parameterize a MUX using `generate` or `case`.
- Implement **2-to-1**, **4-to-1**, and **8-to-1** MUXes.
- Build a **generic N-input MUX** using parameters.
- Write a **self-checking testbench** for functional verification.

---

## 🧩 Part 1 — 2-to-1 MUX

### 📘 Specification

**Inputs:**
- `a`, `b` — 1-bit or multi-bit data inputs  
- `sel` — select signal  

**Output:**
- `y` — output selected by `sel`  

| `sel` | Output |
|--------|---------|
| 0 | `a` |
| 1 | `b` |

---

### 🧱 Module (`mux2.sv`)
```systemverilog
module mux2 #(
  parameter WIDTH = 8
)(
  input  logic [WIDTH-1:0] a,
  input  logic [WIDTH-1:0] b,
  input  logic sel,
  output logic [WIDTH-1:0] y
);
  assign y = (sel) ? b : a;
endmodule
```


## 🧩 Part 2 — 4-to-1 MUX

### 📘 Specification

**Inputs:**
- `a0`, `a1` , `a2`,`a3` — 1-bit or multi-bit data inputs  
- `sel[1:0]` — select signal  

**Output:**
- `y` — output selected by `sel`

### 🧱 Module (`mux4.sv`)
```systemverilog
  module mux4 #(
  parameter WIDTH = 8
)(
  input  logic [WIDTH-1:0] a0, a1, a2, a3,
  input  logic [1:0] sel,
  output logic [WIDTH-1:0] y
);
  always_comb begin
    case(sel)
      2'b00: y = a0;
      ............
      ............
      ............
      ............
      default: y = '0;
    endcase
  end
endmodule
```

## Simple Self-Checking Testbench (`mux4_tb.sv`)

```systemverilog
module mux4_tb;
  parameter WIDTH = 4;
  logic [WIDTH-1:0] a0, a1, a2, a3, y;
  logic [1:0] sel;

  mux4 #(.WIDTH(WIDTH)) dut(.a0(a0), .a1(a1), .a2(a2), .a3(a3), .sel(sel), .y(y));

  initial begin
    a0 = 4'd0; a1 = 4'd1; a2 = 4'd2; a3 = 4'd3;

    for (int i=0; i<4; i++) begin
      sel = i[1:0]; #1;
      assert(y == i) else $error("MUX4 failed for sel=%0d", i);
    end

    $display("✅ MUX4 Test Passed");
    $finish;
  end
endmodule
```
