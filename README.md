# ogol-host

Ogol at a hosted terminal — a console for [the language](https://github.com/sysl-lang/ogol), on a
machine where it can be tried without a board in the way.

```
$ ogol-host
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

**Almost nothing is here.** The read-run-print loop is `sh.sysl.ogol.session`, in the language
package, because there is more than one console — this one, a Pico 2 W over a serial cable, an STM32
coming, and the Pico's other core. A loop written out per platform is copies that drift.

What is left in this repository is the only platform-dependent part: **which streams the lines come
from**. `session` takes a `*Iterate[string]` and does not care — at a terminal that is a
`sysl.term.edit.Editor` over standard input in cbreak mode, and on a pipe it is `sysl.io`'s cooked
line cursor. Those few lines ask `sysl.posix.tty.raw` whether there is a terminal to take over and
build one or the other from the answer.

**`host` rather than `desktop` or `mac`** because that is sysl's own word — `Target.host`, and "a
hosted program" throughout the library. It builds anywhere hosted.

**`raw()` refusing is not an error.** With input redirected there is nothing to change and an editor
is the wrong facility anyway — nothing is being typed and nothing should be echoed — so
`ogol-repl < script.ogol` goes on working and reads the stream.

Up and down walk a history of the last 64 lines; `←→`, Home, End, Delete and `Ctrl-A/E/B/F/U/K` all
work, in both of the spellings a terminal sends its arrow keys.

## Building

The language is an ordinary coordinate in `sysl.hocon`, so this builds from a fresh clone:

```
sysl build ogol-host.sysl -o ogol-host
```

It arrived by `--lib` from a checkout that had to be sitting beside this one until `sysl-lang/ogol`
was tagged `v0.1.0`.

**Needs sysl 0.0.51 or newer**, which is where the terminal module became `sysl.posix.tty`. The
facilities themselves are older — `sysl.term.edit` and `raw` ship in 0.0.38 — but the import path
above does not resolve before 0.0.51.

**Build it and run the binary rather than using `sysl run`.** `sysl run` gained the program's standard
input in 0.0.37 (card `0056`), which is enough for a program that reads a pipe — but it does not pass
the *terminal* through, so `raw()` sees no tty, declines, and you get the cooked path with no echo and
no editing. The built binary inherits the terminal and behaves as it should.

## Licence

ISC.
