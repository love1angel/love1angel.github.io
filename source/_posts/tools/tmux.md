---
title: tmux
date: 2024-05-17 08:51:43
categories:
- tools
tags:
---

本文主要记录 tmux 使用。

<!-- more -->

## session

新建 session，命名为 work
`tmux new-session -s work`

分离当前 session
`c-b d`

attach 到当前 session
`tmux attach -t work`
