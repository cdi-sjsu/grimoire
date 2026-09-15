---
title: Using the svlings command
weight: 20
---

# Using the `svlings` command

`svlings` is a small command line tool that ships with the course. It's the thing that actually
compiles your circuit with verilator, runs a check against it, and turns the result into a plain
"yes that works" or "no, here's why." Every exercise, without exception, is checked this exact
same way.

## `svlings list`

Shows every exercise, grouped by section, in course order. No checking happens here — it's just
a table of contents, so it's instant.

```sh
svlings list
```

## `svlings verify`

The command you'll run the most. It checks exercises **in order**, starting from the first one,
and stops at the first one that isn't done yet — printing what's wrong and what file to open.
Once that one passes, run it again and it moves on to the next.

```sh
svlings verify
```

Add `--all` to check every exercise without stopping early, useful once you think you're done
with the whole course and want one final summary.

```sh
svlings verify --all
```

Every run rebuilds with verilator from scratch, on purpose (a cached, stale binary reporting an
old result would be far more confusing than a few extra seconds). Day to day this is fast, since
`verify` only builds up through wherever you currently are — but a full `--all` run, or a plain
`verify` once you've finished the whole course, rebuilds all 28 exercises one after another and
takes noticeably longer. That's expected; reach for `svlings run <name>` below for quick feedback
on one exercise while you're actively working on it.

## `svlings run <name>`

Builds and runs one specific exercise, verbosely — every line the testbench printed, not just a
pass/fail summary. Good for when `verify` tells you something's wrong and you want to see the
whole story.

```sh
svlings run half_adder
```

You don't need the exercise's full path or section number — just enough of its name to be
unambiguous. If it isn't unambiguous, `svlings` lists the matches instead of guessing.

## `svlings watch`

Runs `verify` once, then sits there watching the exercise files for changes, rerunning
automatically every time you save. Leave this running in a spare terminal while you edit in
another window or your editor, and you never have to remember to switch back and rerun anything
yourself.

```sh
svlings watch
```

Ctrl-C stops it.

## `svlings hint <name>`

Prints a hint for one exercise — usually enough to get unstuck without just handing you the
answer.

```sh
svlings hint half_adder
```

## `svlings solution <name>`

Prints the full, correct answer for one exercise. Nobody's grading you — looking at this and
understanding *why* it works is a completely legitimate way to learn.

```sh
svlings solution half_adder
```

## `svlings reset <name>`

Throws away whatever you've changed in one exercise's file and restores it to its original,
unsolved starting point (using git — the course tracks its own starting state). Handy if you've
made a mess of a file and would rather start clean than untangle it.

```sh
svlings reset half_adder
```
