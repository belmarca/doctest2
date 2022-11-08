---
title: Extensions to standard special forms
---

#### lambda
#### define

```scheme
(lambda lambda-formals body) ;; special form
(define (variable define-formals) body) ;; special form
```

```
lambda-formals = ( formal-argument-list ) | r4rs-lambda-formals
define-formals = formal-argument-list | r4rs-define-formals
formal-argument-list = dsssl-formal-argument-list | rest-at-end-formal-argument-list
dsssl-formal-argument-list = reqs opts rest keys
rest-at-end-formal-argument-list = reqs opts keys rest | reqs opts keys . rest-formal-argument
reqs = required-formal-argument*
required-formal-argument = variable
opts = #!optional optional-formal-argument* | empty
optional-formal-argument = variable | ( variable initializer )
rest = #!rest rest-formal-argument | empty
rest-formal-argument = variable
keys = #!key keyword-formal-argument* | empty
keyword-formal-argument = variable | ( variable initializer )
initializer = expression
r4rs-lambda-formals = ( variable* ) | ( variable+ . variable ) | variable
r4rs-define-formals = variable* | variable* . variable
```

These forms are extended versions of the `lambda` and `define` special forms of
standard Scheme. They allow the use of optional formal arguments, either
positional or named, and support the syntax and semantics of the DSSSL standard.

When the procedure introduced by a `lambda` (or `define`) is applied to a list of
actual arguments, the formal and actual arguments are processed as specified in
the R4RS if the `lambda-formals` (or `define-formals`) is a `r4rs-lambda-formals` (or
`r4rs-define-formals`).

If the `formal-argument-list` matches `dsssl-formal-argument-list` or
`extended-formal-argument-list` they are processed as follows:

1. Variables in `required-formal-arguments` are bound to successive actual
   arguments starting with the first actual argument. It shall be an error if
   there are fewer actual arguments than `required-formal-arguments`.
2. Next variables in `optional-formal-arguments` are bound to remaining actual
   arguments. If there are fewer remaining actual arguments than
   `optional-formal-arguments`, then the variables are bound to the result of
   evaluating `initializer`, if one was specified, and otherwise to `#f`. The
   `initializer` is evaluated in an environment in which all previous formal
   arguments have been bound.
3. If `#!key` does not appear in the `formal-argument-list` and there is no
   `rest-formal-argument` then it shall be an error if there are any remaining
   actual arguments.
4. If `#!key` does not appear in the `formal-argument-list` and there is a
   `rest-formal-argument` then the `rest-formal-argument` is bound to a list of
   all remaining actual arguments.
5. If `#!key` appears in the `formal-argument-list` and there is no
   `rest-formal-argument` then there shall be an even number of remaining actual
   arguments. These are interpreted as a series of pairs, where the first member
   of each pair is a keyword specifying the argument name, and the second is the
   corresponding value. It shall be an error if the first member of a pair is
   not a keyword. It shall be an error if the argument name is not the same as a
   variable in a `keyword-formal-argument`. If the same argument name occurs
   more than once in the list of actual arguments, then the first value is used.
   If there is no actual argument for a particular `keyword-formal-argument`,
   then the variable is bound to the result of evaluating `initializer` if one
   was specified, and otherwise to `#f`. The `initializer` is evaluated in an
   environment in which all previous formal arguments have been bound.
6. If `#!key` appears in the `formal-argument-list` and there is a
   `rest-formal-argument` before the `#!key` then there may be an even or odd
   number of remaining actual arguments and the `rest-formal-argument` is bound
   to a list of all remaining actual arguments. Then, these remaining actual
   arguments are scanned from left to right in pairs, stopping at the first pair
   whose first element is not a keyword. Each pair whose first element is a
   keyword matching the name of a `keyword-formal-argument` gives the value
   (i.e. the second element of the pair) of the corresponding formal argument.
   If the same argument name occurs more than once in the list of actual
   arguments, then the first value is used. If there is no actual argument for a
   particular `keyword-formal-argument`, then the variable is bound to the
   result of evaluating `initializer` if one was specified, and otherwise to
   `#f`. The initializer is evaluated in an environment in which all previous
   formal arguments have been bound.
7. If `#!key` appears in the `formal-argument-list` and there is a
   `rest-formal-argument` after the `#!key` then there may be an even or odd
   number of remaining actual arguments. The remaining actual arguments are
   scanned from left to right in pairs, stopping at the first pair whose first
   element is not a keyword. Each pair shall have as its first element a keyword
   matching the name of a `keyword-formal-argument`; the second element gives
   the value of the corresponding formal argument. If the same argument name
   occurs more than once in the list of actual arguments, then the first value
   is used. If there is no actual argument for a particular
   `keyword-formal-argument`, then the variable is bound to the result of
   evaluating initializer if one was specified, and otherwise to `#f`. The
   `initializer` is evaluated in an environment in which all previous formal
   arguments have been bound. Finally, the `rest-formal-argument` is bound to
   the list of the actual arguments that were not scanned (i.e. after the last
   keyword/value pair).

In all cases it is an error for a variable to appear more than once in a
`formal-argument-list`.

Note that this specification is compatible with the DSSSL language standard
(i.e. a correct DSSSL program will have the same semantics when run with
Gambit).

It is unspecified whether variables receive their value by binding or by
assignment. Currently the compiler and interpreter use different methods, which
can lead to different semantics if `call-with-current-continuation` is used in an
initializer. Note that this is irrelevant for DSSSL programs because
`call-with-current-continuation` does not exist in DSSSL.

For example:

```scheme
> ((lambda (#!rest x) x) 1 2 3)
(1 2 3)
> (define (f a #!optional b) (list a b))
> (define (g a #!optional (b a) #!key (k (* a b))) (list a b k))
> (define (h1 a #!rest r #!key k) (list a k r))
> (define (h2 a #!key k #!rest r) (list a k r))
> (f 1)
(1 #f)
> (f 1 2)
(1 2)
> (g 3)
(3 3 9)
> (g 3 4)
(3 4 12)
> (g 3 4 k: 5)
(3 4 5)
> (g 3 4 k: 5 k: 6)
(3 4 5)
> (h1 7)
(7 #f ())
> (h1 7 k: 8 9)
(7 8 (k: 8 9))
> (h1 7 k: 8 z: 9)
(7 8 (k: 8 z: 9))
> (h2 7)
(7 #f ())
> (h2 7 k: 8 9)
(7 8 (9))
> (h2 7 k: 8 z: 9)
*** ERROR IN (console)@17.1 -- Unknown keyword argument passed to procedure
(h2 7 k: 8 z: 9)
```
