---
title: What's in the course
weight: 40
---

# What's in the course

Sections are numbered and meant to be done in order — each one leans on ideas from the ones
before it. This is Phase 1: the SystemVerilog language itself, thoroughly. It deliberately stops
short of the parts that are really about verification *methodology* rather than the language —
classes, randomization, coverage, UVM. Those are real and worth learning, and they're their own
course once you're standing on solid ground here.

Read a section's own `README.md` in the repository before touching its exercises — this page is
just a preview so you know roughly what's coming, not a replacement for that.

### 00 — Welcome

Not really about SystemVerilog yet. This is where you get the edit → save → check loop working
for the first time, so every section after this one is just "apply that loop to a new idea."

### 01 — Modules and ports

The basic building block of any design: a labeled box with inputs and outputs, and nothing else
visible from outside it. Also covers wiring one module up as a piece inside a bigger one —
instantiation.

### 02 — Logic gates and vectors

AND, OR, NOT, XOR — the whole handful of gates everything else is built from — plus **vectors**
(grouping several wires into one number) and the literal syntax you'll see everywhere,
like `8'hFF`.

### 03 — Data types

`logic` and why it exists, what the four possible values of a wire actually mean (0, 1, unknown,
and disconnected), and signed vs. unsigned numbers.

### 04 — Operators

Arithmetic and comparison you already know from anywhere else, plus the operators that are
specific to hardware description: bitwise vs. logical (a genuinely common beginner mix-up),
reduction operators, and shifts.

### 05 — Combinational logic

`always_comb`, for when a single `assign` line isn't enough. Comes with the most famous beginner
trap in the whole language: the accidentally inferred **latch**, and why the tools yell about it.

### 06 — Sequential logic

`always_ff`, clocks, and registers — the first real *memory* in this course. Also nonblocking
assignment (`<=`), and why it's different from the `=` you just got comfortable with.

### 07 — Conditionals and `case`

`case` and `casez`, for when an `if`/`else` chain gets too long to read comfortably. `casez`'s
don't-care bits are how you build a **priority encoder**, a genuinely useful building block.

### 08 — Parameters, loops, and generate

`parameter` (modules with a knob on them), a procedural `for` loop inside a design (fully
unrolled at build time), and `generate for` — stamping out repeated hardware structure, not just
repeated statements.

### 09 — Arrays and memories

Unpacked arrays, and the pattern behind every real memory you'll instantiate: an array, written
synchronously, read either combinationally or synchronously.

### 10 — Enums and structs

Naming your states instead of tracking magic numbers in your head, and bundling related signals
together instead of listing them all out individually.

### 11 — Functions and tasks

Pulling reusable combinational logic out into its own named thing, callable from more than one
place.

### 12 — Finite state machines

The capstone: everything above comes together into the two-block pattern (a register plus
combinational next-state logic) that essentially every real FSM in the world follows, no matter
how complicated.

### 13 — Testbenches and verification

Flips the usual direction — you write the checks yourself this time — plus **assertions**:
stating an invariant right next to the logic it protects, so a violation gets caught the instant
it happens instead of several stages downstream.

### 14 — Interfaces, and what's next

Bundling a whole group of signals that always travel together into one thing you pass around,
instead of wiring each one individually. Also where the course points you toward Phase 2 —
class-based testbenches, randomization, coverage, UVM — once you're ready for it.
