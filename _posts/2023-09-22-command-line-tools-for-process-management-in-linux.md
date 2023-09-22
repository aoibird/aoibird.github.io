---
layout: post
title: Command line tools for process management in Linux
categories: Toolbox
tags: ["Linux", "CLI"]
---

## top & htop

The classic tool for displaying processes in Linux is, of course, the `top` command. Simply type `top`, and you'll be greeted with a dynamic, real-time overview of running processes. However, while the `top` command certainly offers a dynamic view, it can appear a bit rudimentary. If you're looking for a more visually appealing option, consider using `htop`. This command provides an interactive process view that elevates the experience. Just type `htop`, and you'll find yourself in a TUI that neatly presents the processes. You can click and scroll through the list for a more user-friendly experience. Here's an example of what the `htop` interface looks like:

![Htop](/assets/htop-2.0.png)

## ps

Certainly, if you're not concerned about having a fancy interface and simply need a list of processes that can seamlessly work with other commands like `grep`, then `ps` is the right choice for you. Just by typing it in:
```bash
ps aux
```
You'll get a list of all the running processes, and you can easily use the `grep` command to pinpoint exactly what you're interested in:
```bash
ps aux | grep python
```
where the `a` option stands for "all users", `u` is used to display ownership information, and `x` ensures that all processes, not just those attached to a terminal, are shown.


## kill & killall

If you encounter a process that has become unresponsive or stuck, you'll likely want to stop it. The `kill` command is used to terminate a process either by specifying its PID or its name. On the other hand, `killall` can be used to terminate all processes running a specific command. Both of these commands allow you to specify a signal when terminating a process. For example, using `-9` as a signal corresponds to sending the `SIGKILL` signal, which forcefully terminates the process:
```bash
kill -9 1260748
killall -9 python
```
