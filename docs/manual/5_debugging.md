# Debugging

## Debugging model

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

## Debugging commands

In addition to expressions, the REPL accepts the following special "comma"
commands:

`,?` and `,help`
: Give a summary of the REPL commands.

`,(h subject)`
: This command will show the section of the Gambit manual with the definition of
the procedure or special form subject, which must be a symbol. For example `,(h
time)` will show the section documenting the time special form. Please see the
`help` procedure for additional information.

`,h`
: This command will show the section of the Gambit manual with the definition of
  the procedure which raised the exception for which this REPL was started.

`,q`
: Terminate the process with exit status `0`. This is equivalent to calling
  `(exit 0)`.

`,qt`
: Terminate the current thread (note that terminating the primordial thread
  terminates the process).

`,t`
: Return to the outermost REPL, also known as the "top-level REPL".

`,d`
: Leave the current REPL and resume the enclosing REPL. This command does
  nothing in the top-level REPL.

`,(c expr)`
: Leave the current REPL and continue the computation that initiated the REPL
  with a specific value. This command can only be used to continue a computation
  that signaled an error. The expression expr is evaluated in the current
  context and the resulting value is returned as the value of the expression
  which signaled the error. For example, if the evaluation of the expression `(*
  (/ x y) 2)` signaled an error because `y` is zero, then in the nested REPL a
  `,(c (+ 4 y))` will resume the computation of `(* (/ x y) 2)` as though the
  value of `(/ x y)` was `4`. This command must be used carefully because the
  context where the error occured may rely on the result being of a particular
  type. For instance a `,(c #f)` in the previous example will cause `*` to
  signal a type error (this problem is the most troublesome when debugging
  Scheme code that was compiled with type checking turned off so be careful).

`,c`
: Leave the current REPL and continue the computation that initiated the REPL.
  This command can only be used to continue a computation that was stopped due
  to a user interrupt, breakpoint or a single-step.

`,s`
: Leave the current REPL and continue the computation that initiated the REPL in
  single-stepping mode. The computation will perform an evaluation step (as
  defined by `step-level-set!`) and then stop, causing a nested REPL to be
  entered. Just before the evaluation step is performed, a line is displayed (in
  the same format as `trace`) which indicates the expression that is being
  evaluated. If the evaluation step produces a result, the result is also
  displayed on another line. A nested REPL is then entered after displaying a
  message which describes the next step of the computation. This command can
  only be used to continue a computation that was stopped due to a user
  interrupt, breakpoint or a single-step.

`,l`
: This command is similar to `,s` except that it "leaps" over procedure calls,
  that is procedure calls are treated like a single step. Single-stepping mode
  will resume when the procedure call returns, or if and when the execution of
  the called procedure encounters a breakpoint.

`,N`
: Move to frame number `N` of the continuation. After changing the current frame,
  a one-line summary of the frame is displayed as if the `,y` command was
  entered.

`,N+`
: Move forward by `N` frames in the chain of continuation frames (i.e. towards
  older continuation frames). After changing the current frame, a one-line
  summary of the frame is displayed as if the `,y` command was entered.

`,N-`
: Move backward by `N` frames in the chain of continuation frames (i.e. towards
  more recent continuation frames). After changing the current frame, a one-line
  summary of the frame is displayed as if the `,y` command was entered.

`,+`
: Equivalent to `,1+`.

`,-`
: Equivalent to `,1-`.

`,++`
: Equivalent to `,N+` where `N` is the number of continuation frames displayed at
  the head of a backtrace.

`,--`
: Equivalent to `,N-` where `N` is the number of continuation frames displayed
  at the head of a backtrace.

`,y`
: Display a one-line summary of the current frame. The information is displayed
  in four fields. The first field is the frame number. The second field is the
  procedure that created the frame or `(interaction)` if the frame was created
  by an expression entered at the REPL. The remaining fields describe the
  subproblem associated with the frame, that is the expression whose value is
  being computed. The third field is the location of the subproblem's source
  code and the fourth field is a reproduction of the source code, possibly
  truncated to fit on the line. The last two fields may be missing if that
  information is not available. In particular, the third field is missing when
  the frame was created by a user call to the `eval` procedure or by a compiled
  procedure not compiled with the declaration `debug-location`, and the last
  field is missing when the frame was created by a compiled procedure not
  compiled with the declaration `debug-source`.

`,b`
: Display a backtrace summarizing each frame in the chain of continuation frames
  starting with the current frame. For each frame, the same information as for
  the `,y` command is displayed (except that location information is displayed
  in the format `stream@line:column`). If there are more than 15 frames in the
  chain of continuation frames, some of the middle frames will be omitted.

`,be`
: Like the `,b` command but also display the environment.

`,bed`
: Like the `,be` command but also display the dynamic environment.

`,(b expr)`
: Display the backtrace of expr's value, `X`, which is obtained by evaluating `expr`
in the current frame. `X` must be a continuation or a thread. When `X` is a
continuation, the frames in that continuation are displayed. When `X` is a
thread, the backtrace of the current continuation of that thread is displayed.

`,(be expr)`
: Like the `,(b expr)` command but also display the environment.

`,(bed expr)`
: Like the `,(be expr)` command but also display the dynamic environment.

`,i`
: Pretty print the procedure that created the current frame or `(interaction)`
  if the frame was created by an expression entered at the REPL. Compiled
  procedures will only be pretty printed when they are compiled with the
  declaration `debug-source`.

`,e`
: Display the environment which is accessible from the current frame. The
  lexical environment is displayed, followed by the dynamic environment if the
  parameter object `repl-display-dynamic-environment?` is not `#f`. Global
  lexical variables are not displayed. Moreover the frame must have been created
  by interpreted code or code compiled with the declaration
  `debug-environments`. Due to space safety considerations and compiler
  optimizations, some of the lexical variable bindings may be missing. Lexical
  variable bindings are displayed using the format `variable = expression` (when
  variable is mutable) or `variable == expression` (when variable is immutable,
  which may happen in compiled code due to compiler optimization) and
  dynamically-bound parameter bindings are displayed using the format
  `(parameter) = expression`. Note that expression can be a self-evaluating
  expression (`number`, `string`, `boolean`, `character`, ...), a `quote`d
  expression, a `lambda` expression or a global variable (the last two cases,
  which are only used when the value of the variable or parameter is a
  procedure, simplifies the debugging of higher-order procedures). A parameter
  can be a `quote`d expression or a global variable. Lexical bindings are
  displayed in inverse binding order (most deeply nested first) and shadowed
  variables are included in the list.

`,ed`
: Like the `,e` command but the dynamic environment is always displayed.

`,(e expr)`
: Display the environment of expr's value, `X`, which is obtained by evaluating
  expr in the current frame. `X` must be a continuation, a thread, a procedure, or
  a nonnegative integer. When `X` is a continuation, the environment at that point
  in the code is displayed. When `X` is a thread, the environment of the current
  continuation of that thread is displayed. When `X` is a procedure, the lexical
  environment where `X` was created is combined with the current continuation and
  this combined environment is displayed. When `X` is an integer, the environment
  at frame number `X` of the continuation is displayed.

`,(ed expr)`
: Like the `,(e expr)` command but the dynamic environment is always displayed.

`,st`
: Display the state of the threads in the current thread's thread group. A
  thread can be: `uninitialized`, `initialized`, `active`, and `terminated`
  (normally or abnormally). Active threads can be `running`, `sleeping` and
  `waiting` on a synchronization object (`mutex`, condition variable or `port`)
  possibly with a timeout.

`,(st expr)`
: Display the state of a specific thread or thread group. The value of `expr` must
  be a `thread` or `thread group`.

`,(v expr)`
: Start a new REPL visiting expr's value, `X`, which is obtained by evaluating
expr in the current frame. `X` must be a continuation, a thread, a procedure, or a
nonnegative integer. When `X` is a continuation, the new REPL's continuation is `X`
and evaluations are done in the environment at that point in the code. When `X` is
a thread, the thread is interrupted and the new REPL's continuation is the point
where the thread was interrupted. When `X` is a procedure, the lexical environment
where `X` was created is combined with the current continuation and evaluations
are done in this combined environment. When `X` is an integer, the REPL is started
in frame number `X` of the continuation.

## Debugging example

Here is a sample interaction with `gsi`:

```shell
$ gsi
Gambit v4.9.4

> (define (invsqr x) (/ 1 (expt x 2)))
> (define (mymap fn lst)
    (define (mm in)
      (if (null? in)
          '()
          (cons (fn (car in)) (mm (cdr in)))))
    (mm lst))
> (mymap invsqr '(5 2 hello 9 1))
*** ERROR IN invsqr, (console)@1.25 -- (Argument 1) NUMBER expected
(expt 'hello 2)
1> ,i
#<procedure #2 invsqr> =
(lambda (x) (/ 1 (expt x 2)))
1> ,e
x = 'hello
1> ,b
0  invsqr                    (console)@1:25          (expt x 2)
1  #<procedure #4>           (console)@6:17          (fn (car in))
2  #<procedure #4>           (console)@6:31          (mm (cdr in))
3  #<procedure #4>           (console)@6:31          (mm (cdr in))
4  (interaction)             (console)@8:1           (mymap invsqr '(5 2 hel...
1> ,+
1  #<procedure #4>           (console)@6.17          (fn (car in))
1\1> (pp #4)
(lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
1\1> ,e
in = '(hello 9 1)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\1> ,(e mm)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\1> fn
#<procedure #2 invsqr>
1\1> (pp fn)
(lambda (x) (/ 1 (expt x 2)))
1\1> ,+
2  #<procedure #4>           (console)@6.31          (mm (cdr in))
1\2> ,e
in = '(2 hello 9 1)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\2> ,(c (list 3 4 5))
(1/25 1/4 3 4 5)
> ,q
```

## Procedures related to debugging

### help
### help-browser

```scheme
(help [subject]) ;; procedure
(help-browser [new-value]) ;; procedure
```

The `help` procedure displays the section of the Gambit manual with the
definition of the procedure or special form subject, which must be a procedure
or symbol. For example the call `(help gensym)` will show the section
documenting the `gensym` procedure and the call `(help 'time)` will show the
section documenting the `time` special form. When the `subject` is absent, the
documentation of the `help` procedure is shown. The help procedure returns the
`void` object.

The parameter object `help-browser` is bound to a string naming the external
program that is used by the `help` procedure to view the documentation.
Initially it is bound to the empty string. In normal circumstances when
`help-browser` is bound to an empty string the `help` procedure runs the script
`~~bin/gambdoc.bat` which searches for a suitable web browser to open the
documentation in HTML format. Unless the system was built with the command
`configure --enable-help-browser=...`, the text-only browser `lynx` (see
[http://lynx.isc.org/](http://lynx.isc.org/)) will be used by default if it is
available. We highly recommend that you install this browser if you are
interested in viewing the documentation within the console in which the REPL is
running. You can exit `lynx` conveniently by typing an end of file (usually
`<^D>`).

For example:

```scheme
> (help-browser "firefox") ; use firefox instead of lynx
> (help 'gensym)
> (help gensym) ; OK because gensym is a procedure
> (help 'time)
> (help time) ; not OK because time is a special form
*** ERROR IN (console)@5.7 -- Macro name can't be used as a variable: time
>
```

### apropos

```scheme
(apropos [substring [port]]) ;; procedure
```

The `apropos` procedure writes to the port `port` a report of all the global
variables whose name contains `substring`, a `string` or `symbol`. If `substring` is not
specified the report contains all the global variables. If it is not specified,
`port` defaults to the interaction channel (i.e. the output will appear at the
REPL). The `apropos` procedure returns the `void` object.

The global variables are grouped into namespaces. The empty namespace, if it is
relevant, is last. This reduces the likelihood it will scroll off the screen if
there are several global variables in other namespaces, which are typically less
interesting.

Note that with the `apropos` procedure it is possible to reveal the existence of
procedures of the runtime system and modules that are not intended to be called
by user code. These procedures often avoid type checking their arguments or must
be called in a specific context, so calling them incorrectly may crash the
system. On the other hand it also allows discovering the existence of certain
functionalities that may have gone unnoticed.

For example:

```scheme
> (apropos "cons")
"##" namespace:
  10^-constants, cons, cons*, cons*-aux, console-port,
  constant-expression-value, constant-expression?,
  cprc-quasi-cons, deconstruct-call,
  define-type-construct-constant, degen-quasi-cons,
  gen-quasi-cons, quasi-cons, stdio/console-repl-channel,
  void-constant?, xcons
empty namespace:
  cons, cons*, console-port, xcons
> (import (srfi 69))
> (apropos "table?")
"##" namespace:
  gc-hash-table?, mutable?, readtable?, table?
"srfi/69#" namespace:
  hash-table?
empty namespace:
  readtable?, table?
> (apropos "srfi/69#")
"srfi/69#" namespace:
  ||, alist->hash-table, hash, hash-by-identity,
  hash-table->alist, hash-table-copy, hash-table-delete!,
  hash-table-equivalence-function, hash-table-exists?,
  hash-table-fold, hash-table-hash-function,
  hash-table-keys, hash-table-merge!, hash-table-ref,
  hash-table-ref/default, hash-table-set!, hash-table-size,
  hash-table-update!, hash-table-update!/default,
  hash-table-values, hash-table-walk, hash-table?,
  make-hash-table, string-ci-hash, string-hash
```

### repl-result-history-ref
### repl-result-history-max-length-set!

```scheme
(repl-result-history-ref i) ;; procedure
(repl-result-history-max-length-set! n) ;; procedure
```

The REPL keeps a history of the last few results printed by the REPL. The call
`(repl-result-history-ref i)` returns the `i`th previous result (the last for `i=0`,
the next to last for `i=1`, etc). By default the REPL result history remembers up
to 3 results. The maximal length of the history can be set to `n` between `0` and `10`
by a call to `(repl-result-history-max-length-set! n)`.

For convenience the reader defines an abbreviation for calling
`repl-result-history-ref`. Tokens formed by a sequence of one or more hash signs,
such as `#`, `##`, etc, are expanded by the reader into the list
`(repl-result-history-ref i)`, where `i` is the number of hash signs minus 1. In
other words, `#` will return the last result printed by the REPL, `##` will
return the next to last, etc.

For example:

```scheme
> (map (lambda (x) (* x x)) '(1 2 3))
(1 4 9)
> (reverse #)
(9 4 1)
> (append # ##)
(9 4 1 1 4 9)
> 1
1
> 1
1
> (+ # ##)
2
> (+ # ##)
3
> (+ # ##)
5
> ####
*** ERROR IN (console)@9.1 -- (Argument 1) Out of range
(repl-result-history-ref 3)
1>
```
