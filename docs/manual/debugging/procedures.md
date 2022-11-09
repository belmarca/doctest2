---
title: Procedures related to debugging
---

# Procedures related to debugging

#### help
#### help-browser

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

#### apropos

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

#### repl-result-history-ref
#### repl-result-history-max-length-set!

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
> #####
*** ERROR IN (console)@9.1 -- (Argument 1) Out of range
(repl-result-history-ref 3)
1>
```

#### trace
#### untrace

```scheme
(trace proc ...) ;; procedure
(untrace proc ...) ;; procedure
```

The `trace` procedure starts tracing calls to the specified procedures. When a
traced procedure is called, a line containing the procedure and its arguments is
displayed (using the procedure call expression syntax). The line is indented
with a sequence of vertical bars which indicate the nesting depth of the
procedure’s continuation. After the vertical bars is a greater-than sign which
indicates that the evaluation of the call is starting.

When a traced procedure returns a result, it is displayed with the same
indentation as the call but without the greater-than sign. This makes it easy to
match calls and results (the result of a given call is the value at the same
indentation as the greater-than sign). If a traced procedure `P1` performs a
tail call to a traced procedure `P2`, then `P2` will use the same indentation as
`P1`. This makes it easy to spot tail calls. The special handling for tail calls
is needed to preserve the space complexity of the program (i.e. tail calls are
implemented as required by Scheme even when they involve traced procedures).

The untrace procedure stops tracing calls to the specified procedures. When no
argument is passed to the trace procedure, the list of procedures currently
being traced is returned. The `void` object is returned by the `trace` procedure
when it is passed one or more arguments. When no argument is passed to the
`untrace` procedure stops all tracing and returns the `void` object. A compiled
procedure may be traced but only if it is bound to a global variable.

For example:

```scheme
> (define (fact n) (if (< n 2) 1 (* n (fact (- n 1)))))
> (trace fact)
> (fact 5)
| > (fact 5)
| | > (fact 4)
| | | > (fact 3)
| | | | > (fact 2)
| | | | | > (fact 1)
| | | | | 1
| | | | 2
| | | 6
| | 24
| 120
120
> (trace -)
*** WARNING -- Rebinding global variable "-" to an interpreted procedure
> (define (fact-iter n r) (if (< n 2) r (fact-iter (- n 1) (* n r))))
> (trace fact-iter)
> (fact-iter 5 1)
| > (fact-iter 5 1)
| | > (- 5 1)
| | 4
| > (fact-iter 4 5)
| | > (- 4 1)
| | 3
| > (fact-iter 3 20)
| | > (- 3 1)
| | 2
| > (fact-iter 2 60)
| | > (- 2 1)
| | 1
| > (fact-iter 1 120)
| 120
120
> (trace)
(#<procedure #2 fact-iter> #<procedure #3 -> #<procedure #4 fact>)
> (untrace)
> (fact 5)
120
```

#### step
#### step-level-set!

```scheme
(step) ;; procedure
(step-level-set! level) ;; procedure
```

The `step` procedure enables single-stepping mode. After the call to `step` the
computation will stop just before the interpreter executes the next evaluation
`step` (as defined by `step-level-set!`). A nested REPL is then started. Note that
because single-stepping is stopped by the REPL whenever the prompt is displayed
it is pointless to enter `(step)` by itself. On the other hand entering `(begin
(step) expr)` will evaluate `expr` in single-stepping mode.

The procedure `step-level-set!` sets the stepping level which determines the
granularity of the evaluation steps when single-stepping is enabled. The
stepping level level must be an exact integer in the range `0` to `7`. At a level of
`0`, the interpreter ignores single-stepping mode. At higher levels the
interpreter stops the computation just before it performs the following
operations, depending on the stepping level:

1. procedure call
2. `delay` special form and operations at lower levels
3. `lambda` special form and operations at lower levels
4. `define` special form and operations at lower levels
5. `set!` special form and operations at lower levels
6. variable reference and operations at lower levels
7. constant reference and operations at lower levels
8. The default stepping level is 7.

For example:

```scheme
> (define (fact n) (if (< n 2) 1 (* n (fact (- n 1)))))
> (step-level-set! 1)
> (begin (step) (fact 5))
*** STOPPED IN (console)@3.15
1> ,s
| > (fact 5)
*** STOPPED IN fact, (console)@1.22
1> ,s
| | > (< n 2)
| | #f
*** STOPPED IN fact, (console)@1.43
1> ,s
| | > (- n 1)
| | 4
*** STOPPED IN fact, (console)@1.37
1> ,s
| | > (fact (- n 1))
*** STOPPED IN fact, (console)@1.22
1> ,s
| | | > (< n 2)
| | | #f
*** STOPPED IN fact, (console)@1.43
1> ,s
| | | > (- n 1)
| | | 3
*** STOPPED IN fact, (console)@1.37
1> ,l
| | | > (fact (- n 1))
*** STOPPED IN fact, (console)@1.22
1> ,l
| | > (* n (fact (- n 1)))
| | 24
*** STOPPED IN fact, (console)@1.32
1> ,l
| > (* n (fact (- n 1)))
| 120
120
```

#### break
#### unbreak

```scheme
(break proc ...) ;; procedure
(unbreak proc ...) ;; procedure
```

The `break` procedure places a breakpoint on each of the specified procedures.
When a procedure is called that has a breakpoint, the interpreter will enable
single-stepping mode (as if `step` had been called). This typically causes the
computation to stop soon inside the procedure if the stepping level is high
enough.

The `unbreak` procedure removes the breakpoints on the specified procedures. With
no argument, `break` returns the list of procedures currently containing
breakpoints. The `void` object is returned by `break` if it is passed one or more
arguments. With no argument `unbreak` removes all the breakpoints and returns the
`void` object. A breakpoint can be placed on a compiled procedure but only if it
is bound to a global variable.

For example:

```scheme
> (define (double x) (+ x x))
> (define (triple y) (- (double (double y)) y))
> (define (f z) (* (triple z) 10))
> (break double)
> (break -)
*** WARNING -- Rebinding global variable "-" to an interpreted procedure
> (f 5)
*** STOPPED IN double, (console)@1.21
1> ,b
0  double                    (console)@1:21          +
1  triple                    (console)@2:31          (double y)
2  f                         (console)@3:18          (triple z)
3  (interaction)             (console)@6:1           (f 5)
1> ,e
x = 5
1> ,c
*** STOPPED IN double, (console)@1.21
1> ,c
*** STOPPED IN f, (console)@3.29
1> ,c
150
> (break)
(#<procedure #3 -> #<procedure #4 double>)
> (unbreak)
> (f 5)
150
```

#### generate-proper-tail-calls

```scheme
(generate-proper-tail-calls [new-value]) ;; procedure
```
!!! warning "Deprecation warning"
    This procedure is DEPRECATED and will be removed in a future version of
    Gambit. Use the `proper-tail-calls` declaration instead.
   
The parameter object `generate-proper-tail-calls` is bound to a boolean value
controlling how the interpreter handles tail calls. When it is bound to `#f` the
interpreter will treat tail calls like nontail calls, that is a new continuation
will be created for the call. This setting is useful for debugging, because when
a primitive signals an error the location information will point to the call
site of the primitive even if this primitive was called with a tail call. The
initial value of this parameter object is `#t`, which means that a tail call will
reuse the continuation of the calling function.

This parameter object only affects code that is subsequently processed by `load`
or `eval`, or entered at the REPL.

For example:

```scheme
> (generate-proper-tail-calls)
#t
> (let loop ((i 1)) (if (< i 10) (loop (* i 2)) oops))
*** ERROR IN #<procedure #2>, (console)@2.47 -- Unbound variable: oops
1> ,b
0  #<procedure #2>           (console)@2:47          oops
1  (interaction)             (console)@2:1           ((letrec ((loop (lambda...
1> ,t
> (generate-proper-tail-calls #f)
> (let loop ((i 1)) (if (< i 10) (loop (* i 2)) oops))
*** ERROR IN #<procedure #3>, (console)@6.47 -- Unbound variable: oops
1> ,b
0  #<procedure #3>           (console)@6:47          oops
1  #<procedure #3>           (console)@6:32          (loop (* i 2))
2  #<procedure #3>           (console)@6:32          (loop (* i 2))
3  #<procedure #3>           (console)@6:32          (loop (* i 2))
4  #<procedure #3>           (console)@6:32          (loop (* i 2))
5  (interaction)             (console)@6:1           ((letrec ((loop (lambda...
```

#### display-environment-set!

```
(display-environment-set! display?) ;; procedure
```

!!! warning "Deprecation warning"
    This procedure is DEPRECATED and will be removed in a future version of
    Gambit. Use the parameter object `repl-display-environment?` instead.

This procedure sets a flag that controls the automatic display of the
environment by the REPL. If `display?` is true, the environment is displayed by
the REPL before the prompt. The default setting is not to display the
environment.

#### repl-display-environment?

```scheme
(repl-display-environment? display?) ;; procedure
```

The parameter object `repl-display-environment?` is bound to a boolean value
that controls the automatic display of the environment by the REPL. If
`display?` is true, the environment is displayed by the REPL before the prompt.
This is particularly useful in single-stepping mode. The default setting is not
to display the environment.

#### display-dynamic-environment?

```scheme
(display-dynamic-environment? display?) ;; procedure
```

The parameter object `display-dynamic-environment?` is bound to a boolean value
that controls wether the dynamic environment is displayed when the environment
is displayed. The default setting is not to display the dynamic environment.

#### pretty-print

```scheme
(pretty-print obj [port]) procedure
```

This procedure pretty-prints `obj` on the `port` port. If it is not specified,
`port` defaults to the current output-port.

For example:

```scheme
> (pretty-print
    (let* ((x '(1 2 3 4)) (y (list x x x))) (list y y y)))
(((1 2 3 4) (1 2 3 4) (1 2 3 4))
 ((1 2 3 4) (1 2 3 4) (1 2 3 4))
 ((1 2 3 4) (1 2 3 4) (1 2 3 4)))
```

#### pp

```scheme
(pp obj [port]) ;; procedure
```

This procedure pretty-prints `obj` on the `port` port. When `obj` is a procedure
created by the interpreter or a procedure created by code compiled with the
declaration `debug-source`, the procedure's source code is displayed. If it is
not specified, `port` defaults to the interaction channel (i.e. the output will
appear at the REPL).

For example:

```scheme
> (define (f g) (+ (time (g 100)) (time (g 1000))))
> (pp f)
(lambda (g)
  (+ (##time (lambda () (g 100)) '(g 100))
     (##time (lambda () (g 1000)) '(g 1000))))
```

#### gc-report-set!

```scheme
(gc-report-set! report?) ;; procedure
```

This procedure controls the generation of reports during garbage collections. If
the argument is true, a brief report of memory usage is generated after every
garbage collection. It contains: the time taken for this garbage collection, the
amount of memory allocated in megabytes since the program was started, the size
of the heap in megabytes, the heap memory in megabytes occupied by live data,
the proportion of the heap occupied by live data, and the number of bytes
occupied by movable and nonmovable objects.

