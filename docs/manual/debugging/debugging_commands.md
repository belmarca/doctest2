---
title: Debugging commands
---

# Debugging commands

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

