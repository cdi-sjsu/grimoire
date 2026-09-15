---
title: Scaling and storing data
weight: 50
---

# Scaling and storing data

Widths and repeated structures should usually come from one source of truth. Parameters, loops,
and arrays let a description scale while still producing a fixed amount of hardware.

## Configurable widths

```systemverilog
module register #(
    parameter int WIDTH = 8
) (
    input  logic             clk,
    input  logic [WIDTH-1:0] data_in,
    output logic [WIDTH-1:0] data_out
);
    always_ff @(posedge clk)
        data_out <= data_in;
endmodule
```

The default width is 8. An instance can override it by name:

```systemverilog
register #(.WIDTH(16)) wide_register (
    .clk      (clk),
    .data_in  (wide_input),
    .data_out (wide_output)
);
```

Parameters are fixed when the design is elaborated; they are not inputs that change while the
circuit runs. `localparam` creates a derived constant that instances cannot override.

## Procedural loops

A `for` loop inside `always_comb` repeats statements over a fixed range:

```systemverilog
integer i;
always_comb begin
    parity = 1'b0;
    for (i = 0; i < WIDTH; i = i + 1)
        parity = parity ^ data[i];
end
```

Synthesis unrolls this into hardware. It is not one gate reused at different times. Loop bounds
therefore need to be determinable when building the design.

## Repeating structure

A generate loop creates repeated instances or continuous structure:

```systemverilog
genvar bit_index;
generate
    for (bit_index = 0; bit_index < WIDTH; bit_index = bit_index + 1) begin : invert_each_bit
        assign output_data[bit_index] = ~input_data[bit_index];
    end
endgenerate
```

`genvar` exists only while elaborating the design. The name after the colon gives each generated
scope a stable hierarchical name for tools and testbenches.

## Packed and unpacked arrays

The position of a range changes its meaning:

```systemverilog
logic [7:0] one_byte;          // packed: one 8-bit value
logic [7:0] memory [0:255];    // unpacked: 256 elements, each 8 bits
```

Use `memory[address]` to select one element and `memory[address][3]` to select bit 3 of that
element.

## Memory read and write patterns

A common single-write-port memory uses a clocked write:

```systemverilog
always_ff @(posedge clk) begin
    if (write_enable)
        memory[write_address] <= write_data;
end

assign read_data = memory[read_address];
```

That example has asynchronous/combinational read: changing `read_address` changes `read_data`
without a clock edge. A synchronous read instead registers the result:

```systemverilog
always_ff @(posedge clk) begin
    read_data <= memory[read_address];
end
```

These styles can infer different physical memory resources. Use the one required by the design,
not whichever happens to be shorter.

`$clog2(DEPTH)` is commonly used for address width. Be careful with a depth of 1, for which a
minimum-width policy may be needed in real reusable code.
