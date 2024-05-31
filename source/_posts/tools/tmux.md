---
title: tmux
date: 2024-05-17 08:51:43
categories:
- tools
tags:
---

tmux cheatsheet

<!-- more -->

## session

`tmux new-session -s <sesssion name>`: create new session, naming by -s

`tmux detach`, `Ctrl + b, d`: detach current session

`tmux attach -t <name, session number>`: attach to session name work

## window

`tmux new-window -n <window name>`: create new window, naming by -n

`Ctrl + b, <window number>`: select window
