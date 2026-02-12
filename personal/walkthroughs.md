+++
title = "Walkthroughs"

hasmath = false
date = Date("01/03/2024", "dd/mm/yyyy")

tags = ["walkthrough", ]
+++


# Walkthroughs

Over the course of the last years, I experimented lots of stuff, mainly with computers and affiliated -- actually, making this website was such an experiment. Sometimes, a YouTube tutorial or a detailed guide gave me the exact steps to follow in order to succeed, simply and swiftly. Some other times, it was one or multiple obscure forum answers from a decade ago that unexpectedly helped me. And in some occasions... I got completely stuck.

So! Dearest reader, I wouldn't want that to happen to you. Thus, I will try to give some of the help I received from the (almost) all-knowing Internet back, and write about my aforementioned experiments. So, let's walk together on some of the tracks I traced.

\toc

<!-- ## Installing Linux

Start simple, start with Ubuntu or affiliated (personal recommendation: Linux Mint). Rice it if you want! -->

## Improving Linux

[Better Linux basic commands](/walkthrough/useful_linux_commands)

## Solving Linux questions

### Finding out the current notification daemon

- Find out who owns the notification bus (following [this link](https://unix.stackexchange.com/questions/379810/find-out-the-owner-of-a-dbus-service-name))
```bash
dbus-send --session --print-reply --dest=org.freedesktop.DBus / org.freedesktop.DBus.GetConnectionUnixProcessID string:org.freedesktop.Notifications
```
- This will give an integer, the PID (eg `3927`). Then:
```bash
ps -o 3927
```
replacing `3927` with your PID. Could probably do this with one line, but eh, if it works it works.

TODO for me: understand the buses and Freedesktop.

<!-- ## Installing Julia

Not that complicated, and well-explained. A tip: use `juliaup`. Maybe an opinionated article about Julia? [link](/walkthrough/first_steps_julia) -->

## Setting up a web server (still unsuccessful)

Easiest way for now : Apache2 on a Raspberry Pi.

<!-- ## GitHub

GitHub, ssh key, core concepts of git. -->

<!-- ## Add PPA repo, with the new, more secure way

Cf. taylor example -->

<!-- ## Collection of Julia performance tips I found

AllocCheck.jl, read the [doc page](https://docs.julialang.org/en/v1/manual/performance-tips/), it's great!
-->
## Setting up this website

I set up this website using [Franklin.jl](https://franklinjl.org/) and [Githup Pages](https://pages.github.com/). [See here](/walkthrough/website_with_franklin) for more info!