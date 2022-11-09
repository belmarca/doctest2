---
title: Interactive mode
---

# Interactive mode

In interactive mode a read-eval-print loop (REPL) is started for the user to
interact with the interpreter. At each iteration of this loop the interpreter
displays a prompt, reads a command and executes it. The commands can be
expressions to evaluate (the typical case) or special commands related to
debugging, for example `,q` to terminate the process (for a complete list of
commands see [Debugging](/manual/debugging) or use the `,help` command). Most
commands produce some output, such as the value or error message resulting from
an evaluation.

The input and output of the interaction is done on the interaction channel. The
interaction channel can be specified through the runtime options but if none is
specified the system uses a reasonable default that depends on the system's
configuration. Typically the program's standard input and output are used as the
interaction channel. When using the runtime option `-:debug=c`, the interaction
channel is the user`s console, also known as the controlling terminal in the
UNIX world.

When the REPL starts, the ports associated with `(current-input-port)`,
`(current-output-port)` and `(current-error-port)` all refer to the interaction
channel.

Expressions are evaluated in the global interaction environment. The interpreter
adds to this environment any definition entered using the define and
define-macro special forms. Once the evaluation of an expression is completed,
the value or values resulting from the evaluation are output to the interaction
channel by the pretty printer. The special void object is not output. This
object is returned by most procedures and special forms which are defined as
returning an unspecified value (e.g. `write`, `set!`, `define`).

Here is a sample interaction with gsi:

```shell
$ gsi
Gambit v4.9.4

> (define (fact n) (if (< n 2) 1 (* n (fact (- n 1)))))
> (map fact '(1 2 3 4 5 6))
(1 2 6 24 120 720)
> (values (fact 10) (fact 40))
3628800
815915283247897734345611269596115894272000000000
> ,q
```

What happens when errors occur is explained in [Debugging](/manual/debugging).
