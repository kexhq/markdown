# Markdown

A documented Markdown subset → HTML, for Kex's own docs and hand-written
prose.

## Installing as a `Tey` package

```sh
tey add markdown --git https://github.com/kexhq/markdown --tag "~> 0.1"
tey install
```

Or pull it in manually in your own package's `package.kex`:

```rb
tey("markdown", git: "https://github.com/kexhq/markdown", tag: "~> 0.1")
```

The tag is the release, and `~> 0.1` takes the newest `0.1.x`; Tey resolves
it against this repository's tags and records the exact commit in
`tey.lock` — the same way [Rodolfo](https://github.com/kexhq/rodolfo) is
installed. Then `tey install`, and in the source:

```rb
using Markdown
```

Supported, including the GitHub Flavored Markdown extensions people actually
reach for: YAML-ish frontmatter (with unknown keys kept, not just the ones
this module reads), ATX headings, paragraphs, fenced code (` ```kex ` runs
through a small Kex syntax highlighter), blockquotes, GitHub-style alerts
(`> [!NOTE]` and friends), ordered and unordered lists with nesting and
task-list checkboxes (`- [ ]` / `- [x]`), GFM tables, thematic breaks, and
inline code / bold / italics / strikethrough / links / images. No table
column alignment, no autolinks, no HTML passthrough, no reference links, no
setext headings — anything else stays a paragraph rather than becoming an
error.

```rb
using Markdown

main(args) do
  IO.printLine(Markdown.toHtml("# Hello

Some **bold** text."))
end
```

## Usage

A page usually has more going on than one heading. This one has
frontmatter, a task list, a table, an alert, and a fenced Kex snippet — the
kind of mix a real changelog or guide page has:

````rb
using Markdown

let page = "---
title: v0.2.0
---
# v0.2.0

> [!NOTE]
> Table rendering changed shape; templates reading `Table` may need updates.

- [x] Ship task-list checkboxes
- [ ] Write the migration guide

| Change | Area |
| - | - |
| Faster parsing | core |

```kex
let shipped = true
```"

main do
  let doc = Markdown.parseDocument(page)
  IO.printLine("Release: ${doc.frontmatter.title}")   # "Release: v0.2.0"
  IO.printLine(Markdown.toHtml(page))                 # the full rendered page
end
````

`toHtml` highlights the `kex` fence (`<span class="tok-keyword">`, …);
`toAuthoringHtml` leaves fenced code as plain text instead, for an editor
that reads a code block back out as source and would lose highlight spans
wrapped around its newlines.

## Frontmatter

`parseDocument` splits a leading `---` fence off the body. `title`,
`description`, and `order` get typed accessors; every key seen — those three
included — also lands in `frontmatter.fields`, so a caller with its own
schema can read a page's `author`, `tags`, or anything else without this
module needing to know about it:

```rb
let doc = Markdown.parseDocument(text)
let author = doc.frontmatter.fields.get("author").or("")
```

## Examples

- `examples/from-file` — reads a `.md` file from disk at runtime with
  `FS.File.read` and renders it, including custom frontmatter fields
- `examples/embed` — compiles a `.md` file into the binary at build time
  with `Kex.embed`, for help text or docs that ship with the executable

```sh
cd examples/from-file
tey install
tey run
```

`tey test` at the root runs `spec/`; each example is its own workspace
member, so `tey build` here builds them too.

`Markdown.Inline` (code spans, links, images, emphasis) and
`Markdown.Highlight` (the Kex syntax highlighter fenced code runs through)
are also usable directly.

## Release it

Bump `version` in `package.kex`, then:

```sh
git tag v0.2.0 && git push origin v0.2.0
```

The tag is the release: Tey resolves a requirement like `tag: "~> 0.1"` by
listing this repository's tags, and the push triggers `release.yml`, which
runs the test suite and publishes a GitHub release.
