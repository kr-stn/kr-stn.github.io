---
published: false
layout: post
title: Isolate Conda
---
I'm a huge fan of conda (especially on Windows) but installing it on Linux systems has a weird default - it appends itself to the system Path and overwrites your systems Python alias.

- can be changed with order of PATH in .bashrc
- if not --> show how to change to use alias conda_py='EXPORT....'