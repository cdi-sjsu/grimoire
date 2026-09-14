---
title: Remembering state over time
weight: 40
---

# Remembering state over time

Sequential hardware remembers values. Most synchronous designs agree on one signal, the clock,
whose edges tell every register when it may capture a new value.

## Registers and clock edges

```systemverilog
always_ff @(posedge clk) begin
    count <= count + 1'b1;
end
```

`posedge clk` means the transition from low to high. At each rising edge, the expression on the
right is sampled and scheduled into `count`. Between edges, `count` retains its value.

Use nonblocking assignment (`<=`) in `always_ff`. All right-hand sides observe the old state before
any left-hand side updates, matching a bank of registers that captures simultaneously:

```systemverilog
always_ff @(posedge clk) begin
    first  <= input_value;
    second <= first;
end
```

After an edge, `first` gets the input's previous value and `second` gets `first`'s previous value.
Changing these to blocking assignments would incorrectly make statement order affect the modeled
pipeline.

## Reset styles

A synchronous reset is checked only at the active clock edge:

```systemverilog
always_ff @(posedge clk) begin
    if (reset)
        count <= '0;
    else
        count <= count + 1'b1;
end
```

An asynchronous reset can change registers without waiting for a clock edge, so it appears in the
event control too:

```systemverilog
always_ff @(posedge clk or posedge reset) begin
    if (reset)
        count <= '0;
    else
        count <= count + 1'b1;
end
```

For an active-low reset, names commonly end in `_n`, and assertion happens on `negedge`:

```systemverilog
always_ff @(posedge clk or negedge reset_n) begin
    if (!reset_n)
        count <= '0;
    else
        count <= next_count;
end
```

Follow the reset style stated by the exercise or project. These forms describe different hardware.

## Enables and next values

Leaving out an `else` in clocked logic means "retain the current value," which is valid register
behavior:

```systemverilog
always_ff @(posedge clk) begin
    if (enable)
        data_out <= data_in;
end
```

This differs from an incomplete `always_comb`, where retaining a value accidentally creates a
latch. Ask whether the signal is intentionally remembering state to decide which behavior is
correct.
