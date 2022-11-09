---
title: Debugging model
---

# Debugging model

The evaluation of an expression may stop before it is completed for the
following reasons:

1. An evaluation error has occured, such as attempting to divide by zero.
2. The user has interrupted the evaluation (usually by typing `<^C>`).
3. A breakpoint has been reached or `(step)` was evaluated.
4. Single-stepping mode is enabled.

When an evaluation stops, a message is displayed indicating the reason and
location where the evaluation was stopped. The location information includes, if
known, the name of the procedure where the evaluation was stopped and the source
code location in the format `stream@line.column`, where `stream` is either a
string naming a file or a symbol within parentheses, such as `(console)`.

A nested REPL is then initiated in the context of the point of execution where
the evaluation was stopped. The nested REPL's continuation and evaluation
environment are the same as the point where the evaluation was stopped. For
example when evaluating the expression `(let ((y (- 1 1))) (* (/ x y) 2))`, a
"divide by zero" error is reported and the nested REPL's continuation is the one
that takes the result and multiplies it by two. The REPL's lexical environment
includes the lexical variable `y`. This allows the inspection of the evaluation
context (i.e. the lexical and dynamic environments and continuation), which is
particularly useful to determine the exact location and cause of an error.

The prompt of nested REPLs includes the nesting level; `1>` is the prompt at the
first nesting level, `2>` at the second nesting level, and so on. An end of file
(usually `<^D>`) will cause the current REPL to be terminated and the enclosing
REPL (one nesting level less) to be resumed.

At any time the user can examine the frames in the REPL's continuation, which is
useful to determine which chain of procedure calls lead to an error. A backtrace
that lists the chain of active continuation frames in the REPL's continuation
can be obtained with the `,b` command. The frames are numbered from `0`, that is
frame `0` is the most recent frame of the continuation where execution stopped,
frame `1` is the parent frame of frame `0`, and so on. It is also possible to
move the REPL to a specific parent continuation (i.e. a specific frame of the
continuation where execution stopped) with the `,N`, `,N+`, `,N-`, `,+`, `,-`,
`,++`, and `,--` commands. When the frame number of the frame being examined is
not zero, it is shown in the prompt after the nesting level, for example `1\5>`
is the prompt when the REPL nesting level is `1` and the frame number is `5`.

Expressions entered at a nested REPL are evaluated in the environment (both
lexical and dynamic) of the continuation frame currently being examined if that
frame was created by interpreted Scheme code. If the frame was created by
compiled Scheme code then expressions get evaluated in the global interaction
environment. This feature may be used in interpreted code to fetch the value of
a variable in the current frame or to change its value with `set!`. Note that
some special forms (`define` in particular) can only be evaluated in the global
interaction environment.
