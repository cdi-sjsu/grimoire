---
title: Giving data and behavior names
weight: 60
---

# Giving data and behavior names

Good names remove bookkeeping from your head. SystemVerilog can name meaningful values, bundle
related signals, and package repeated calculations.

## Enumerated values

An enum gives names to the possible values of a type:

```systemverilog
typedef enum logic [1:0] {
    IDLE,
    LOAD,
    RUN,
    DONE
} state_t;

state_t state, next_state;
```

Now code can say `state == RUN` instead of comparing against a magic number. The base type
`logic [1:0]` makes the representation width explicit. There must be enough bit patterns for all
members.

## Bundling related fields

A packed struct acts like one vector whose slices have names:

```systemverilog
typedef struct packed {
    logic       valid;
    logic [2:0] opcode;
    logic [7:0] payload;
} command_t;

command_t command;
assign should_run = command.valid && (command.opcode == 3'd2);
```

Because it is `packed`, `command` can be assigned, passed through a port, or reset as one value.
Fields are accessed with a dot.

## Functions

A function returns one value and should execute without consuming simulation time. It is a good
fit for reusable combinational calculations:

```systemverilog
function automatic logic is_even(input logic [7:0] value);
    return ~value[0];
endfunction

assign even = is_even(data);
```

For a wider result, put the packed range after the return type:

```systemverilog
function automatic logic [7:0] increment(input logic [7:0] value);
    return value + 8'd1;
endfunction
```

`automatic` gives each call its own local storage, which is the least surprising default for
reusable functions.

## Tasks

Tasks can have multiple outputs and, in testbenches, may contain timing controls:

```systemverilog
task automatic drive_and_check(input logic [7:0] value);
    data_in = value;
    #1;
    assert (data_out == value);
endtask
```

That task is testbench code because `#1` advances simulation time. Keep synthesizable design logic
free of testbench delays. Use a function when a pure expression is enough; use a task when a
procedure needs several inputs/outputs or testbench timing.

## Scope

Declare a function or task inside the module that uses it unless several modules genuinely share
it. Types and utilities shared across a project can live in a `package`, but local declarations
keep beginner designs easier to trace.
