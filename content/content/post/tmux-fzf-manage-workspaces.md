---
author: Anton Suprun
title: Workspace navigation with tmux+fzf
date: 2024-08-16
description: A brief overview of my workspace management flow
---

Recently I've been exploring using tmux and fzf for a workspace management.
I wanted a quick way to navigate and jump between projects. Here is what I came up with.

### Prerequisites

Before you can use this workspace navigator, ensure you have the following installed:

1. [tmux](https://github.com/tmux/tmux/wiki) - a terminal multiplexer
2. [fzf](https://github.com/junegunn/fzf) - a command-line fuzzy finder
3. [fd](https://github.com/sharkdp/fd) - a simple, fast alternative to `find`

*Note: You'll need to have tmux running to use the `display-popup` command.*

---

Add this snippet to your `.tmux.conf` file:

```bash
bind k display-popup -T"Workspace Navigator" -w 75% -h 40% -E "~/bin/scripts/list-workspaces"
```

Here's that script:

`list-workspaces`
```bash
#!/opt/homebrew/bin/bash

fd . ~/Projects -d 1 -x echo {} {/} | \
fzf --header "session: enter  |  window: ctrl-w |  below: ctrl-j  |  above: ctrl-k  |  left: ctrl-h  |  right: ctrl-l" \
    --reverse \
    --margin 0% \
    --padding 0% \
    --with-nth=2 \
    --bind='enter:become(tmux has -t={2} 2>/dev/null || tmux new -ds {2} -c {1} && tmux switchc -t={2})' \
    --bind='ctrl-w:become(tmux neww -c {1} -n {2})' \
    --bind='ctrl-l:become(tmux splitw -c {1} -h)' \
    --bind='ctrl-h:become(tmux splitw -c {1} -hb)' \
    --bind='ctrl-j:become(tmux splitw -c {1} -v)' \
    --bind='ctrl-k:become(tmux splitw -c {1} -vb)' \
```

This script uses fd to list directories in the ~/Projects folder, then pipes the output to fzf for interactive filtering. It allows you to create or switch to tmux sessions, create new windows, and split panes in various directions using keyboard shortcuts. 

And here's it is in action:

![Alt text for the image](/workspace-nav.gif)

This has become my go-to approach for workspace navigation. It's especially useful in my job, where I work with many packages and microservices; being able to quickly jump between them makes this script a huge time-saver.

Try using it and let me know what you think!
