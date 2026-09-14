---
title: Checking that hardware works
weight: 80
---

# Checking that hardware works

A testbench is SystemVerilog code that creates the design, drives its inputs, and checks its
outputs. It is simulation code, not hardware intended for synthesis.

## A small testbench

```systemverilog
module and_gate_tb;
    logic a, b;
    logic y;

    and_gate dut (
        .a (a),
        .b (b),
        .y (y)
    );

    initial begin
        a = 1'b0;
        b = 1'b0;
        #1;
        assert (y == 1'b0) else $error("0 & 0 should be 0");

        a = 1'b1;
        b = 1'b1;
        #1;
        assert (y == 1'b1) else $error("1 & 1 should be 1");

        $finish;
    end
endmodule
```

`dut` means design under test. An `initial` block starts once at simulation time zero. `#1` waits
one time unit so combinational changes can settle before the check. `$error` and `$finish` are
simulator system tasks, identified by `$`.

## Driving a clock

```systemverilog
initial clk = 1'b0;
always #5 clk = ~clk;
```

This testbench clock has a period of 10 time units. For clocked designs, drive inputs away from the
sampling edge and check registered outputs after the edge to avoid races:

```systemverilog
data_in = 8'h3C;
@(posedge clk);
#1;
assert (data_out == 8'h3C);
```

`@(posedge clk)` waits for an event rather than a fixed amount of time.

## Immediate and concurrent assertions

An immediate assertion checks when procedural execution reaches it:

```systemverilog
assert (actual === expected)
    else $fatal(1, "expected %h, got %h", expected, actual);
```

Case equality (`===`) is often useful in testbenches because an unknown output should fail against
a known expected value rather than making the comparison itself unknown.

A concurrent assertion describes behavior across clock cycles:

```systemverilog
assert property (@(posedge clk) disable iff (reset)
    request |=> grant);
```

Here `|=>` means that if `request` is true on one sampled edge, `grant` must be true on the next.
`disable iff (reset)` turns the property off while reset is active.

## Grouping a connection protocol

An interface bundles signals that travel together:

```systemverilog
interface request_if (input logic clk);
    logic request;
    logic grant;

    modport requester (input clk, output request, input grant);
    modport responder (input clk, input request, output grant);
endinterface
```

A module can accept the appropriate view:

```systemverilog
module responder (request_if.responder bus);
    always_ff @(posedge bus.clk)
        bus.grant <= bus.request;
endmodule
```

The `modport` directions are from that participant's point of view. Interfaces reduce repetitive
port lists and keep protocol signals together; they do not replace understanding which participant
drives each signal.

## Testbench habits

- Initialize every driven input, especially reset and clock.
- Check boundary cases, not only the easiest normal case.
- Include useful expected and actual values in failure messages.
- End finite tests with `$finish`; use `$fatal` when continuing would be meaningless.
- Treat warnings and unknown values as clues to investigate.
