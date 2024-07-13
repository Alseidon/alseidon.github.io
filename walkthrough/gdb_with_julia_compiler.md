+++
title = "Using GDB with the Julia compiler"

hasmath = false
date = Date("13/07/2024", "dd/mm/yyyy")

tags = ["code", "julia", ]
+++

# Using GDB with the Julia compiler

## Context

When starting to dive in the Julia internals, you will encounter C and C++ files at some point. Debugging these can be a pain... Without the right tools. And [GDB](https://www.sourceware.org/gdb/) is definitely one of them. It can be scary to use though... Especially when there is Julia running on top!

Things aren't that complicated though. See for yourself!

(Note that these are instructions for Linux. They should be somewhat reproducible with Windows/MacOS, but I can't guarantee it.)

##  Launching GDB with Julia

- Start a new Julia REPL. Type `getpid()` to get Julia's current process id.
- In another terminal, type `sudo gdb --pid JULIA_PID`, replacing `JULIA_PID` with the process id you just got. `sudo` is required to attach gdb to the Julia process like this.
- You are good to go! Follow [these debugging tips](https://docs.julialang.org/en/v1/devdocs/debuggingtips/) to get started.

## The easier way

The method above sure works, but it requires two terminals (might be annoying) and `sudo` (might be problematic). You can also simply type `gdb julia`, starting a new gdb session. Set your breakpoints, and then `run` will actually start Julia and you will switch to its REPL. When encountering a breakpoint, you switch back to the gdb console. You can go back and forth like this.

The lack of simultaneous control on gdb and julia can be annoying, but this is quicker to set up. The choice is yours!