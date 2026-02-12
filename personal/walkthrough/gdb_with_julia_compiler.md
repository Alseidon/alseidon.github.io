+++
title = "Using GDB with the Julia compiler"

hasmath = false
date = Date("13/07/2024", "dd/mm/yyyy")

tags = ["code", "julia", ]
+++

# Using GDB with the Julia compiler

\toc

## Context

When starting to dive in the Julia internals, you will encounter C and C++ files at some point. Debugging these can be a pain... Without the right tools. And [GDB](https://www.sourceware.org/gdb/) is definitely one of them. It can be scary to use though... Especially when there is Julia running on top!

Things aren't that complicated though. See for yourself!

(Note that these are instructions for Linux. They should be somewhat reproducible with Windows/MacOS, but I can't guarantee it.)

##  Connecting GDB and Julia

### Attaching GDB to a running Julia REPL

- In your Julia REPL, type `getpid()` to get Julia's current process id.
- In another terminal, type `sudo gdb --pid JULIA_PID`, replacing `JULIA_PID` with the process id you just got. `sudo` is required to attach gdb to the Julia process like this.
- Setup breakpoints: you can type `break function_name` to break when the function is called, or `break path/to/file.cpp:1234` to break when the line in the given file is reached.
- Type `c` (short for `continue`) to let the Julia process continue.
- You are good to go! Follow [these debugging tips](https://docs.julialang.org/en/v1/devdocs/debuggingtips/) to get started.

### The easier way: launching Julia from gdb

The method above sure works, but it requires two terminals (might be annoying) and `sudo` (might be problematic). You can also simply type `gdb julia`, starting a new gdb session. Set your breakpoints, and then `run` will actually start Julia and you will switch to its REPL. When encountering a breakpoint, you switch back to the gdb console. You can go back and forth like this.

The lack of simultaneous control on gdb and julia can be annoying, but this is quicker to set up. The choice is yours!

## Some tips

### Printing variables
More info [here](https://docs.julialang.org/en/v1/devdocs/debuggingtips/#Useful-Julia-functions-for-Inspecting-those-variables).
- For `jl_value_t *` variables, you can type `call jl_(my_var)` in GDB to print `my_var` in the Julia REPL.
- For LLVM variables, you can type `call my_var->dump()` in GDB to print `my_var` in the GDB terminal.

### GDB caveats
- Typing `Enter` with an empty line in GDB repeats the last command. And commands are buffered! Meaning, if you type Enter 5 times after running `continue`, it will `continue` 5 times after hitting a breakpoint, effectively ignoring your 5 next breakpoints. Do *not* rage type `Enter`!