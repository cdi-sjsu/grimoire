---
title: Representing values
weight: 20
---

# Representing values

Hardware has a fixed number of wires. SystemVerilog therefore makes width and bit-level operations
part of ordinary syntax.

## Bits and vectors

`logic flag;` declares one bit. A range declares a packed vector:

```systemverilog
logic [7:0] byte_value;   // 8 bits: 7 down to 0
logic [15:0] address;     // 16 bits
```

Select one bit with an index and a consecutive group with a part-select:

```systemverilog
assign low_bit  = byte_value[0];
assign high_half = byte_value[7:4];
```

Join values with concatenation, or repeat a value with replication:

```systemverilog
assign byte_value = {high_half, low_half};
assign mask = {8{enable}};  // eight copies of enable
```

## Number literals

A sized literal has the form `width'basevalue`:

```systemverilog
8'b1010_0110  // 8-bit binary; underscores are visual separators
8'hA6         // the same value in hexadecimal
8'd166        // the same value in decimal
6'o45         // 6-bit octal
```

Prefer sized literals in RTL. They make the intended hardware width visible and avoid surprising
truncation or extension. `'0` fills the destination with zeroes and `'1` fills it with ones,
whatever its width:

```systemverilog
assign byte_value = '0;
```

## Four-state logic

Each `logic` bit can be `0`, `1`, `x` (unknown), or `z` (high impedance/disconnected). `x` often
means a signal was never initialized or two assumptions conflict. Do not hide it without finding
the cause. `z` is mainly used for shared or external buses, not ordinary internal logic.

`==` can produce unknown when an operand contains `x` or `z`. `===` compares all four states and
always produces 0 or 1; it is mostly useful in testbenches.

## Signed and unsigned values

Packed values are unsigned unless declared `signed`:

```systemverilog
logic signed [7:0] temperature;
logic        [7:0] count;
```

Width and signedness affect comparisons, shifts, and arithmetic. Convert deliberately with
`$signed(value)` or `$unsigned(value)` when values of different signedness meet.

## Operator families

```systemverilog
a + b   a - b   a * b       // arithmetic
a == b  a != b  a < b       // comparison
a & b   a | b   a ^ b  ~a   // bitwise: one operation per bit
a && b  a || b  !a          // logical: treats each whole value as true/false
&a      |a      ^a           // reduction: many bits become one bit
a << 2  a >> 2  a >>> 2     // shifts; >>> preserves a signed left operand's sign
test ? when_true : when_false
```

Bitwise and logical operators are not interchangeable. For `4'b0101 & 4'b0011`, the result is
`4'b0001`; logical `&&` only asks whether each entire operand is nonzero and returns one bit.

SystemVerilog precedence is easy to misremember. Parenthesize mixed operations to make intent
obvious:

```systemverilog
assign y = valid && ((a & mask) == expected);
```

## Width mistakes

Assigning a wider value to a narrower destination discards bits. Assigning a narrower value to a
wider destination extends it. Treat width warnings as design feedback, not noise, and slice or
extend explicitly when that is truly what you intend.
