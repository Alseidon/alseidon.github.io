+++
title = "Useful Linux commands"

hasmath = false
date = Date("06/03/2024", "dd/mm/yyyy")

tags = ["walkthrough", "linux", ]
+++

# Useful Linux commands

In my first years using Linux, I quickly learned a lot of basic commands to use in the terminal: `cd`, `ls`, `cat` etc. Only recently did I discover that while these commands are incredibly helpful, they did not *need* to be so basic: alternatives existed, that *enhanced* them in many ways. Here is a list of some replacements I integrated in my everyday workflow that brought meaningful improvements.

\toc

## A better `cd`: `zoxide`

[Link](https://github.com/ajeetdsouza/zoxide)

For this one, you will also need [fzf](https://github.com/junegunn/fzf#installation), a fuzzy file finder, to use zoxide to its full potential.

This one also need an additional shell setup step; for me (bash), I just appended this to my `.bashrc`:
```bash
# init zoxide
eval "$(zoxide init --cmd cd bash)"
```

## A better `ls` and `tree`: `eza`

[Link](https://github.com/eza-community/eza)

My current aliases for `eza` are:
```bash
# list all (including hidden) files as a list,
# in human mode, with git status
alias lf='eza -lah --git'

# same but as a tree
alias treza='lf --tree'
```

## A better `cat`: `bat`

[Link](https://github.com/sharkdp/bat)

## A better `top`: `btop`

[Link](https://github.com/aristocratos/btop)

## A shorter `man`: `tldr`
This one is not exactly better, but a lot of the time I used `man`, I was just looking for a basic rundown of the most common usecases. This is exactly what `tldr` offers.

[Link](https://github.com/tldr-pages/tldr)

## Other commands I didn't look at yet
- A better `grep`: `ripgrep`
- A better `find`: `fd`
- A better terminal (OK not exactly a command but still): `tmux`