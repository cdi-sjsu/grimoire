---
title: Using .envrc
weight: 40
---

# Using .envrc

You have nix now. You could type `nix develop` every time you open a terminal in a project, wait for it to think, then remember to do it again in the second tab. Nobody keeps that up for long.

`.envrc` is a one line file at the root of a repo that says "this folder has a dev environment". [direnv](https://direnv.net/) reads it, loads the environment when you `cd` in, and unloads it when you `cd` out. The tools exist inside the project and nowhere else.

Most of our repos already have one, and it looks like this:

```sh {filename=".envrc"}
use flake
```

That is the whole file. `use flake` means load the default devShell out of the `flake.nix` sitting next to me.

## What it looks like when it works

```console
$ cd ~/Source/Cdi/grimoire
direnv: loading ~/Source/Cdi/grimoire/.envrc
direnv: using flake
direnv: export +AR +AS +CC +CXX +HOST_PATH +IN_NIX_SHELL ... ~PATH

$ hugo version
hugo v0.158.0+extended

$ cd ..
direnv: unloading

$ hugo version
zsh: command not found: hugo
```

hugo was never installed. It was on your `PATH` for exactly as long as you were standing in that folder, which is the entire idea.

## The first thing it will say to you

```console
direnv: error /home/you/project/.envrc is blocked. Run `direnv allow` to approve its content
```

Not a bug. An `.envrc` is a shell script, and running a script that showed up over the network without reading it first is how people get owned. direnv will not touch it until you say so:

```sh
cat .envrc        # look at it, it should be two words
direnv allow
```

The yes is recorded for that exact file, at that exact path. Change a byte and it asks again. Clone the same repo somewhere else and it asks again. So every fresh clone needs one `direnv allow`, that is the habit.

## Writing one

Most of the time you never write an `.envrc`, the repo came with it. When you are starting something new, this is the vocabulary worth knowing.

```sh {filename=".envrc"}
use flake
```

Pulling a different shell out of the flake, for projects that define more than one:

```sh {filename=".envrc"}
use flake .#embedded
```

Project variables, which is nice because they leave when you leave:

```sh {filename=".envrc"}
use flake

export RUST_LOG=debug
export PROJECT_ROOT=$PWD
```

Putting the repo's own scripts on your `PATH` without hardcoding a path to your home folder:

```sh {filename=".envrc"}
use flake

PATH_add ./scripts
PATH_add ./result/bin
```

Reading a file that is not checked in, for API keys, board serial numbers, whatever you would rather not push:

```sh {filename=".envrc"}
use flake

dotenv_if_exists .env
```

`direnv stdlib` prints every function it gives you, there are plenty more than these.

On that last one, `.envrc` is committed and `.env` is not. Anything you would be unhappy to find in the GitHub web UI goes in `.env`, gets pulled in with `dotenv_if_exists`, and `.env` goes straight into `.gitignore`. Do not put `export GITHUB_TOKEN=ghp_...` in an `.envrc`.

## Gitignore .direnv

Loading a flake leaves a cache folder next to the `.envrc`. It is machine specific build output and it is big:

```gitignore {filename=".gitignore"}
.direnv/
```

Ours already have that line. If you start a repo, put it in on day one, before you accidentally commit a few hundred megabytes.

## Make it fast, nix-direnv

Plain `use flake` re-evaluates the flake on every load, so that is a couple of seconds every time you `cd`, and the shell it built is not protected from `nix-collect-garbage`. Run a cleanup one afternoon and everything downloads again.

[nix-direnv](https://github.com/nix-community/nix-direnv) fixes both halves. It caches the evaluated environment and pins it so garbage collection walks past it. Loads become instant, and it only rebuilds when `flake.nix` or `flake.lock` actually changed.

Install it once, globally:

```sh
nix profile install nixpkgs#nix-direnv
```

Then point direnv at it, also once:

```sh
mkdir -p ~/.config/direnv
echo 'source $HOME/.nix-profile/share/nix-direnv/direnvrc' >> ~/.config/direnv/direnvrc
```

Nothing in any repo changes, `use flake` just quietly goes through nix-direnv from now on.

## The commands you will actually use

| Command | Does what |
| --- | --- |
| `direnv allow` | Trusts this `.envrc` and loads it, the one you type after cloning |
| `direnv reload` | Forces a reload, for when you changed `flake.nix` and the shell looks stale |
| `direnv deny` | Takes the trust back and unloads it |
| `direnv status` | Which config it read, which `.envrc` is loaded, whether it is allowed, start debugging here |
| `direnv exec . <cmd>` | Runs one command inside the environment without entering it, handy in scripts and Makefiles |

## When it goes sideways

**Nothing prints when I `cd` in.** The shell hook is not in place. Installing the binary is not enough, go back to your platform page, add the `eval "$(direnv hook ...)"` line to your shell rc, and open a new terminal.

**It loads, but my editor cannot find anything.** You started the editor from outside the folder, so it inherited your bare `PATH`. Launch it from a shell inside the project, `code .` or `nvim .`, or install the direnv extension for it.

**I changed `flake.nix` and nothing happened.** `direnv reload`. If it still looks wrong, `nix flake update` to move the locked inputs, then reload again.

**It says blocked every single time**, on a file you already allowed. Something keeps rewriting the file, a formatter, a merge, some script. `git status` will show you.

**Every load is slow.** You do not have nix-direnv yet, see above.

**I need it out of the way for a minute.** `cd` somewhere else, or `direnv deny` if you want it to stay off in that folder until you say otherwise.
