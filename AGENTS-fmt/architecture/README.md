# fmt architecture notes

`fmt` formats text. It is not a netlist, a timer, or a logic network.
`sv-elab` and slang use it for diagnostics and messages.

Fork: `https://github.com/etcimon/fmt`, branch `g6lc`, at the commit
`sv-elab` pins (`553ec11`). No algorithm edit on that branch.

## Execution

`fmt::format` allocates a `std::string` and writes into it.
`fmt::format_to` writes into a caller buffer and does not allocate if the
buffer is large enough. `fmt::print` writes to a file.

A format string is parsed into a small instruction list, then arguments are
visited. Parsing the format string is O(length of the format). Substituting
an argument is O(size of the rendered argument).

## Opportunities

- On a diagnostic path, `format` is fine.
- Inside a per-node or per-pin loop, build one buffer and use `format_to`,
  or do not format at all until the message is shown.
- Do not cache formatted strings keyed by a pointer that can be reused.
  The text would lie about a later object at the same address.

This library does not appear in the TinyRocket timing callgrind. Do not
retune it for STA.
