---
title: Getting Set Up
weight: 10
---

# Getting set up

## If you've never used a terminal before

Everything below happens in a **terminal** — a plain text window where you type commands
instead of clicking things. If you've only ever used a mouse to run programs, here's the
handful of ideas you need before going further.

Your terminal always has a **current directory**, a folder it's standing in. Every command runs
relative to that folder unless you say otherwise. Four commands cover almost everything you need
day to day:

```sh
pwd             # "print working directory" - shows where you currently are
ls              # lists what's in the current folder
cd some_folder  # "change directory" - moves into some_folder
cd ..           # moves UP one folder, out of wherever you are
```

You'll also want some way to open and edit a text file — any graphical code editor (VS Code, Zed,
whatever you like) works fine, or a terminal editor like `nano` if you'd rather stay in the
terminal (`nano some_file.sv`, then Ctrl+O to save, Ctrl+X to quit).

If any of that felt unfamiliar, it's worth reading twice before moving on — the rest of this
page, and the whole course, assumes you're comfortable with `cd` and editing a file.

## Nix

svlings uses the same Nix development environment approach as everything else in this grimoire.
If you haven't set that up yet, go do that first:

- **[Development Environment](../devenv)** — install Nix, turn on flakes, and (optionally) set up
  `direnv` so you never type `nix develop` by hand again.

## Clone the course and get in

```sh
git clone https://github.com/cdi-sjsu/svlings
#op or 
# git clone git@github:cdi-sjsu/svlings svlings
# if you perfer
cd svling
nix develop
```

`nix develop` drops you into a shell with verilator, a C++ toolchain, and gtkwave already
installed and on your PATH — nothing gets installed system-wide, and none of it lingers once you
leave the folder. If you set up `direnv` per the devenv page, `direnv allow` once inside the
folder does this for you automatically from then on.

## The one command that starts everything

```sh
svlings list
```

That prints every exercise in the course, grouped by section, in the order you're meant to do
them. From here, the course itself tells you what to do next — see
**[Using the `svlings` command](./using-svlings)** for what each subcommand actually does.
