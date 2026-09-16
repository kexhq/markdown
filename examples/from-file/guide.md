---
title: Field Notes
author: Ada
tags: kex, docs
---

# Field Notes

Some **bold**, some *italic*, and a [link](https://kex.run).

> [!TIP]
> Frontmatter keys this package doesn't know about — `author`, `tags` — still
> end up in `frontmatter.fields`.

## A short list

- reads from disk at runtime
- one file, one render

```kex
using Markdown

main do
  IO.printLine(Markdown.toHtml("# Hi"))
end
```
