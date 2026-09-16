---
title: Built-in Help
order: 1
---

# Built-in Help

This page is compiled into the binary with `Kex.embed`, so the built
executable can print it without shipping a `.md` file alongside it or
reading anything from disk at runtime.

- no filesystem access
- no missing-file error to handle
- the help text ships with the binary, in sync by construction
