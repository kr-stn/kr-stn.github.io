---
published: true
layout: post
title: Isolate Conda on Linux Servers
---
I'm a huge fan of [conda](https://conda.io/docs/#) (especially on Windows) for any scientific Python work - it is a cross-platform package manager and virtual environment manager with batteries included. Conda is especially helpful for getting my colleagues set-up and ready to go and share environments. There is one big downside I encountered so far: installing it on a Linux systems has a weird default - it appends itself to the system Path and overwrites your systems Python alias. This is highly problematic in any (server) environment that depends on a stable Python version.

The easiest way I found to isolate conda from the system Python is by creating a new alias in my `.bashrc` that activates conda whenever I need it but leaves the system Python untouched for anything else.

```bash
# use alias to switch to Anaconda Python distribution
# keeps "python" pointing to system distribution
alias conda_on='export PATH="/users/krstn/miniconda3/bin:$PATH"'
```

Usage:

```bash
krstn@server:~$ conda_on
krstn@server:~$ source activate my_conda_env
```