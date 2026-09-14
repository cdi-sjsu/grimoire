---
title: Making combinational decisions
weight: 30
---

# Making combinational decisions

Combinational hardware has no memory: its outputs are determined completely by its current inputs.
Use `assign` for a compact expression and `always_comb` when several statements make the intent
clearer.

## Procedural combinational syntax

```systemverilog
always_comb begin
    result = a;
    if (select_b) begin
        result = b;
    end
end
```

Statements inside an `always_comb` block are procedural, so use blocking assignment (`=`). The
block is still hardware, not a routine called by another routine. Tools infer a multiplexer from
the example.

`begin` and `end` group multiple statements. They are optional around one statement, but using
them consistently prevents errors when another statement is added later.

## Complete decisions prevent latches

Every value assigned by combinational logic needs a value on every possible path. This is broken:

```systemverilog
always_comb begin
    if (enable)
        y = data;
end
```

What should `y` be when `enable` is 0? Asking it to keep its previous value requires memory, so a
latch is inferred. Usually the fix is a default assignment before the decision:

```systemverilog
always_comb begin
    y = '0;
    if (enable)
        y = data;
end
```

## Selecting among choices

Use `if` when conditions naturally express priority. The first true branch wins.

```systemverilog
if (urgent)
    choice = 2'd2;
else if (normal)
    choice = 2'd1;
else
    choice = 2'd0;
```

Use `case` when one expression selects among distinct values:

```systemverilog
always_comb begin
    output_value = '0;
    case (operation)
        2'b00: output_value = a + b;
        2'b01: output_value = a - b;
        2'b10: output_value = a & b;
        default: output_value = '0;
    endcase
end
```

`default` handles values not listed, including unknown inputs. `casez` treats `?` and `z` bits in
case items as don't-care positions, which is useful for priority patterns:

```systemverilog
casez (requests)
    4'b1???: winner = 2'd3;
    4'b01??: winner = 2'd2;
    4'b001?: winner = 2'd1;
    default: winner = 2'd0;
endcase
```

Order matters in that `casez`: the first matching pattern has priority. Avoid using `casex`; it
can allow real unknown-value bugs to match silently.

## Rules of thumb

- Give outputs defaults at the top of `always_comb`.
- Use `=` in combinational procedural blocks.
- Assign a signal from one place only.
- Do not add a signal sensitivity list to `always_comb`; the language derives it for you.
