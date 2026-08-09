# ogol-repl

Ogol at a desktop terminal — a console for [the language](https://github.com/sysl-lang/ogol), on a
machine where it can be tried without a board in the way.

```
$ ogol-repl
Ogol. Type a line, or 'bye' to leave.
ogol> print 2 + 3 * 4
14
ogol> to double :n
....   output n * 2
.... end
ogol> print double 21
42
ogol> bye
```

**It is deliberately thin.** Reading a line, deciding whether the line finished, and printing what
came back is the whole of a console's job — thirty lines, of which the interesting one is that a
`Fault` of `More` holds the text and prompts `....` instead of complaining. The board's console is
the same file with `sysl.io` swapped for `sh.sysl.pico2.read_line`, and that one difference is why
the language can be developed here and shipped there.

## Building

`sysl-lang/ogol` is not tagged yet, so it arrives as a source root rather than as a coordinate, and
**this does not build from a fresh clone without the checkout beside it**:

```
sysl build repl.sysl --lib ../ogol -o ogol-repl
```

**`sysl run` works from sysl 0.0.37**, which is the release that connected the program's standard
input — before it, the console read end-of-input at once and exited after printing its banner, which
was card `0056`. On an older compiler, build it and run the binary.

## Licence

ISC.
