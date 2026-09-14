---
title: What This Is, and Why
weight: 5
---

# What this is, and why

## The goal

Most SystemVerilog material out there falls into one of two piles: a dense reference manual that
assumes you already think like an engineer, or a semester-long university course you can't just
casually start on a Tuesday night. svlings tries to be neither. The goal is small steps and
instant feedback — you fix one tiny circuit, a tool tells you right away whether you got it
right, and you move to the next one. Nothing here assumes you've written a line of code before,
or wired up a circuit before.

It's built the way it is on purpose, borrowing from a few places that already do parts of this
well:

- **[Rustlings](https://github.com/rust-lang/rustlings)** — the core loop: one broken thing per
  exercise, fixed by hand, checked instantly, repeated until it clicks.
- **[HDLBits](https://hdlbits.01xz.net/)** — one concept per problem, never several ideas
  tangled together in the same exercise.
- **[nand2tetris](https://www.nand2tetris.org/)** — build it yourself, from the ground up,
  instead of being handed a finished black box and told to trust it.

## What's actually in it

This is **Phase 1**: the SystemVerilog language itself, thoroughly. Modules, every operator
you'll use day to day, combinational and sequential logic, `case`/`casez`, parameters and
generate blocks, arrays and memories, enums and structs, functions and tasks, finite state
machines, writing your own testbenches and assertions, and interfaces. By the end of it you can
read real, professionally written RTL and start writing your own small designs from scratch —
not a toy subset of the language, the actual thing.

It deliberately stops there. Everything past the language itself — class-based testbenches,
randomization and constrained-random stimulus, functional coverage, UVM, calling out to C through
the DPI — is real, valuable, and **Phase 2**, once you're standing on solid ground here. Trying
to teach both at once is exactly the kind of tangle HDLBits and nand2tetris avoid, and svlings is
trying to avoid it too.

## The contents, at a glance

| # | Section |
|---|---------|
| 00 | Welcome — getting the edit → save → check loop working |
| 01 | Modules and ports |
| 02 | Logic gates and vectors |
| 03 | Data types |
| 04 | Operators |
| 05 | Combinational logic (`always_comb`) |
| 06 | Sequential logic (`always_ff`, clocks, reset) |
| 07 | Conditionals and `case` |
| 08 | Parameters, loops, and `generate` |
| 09 | Arrays and memories |
| 10 | Enums and structs |
| 11 | Functions and tasks |
| 12 | Finite state machines |
| 13 | Testbenches and verification |
| 14 | Interfaces, and what's next |

That's the whole shape of it. See **[What's in the course](./course-map)** for a paragraph on
each one, or just run `svlings list` once you're set up and let the course itself walk you
through it in order.
