# 🚦 Assignment 1 — Traffic Light Controller (SystemVerilog)

## Overview
In this lab, you will design and simulate a **Traffic Light Controller** using **SystemVerilog**.  
The controller cycles through **Green → Yellow → Red** lights with specific timing for each state.  
You will model it as a **Finite State Machine (FSM)** with synchronous logic and time counters.

---

## Learning Objectives
By completing this lab, you will:
- Understand how to design an FSM in SystemVerilog.
- Learn to use **sequential and combinational always blocks**.
- Practice **timing control** using counters.
- Build and simulate a complete module with a **testbench**.

---

## Specifications

### System Description
The traffic light cycles through 3 states:

| State | Light Output | Duration (in clock cycles) |
|--------|---------------|----------------------------|
| GREEN  | `3'b001`      | 5 cycles                  |
| YELLOW | `3'b010`      | 2 cycles                  |
| RED    | `3'b100`      | 5 cycles                  |


### Inputs & Outputs
| Signal | Direction | Width | Description |
|---------|------------|--------|-------------|
| `clk` | input | 1 | System clock |
| `reset` | input | 1 | Active-high synchronous reset |
| `lights` | output | 3 | Light status: `{Red, Yellow, Green}` |

---

``` systemverilog
module traffic_light (
    input  logic clk,
    input  logic reset,
    output logic [2:0] lights // {Red, Yellow, Green}
);
.
.
.
.
.
.
endmodule

```
### you can use this TB for Verification

``` systemverilog
`timescale 1ns/1ps
module tb_traffic_light;

    logic clk;
    logic reset;
    logic [2:0] lights;

    // DUT instantiation
    traffic_light dut (
        .clk(clk),
        .reset(reset),
        .lights(lights)
    );

    // Clock generation
    initial clk = 0;
    always #5 clk = ~clk; // 10ns period

    // Stimulus
    initial begin
        $display("Starting Traffic Light Simulation...");
        reset = 1; #20;
        reset = 0;

        // Run simulation for several cycles
        #200;
        $stop;
    end

    // Monitor output
    initial begin
        $monitor("Time=%0t | lights=%b (R,Y,G)", $time, lights);
    end

endmodule
```


