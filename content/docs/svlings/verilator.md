---
title: Talking to verilator directly
weight: 30
---

# Talking to verilator directly

`svlings` is a thin wrapper around one tool: [Verilator](https://www.veripool.org/verilator/), a
free, extremely fast, open-source SystemVerilog simulator. Everything `svlings` does, you can do
yourself directly — and once you're working on something outside the course, verilator (or a
tool very like it) will still be there. This page is a reference for using it by hand, once
you're ready for more control than the wrapper gives you.

All of this assumes you're inside the course's `nix develop` shell already.

## Just check your syntax (fast, no build)

```sh
verilator --lint-only -Wall my_design.sv
```

This parses and elaborates your design and reports any errors or warnings, without spending time
generating and compiling C++. It's the fastest way to get feedback while you're still mid-edit.

## Build and run a simulation

```sh
verilator --binary -sv --timing -Wall \
    --top-module tb \
    -Mdir obj_dir -o sim \
    my_design.sv my_design_test.sv

./obj_dir/sim
```

What these flags mean:

- `--binary` — build a complete, runnable simulation executable.
- `-sv` — turn on SystemVerilog syntax (verilator defaults to plain Verilog otherwise).
- `--timing` — enable support for delays (`#1`) and event control (`@(posedge clk)`) in
  behavioral code, which every testbench needs.
- `-Wall` — turn on the full warning set, including ones (`LATCH`, `UNDRIVEN`) that catch real,
  easy-to-make design bugs. By default verilator treats these as **fatal** — that's a feature,
  not a nuisance, while you're learning to read what the tool is telling you.
- `--top-module tb` — which module is the top of the design, the one nothing else instantiates.
- `-Mdir obj_dir -o sim` — where to put the generated files, and what to name the result.

## Looking at waveforms

Sometimes `$display` output isn't enough and you want to see every signal's value over time,
visually. Verilator can record a **waveform** (a trace of every signal over time), which you can
then open in a viewer:

```sh
verilator --binary -sv --timing -Wall --trace \
    --top-module tb -Mdir obj_dir -o sim \
    my_design.sv my_design_test.sv

./obj_dir/sim          # writes out a file, usually dump.vcd
gtkwave dump.vcd
```

`gtkwave` opens a window where you drag signals from the left-hand tree into the main view and
see them drawn as a timeline. Recording a trace requires your testbench to actually ask for one —
a `$dumpfile("dump.vcd"); $dumpvars;` near the start of your `initial` block is enough.

## A note on warnings-as-errors

If you want verilator to warn you about something but keep building anyway instead of stopping,
add `--Wno-fatal`:

```sh
verilator --binary -sv --timing -Wall --Wno-fatal --top-module tb ...
```

The course deliberately doesn't do this by default — the whole point is to make you stop and
read what the tool is telling you, the first few dozen times, until it stops feeling like a wall
of text and starts feeling like useful information.

## Other tools worth knowing the name of

- **yosys** — open-source synthesis. Where verilator simulates your design (tells you what it
  *does*), yosys turns it into a gate-level netlist (tells you what it would *become* as real
  hardware).
- **gtkwave** — covered above, the standard free waveform viewer.
- **surfer** — a newer, browser-based alternative to gtkwave.
