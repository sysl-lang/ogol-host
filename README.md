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
`Fault` of `More` holds the text and prompts `....` instead of complaining.

**`session` takes a `*Iterate[string]` and does not care what is producing the lines**, which is what
makes the board's console the same program: at a terminal that is a `sysl.term.edit.Editor` over
standard input in cbreak mode, over a serial cable it is the same editor over the port, and on a pipe
it is `sysl.io`'s cooked line cursor. The platform-dependent code is the six lines at the bottom of
`repl.sysl` that ask `sysl.term.tty.raw` whether there is a terminal to take over and build one or the
other from the answer.

**`raw()` refusing is not an error.** With input redirected there is nothing to change and an editor
is the wrong facility anyway — nothing is being typed and nothing should be echoed — so
`ogol-repl < script.ogol` goes on working and reads the stream.

Up and down walk a history of the last 64 lines; `←→`, Home, End, Delete and `Ctrl-A/E/B/F/U/K` all
work, in both of the spellings a terminal sends its arrow keys.

## Building

The language is an ordinary coordinate in `package.hocon`, so this builds from a fresh clone:

```
sysl build repl.sysl -o ogol-repl
```

It arrived by `--lib` from a checkout that had to be sitting beside this one until `sysl-lang/ogol`
was tagged `v0.1.0`.

**Needs sysl 0.0.38 or newer**, which is where `sysl.term.edit` and `sysl.term.tty.raw` ship.

**Build it and run the binary rather than using `sysl run`.** `sysl run` gained the program's standard
input in 0.0.37 (card `0056`), which is enough for a program that reads a pipe — but it does not pass
the *terminal* through, so `raw()` sees no tty, declines, and you get the cooked path with no echo and
no editing. The built binary inherits the terminal and behaves as it should.

## Licence

ISC.
