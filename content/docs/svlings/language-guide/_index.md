---
title: SystemVerilog language guide
weight: 30
bookCollapseSection: true
bookIcon: markdown
---

# SystemVerilog language guide

The exercises are where you practise. This guide is the book to keep open beside them. It explains
the syntax before asking you to use it, and it assumes SystemVerilog is your first hardware
description language.

The chapters are named after ideas, not exercise files. Read them in order the first time; later,
use them as a reference when a piece of syntax is unfamiliar.

1. **[Describing hardware and connections](./hardware-and-connections/)** introduces design blocks,
   ports, continuous assignments, and connecting blocks together.
2. **[Representing values](./values-and-types/)** covers bits, vectors, literals, four-state logic,
   signed values, and operators.
3. **[Making combinational decisions](./combinational-decisions/)** explains `always_comb`,
   blocking assignment, `if`, `case`, and accidental latches.
4. **[Remembering state over time](./state-and-time/)** introduces clocks, registers, resets, and
   nonblocking assignment.
5. **[Scaling and storing data](./scaling-and-storage/)** covers parameters, loops, generate blocks,
   arrays, and memories.
6. **[Giving data and behavior names](./named-data-and-behavior/)** covers enums, structs,
   functions, and tasks.
7. **[Controlling behavior with states](./state-machines/)** builds the standard finite-state
   machine pattern one piece at a time.
8. **[Checking that hardware works](./checking-behavior/)** covers testbenches, timing, assertions,
   and interfaces.

{{< details title="How to use this guide with svlings" >}}

Read the chapter matching the concept you are practising, type the examples rather than only
looking at them, and then return to the exercise. If an exercise still does not make sense, run
`svlings hint <name>` for a clue specific to that exercise. The guide explains the language; the
hint explains what that particular broken circuit needs.

{{< /details >}}
