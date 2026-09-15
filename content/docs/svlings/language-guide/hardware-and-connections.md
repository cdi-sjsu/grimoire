---
title: Describing hardware and connections
weight: 10
---

# Describing hardware and connections

SystemVerilog describes hardware that exists all at once. A line such as `assign y = a & b;` does
not run and finish like a line in a software program. It describes an AND gate whose output keeps
responding whenever either input changes.

## A design block

A `module` gives a piece of hardware a boundary. Its **ports** are the signals visible at that
boundary.

```systemverilog
module and_gate (
    input  logic a,
    input  logic b,
    output logic y
);
    assign y = a & b;
endmodule
```

Read this as: create a piece of hardware named `and_gate`, with two one-bit inputs and one one-bit
output. Everything between `module` and `endmodule` describes what is inside it.

Syntax worth noticing:

- Port declarations are separated by commas; the port list ends with `);`.
- Statements end with a semicolon.
- `input` and `output` describe signal direction at the boundary.
- `logic` is the usual signal type in this course.
- `//` starts a one-line comment; `/* ... */` surrounds a block comment.

## Connections that are always active

Use `assign` for a direct combinational connection:

```systemverilog
assign inverted = ~source;
assign both_high = left & right;
assign selected = choose_right ? right : left;
```

The expression on the right is continuously evaluated. The signal on the left must not also be
assigned somewhere else; real hardware cannot have two unrelated circuits fighting to drive one
wire.

## Putting one block inside another

An **instance** creates hardware described by another module.

```systemverilog
module alarm (
    input  logic door_open,
    input  logic enabled,
    output logic sound
);
    and_gate alarm_gate (
        .a (door_open),
        .b (enabled),
        .y (sound)
    );
endmodule
```

`and_gate` is the module type and `alarm_gate` is this instance's unique name. A connection such
as `.a(door_open)` means "connect the instance's port `a` to this module's signal `door_open`."
Named connections are easier to review than relying on port order.

Internal connections are declared like ports, but without a direction:

```systemverilog
logic first_result;
```

## Common mistakes

- Writing `module thing;` when the exercise expects ports inside parentheses.
- Forgetting the instance name between the module type and its port list.
- Reversing a named connection: the name after the dot belongs to the child module.
- Assigning an input from inside its module. Inputs are driven by the surrounding hardware.
- Thinking file order controls execution. Hardware connections operate concurrently.
