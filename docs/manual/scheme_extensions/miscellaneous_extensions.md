---
title: Miscellaneous extensions
---

#### subvector

```scheme
(subvector vector start end) ;; procedure
```

This procedure is the vector analog of the `substring` procedure. It returns a
newly allocated vector formed from the elements of the vector vector beginning
with index `start` (inclusive) and ending with index `end` (exclusive).

For example:

```scheme
> (subvector '#(a b c d e f) 3 5)
#(d e)
```

#### vector-copy

```scheme
(vector-copy vector [start [end]]) ;; procedure
```

This procedure is like the procedure `subvector` except the parameter `start`
defaults to `0` and the parameter `end` defaults to the length of the `vector`
vector. Note that the elements are not recursively copied.

For example:

```scheme
> (define v1 '#(a b c d e f))
> (define v2 (vector-copy v1))
> v2
#(a b c d e f)
> (eq? v1 v2)
#f
> (vector-copy v1 3)
#(d e f)
> (vector-copy v1 3 5)
#(d e)
```

#### vector-copy!

```scheme
(vector-copy! dest-vector dest-start vector [start [end]]) ;; procedure
```

This procedure mutates the vector dest-vector. It copies the elements of the
vector `vector` beginning with index `start` (inclusive) and ending with index
`end` (exclusive) to the vector `dest-vector` at index `dest-start`. The
parameters `start` and `end` default respectively to `0` and the length of the
vector `vector`. It is an error to copy more elements than will fit in the tail
of the vector `dest-vector` starting at index `dest-start`. Note that the
elements are not recursively copied.

For example:

```scheme
> (define v1 (vector 10 11 12 13 14 15))
> (define v2 (vector 20 21 22 23))
> (vector-copy! v1 1 v2)
> v1
#(10 20 21 22 23 15)
> (vector-copy! v1 1 v2 3)
> v1
#(10 23 21 22 23 15)
> (vector-copy! v1 1 v2 1 3)
> v1
#(10 21 22 22 23 15)
```

#### vector-append

```scheme
(vector-append vector ...) ;; procedure
```

This procedure is the vector analog of the `string-append` procedure. It returns
a newly allocated vector whose elements form the concatenation of the given
vectors.

For example:

```scheme
> (define v '#(1 2 3))
> (vector-append v v v)
#(1 2 3 1 2 3 1 2 3)
```

#### vector-concatenate

```scheme
(vector-concatenate lst [separator]) ;; procedure
```

This procedure returns a newly allocated vector whose elements form the
concatenation of all the vectors in the list `lst`. If the optional vector
`separator` argument is specified, it will be added between all the elements of
lst. Without the `separator` argument the result is the same as `(apply
vector-append lst)`.

For example:

```scheme
> (define v '#(1 2 3))
> (vector-concatenate (list v v v))
#(1 2 3 1 2 3 1 2 3)
> (vector-concatenate (list v v v) '#(88 99))
#(1 2 3 88 99 1 2 3 88 99 1 2 3)
```

#### subvector-fill!

```scheme
(subvector-fill! vector start end fill) ;; procedure
```

This procedure is like `vector-fill!`, but fills a selected part of the given
vector. It sets the elements of the vector `vector`, beginning with index
`start` (inclusive) and ending with index `end` (exclusive) to `fill`. The value
returned is unspecified.

For example:

```scheme
> (define v (vector 'a 'b 'c 'd 'e 'f))
> (subvector-fill! v 3 5 'x)
> v
#(a b c x x f)
```

#### subvector-move!

```scheme
(subvector-move! src-vector src-start src-end dst-vector dst-start) ;; procedure
```

This procedure replaces part of the contents of vector `dst-vector` with part of
the contents of vector `src-vector`. It copies elements from `src-vector`,
beginning with index `src-start` (inclusive) and ending with index `src-end`
(exclusive) to `dst-vector` beginning with index `dst-start` (inclusive). The
value returned is unspecified.

For example:

```scheme
> (define v1 '#(1 2 3 4 5 6))
> (define v2 (vector 'a 'b 'c 'd 'e 'f))
> (subvector-move! v1 3 5 v2 1)
> v2
#(a 4 5 d e f)
```

#### vector-shrink!

```scheme
(vector-shrink! vector k) ;; procedure
```

This procedure shortens the vector `vector` so that its new size is `k`. The
value returned is unspecified.

For example:

```scheme
> (define v (vector 'a 'b 'c 'd 'e 'f))
> v
#(a b c d e f)
> (vector-shrink! v 3)
> v
#(a b c)
```

#### vector-cas!

```scheme
(vector-cas! vector k new-value old-value) ;; procedure
```

The procedure `vector-cas!` performs an atomic compare-and-swap operation on the
element of vector `vector` at index `k`. If the element's value is `eq?` to
`old-value` then the element is changed to `new-value`, otherwise the value does
not change. Regardless what happened, the element's value prior to any change is
returned. It is thus possible to detect a change by an explicit `eq?` test of
the result.

For example:

```scheme
> (define v (vector 'a))
> (eq? 'foo (vector-cas! v 0 'b 'foo))
#f
> v
#(a)
> (eq? 'a (vector-cas! v 0 'b 'a))
#t
> v
#(b)
```

#### vector-inc!

```scheme
(vector-inc! vector k [step]) ;; procedure
```

The procedure `vector-inc!` performs an atomic incrementation on the element of
vector `vector` at index `k`, which must be a fixnum. The parameter `step` defaults to
`1` and it is the fixnum value that is added (with wraparound) to the element. The
procedure returns the value of the element prior to the incrementation.

For example:

```scheme
> (define v (vector 100))
> (vector-inc! v 0)
100
> (vector-inc! v 0)
101
> (vector-inc! v 0 5)
102
> v
#(107)
```

#### vector-set

```scheme
(vector-set vector k obj) ;; procedure
```

The procedure `vector-set` returns a new copy of the vector `vector` with the
element at index `k` replaced with `obj`.

For example:

```scheme
> (define v1 (vector 10 11 12 13))
> (define v2 (vector-set v1 2 99))
> v2
#(10 11 99 13)
> (eq? v1 v2)
#f
```

#### string-set

```scheme
(string-set string k char) ;; procedure
```

The procedure `string-set` returns a new copy of the string `string` with the
character at index `k` replaced with `char`.

For example:

```scheme
> (define s1 (string #\a #\b #\c #\d))
> (define s2 (string-set s1 2 #\.))
> s2
"ab.d"
> (eq? s1 s2)
#f
```

#### string-concatenate

```scheme
(string-concatenate lst [separator]) ;; procedure
```

This procedure returns a newly allocated string which is the concatenation of
all the strings in the list `lst`. If the optional string `separator` argument
is specified, it will be added between all the elements of `lst`. Without the
`separator` argument the result is the same as `(apply string-append lst)`.

For example:

```scheme
> (define s "abc")
> (string-concatenate (list s s s))
"abcabcabc"
> (string-concatenate (list s s s) ",")
"abc,abc,abc"
```

#### substring-fill!

```scheme
(substring-fill! string start end fill) ;; procedure
```

This procedure is like `string-fill!`, but fills a selected part of the given
`string`. It sets the elements of the string `string`, beginning with index
`start` (inclusive) and ending with index `end` (exclusive) to `fill`. The value
returned is unspecified.

For example:

```scheme
> (define s (string #\a #\b #\c #\d #\e #\f))
> (substring-fill! s 3 5 #\x)
> s
"abcxxf"
```

#### substring-move!

```scheme
(substring-move! src-string src-start src-end dst-string dst-start) ;; procedure
```

This procedure replaces part of the contents of string `dst-string` with part of
the contents of string `src-string`. It copies elements from `src-string`,
beginning with index `src-start` (inclusive) and ending with index `src-end`
(exclusive) to `dst-string` beginning with index `dst-start` (inclusive). The
value returned is unspecified.

For example:

```scheme
> (define s1 "123456")
> (define s2 (string #\a #\b #\c #\d #\e #\f))
> (substring-move! s1 3 5 s2 1)
> s2
"a45def"
```

#### string-shrink!

```scheme
(string-shrink! string k) ;; procedure
```

This procedure shortens the string `string` so that its new size is `k`. The
value returned is unspecified.

For example:

```scheme
> (define s (string #\a #\b #\c #\d #\e #\f))
> s
"abcdef"
> (string-shrink! s 3)
> s
"abc"
```

#### box
#### box?
#### unbox
#### set-box!

```scheme
(box obj) ;; procedure
(box? obj) ;; procedure
(unbox box) ;; procedure
(set-box! box obj) ;; procedure
```

These procedures implement the `box` data type. A box is a cell containing a
single mutable field. The lexical syntax of a box containing the object `obj` is
`#&obj` (see section [Box
syntax](/manual/lexical_syntax_and_readtables/box_syntax)).

The procedure `box` returns a new box object whose content is initialized to
`obj`. The procedure `box?` returns `#t` if `obj` is a box, and otherwise
returns `#f`. The procedure `unbox` returns the content of the box `box`. The
procedure `set-box!` changes the content of the box `box` to `obj`. The
procedure `set-box!` returns an unspecified value.

For example:

```scheme
> (define b (box 0))
> b
#&0
> (define (inc!) (set-box! b (+ (unbox b) 1)))
> (inc!)
> b
#&1
> (unbox b)
1
```

#### keyword?
#### keyword->string
#### string->keyword

```scheme
(keyword? obj) ;; procedure
(keyword->string keyword) ;; procedure
(string->keyword string) ;; procedure
```

These procedures implement the `keyword` data type. Keywords are similar to
symbols but are self evaluating and distinct from the `symbol` data type. The
lexical syntax of keywords is specified in [Keyword
syntax](/manual/lexical_syntax_and_readtables/keyword_syntax).

The procedure `keyword?` returns `#t` if `obj` is a keyword, and otherwise returns `#f`.
The procedure `keyword->string` returns the name of `keyword` as a string. The
procedure `string->keyword` returns the keyword whose name is `string`.

For example:

```scheme
> (keyword? 'color)
#f
> (keyword? color:)
#t
> (keyword->string color:)
"color"
> (string->keyword "color")
color:
```

#### gensym

```scheme
(gensym [prefix]) ;; procedure
```

This procedure returns a new uninterned symbol. Uninterned symbols are
guaranteed to be distinct from the symbols generated by the procedures read and
`string->symbol`. The symbol `prefix` is the prefix used to generate the new
symbol's name. If it is not specified, the prefix defaults to `g`.

For example:

```scheme
> (gensym)
#:g0
> (gensym)
#:g1
> (gensym 'star-trek-)
#:star-trek-2
```

#### string->uninterned-symbol
#### uninterned-symbol?

```scheme
(string->uninterned-symbol name [hash]) ;; procedure
(uninterned-symbol? obj) ;; procedure
```

The procedure `string->uninterned-symbol` returns a new uninterned symbol whose
name is `name` and hash is `hash`. The `name` must be a string and the `hash`
must be a nonnegative fixnum.

The procedure `uninterned-symbol?` returns `#t` when `obj` is a symbol that is
uninterned and `#f` otherwise.

For example:

```scheme
> (uninterned-symbol? (gensym))
#t
> (string->uninterned-symbol "foo")
#:foo:
> (uninterned-symbol? (string->uninterned-symbol "foo"))
#t
> (uninterned-symbol? 'hello)
#f
> (uninterned-symbol? 123)
#f
```

#### string->uninterned-keyword
#### uninterned-keyword?

```scheme
(string->uninterned-keyword name [hash]) ;; procedure
(uninterned-keyword? obj) ;; procedure
```

The procedure `string->uninterned-keyword` returns a new uninterned keyword whose
name is `name` and hash is `hash`. The `name` must be a string and the `hash` must be a
nonnegative fixnum.

The procedure `uninterned-keyword?` returns `#t` when `obj` is a keyword that is
uninterned and `#f` otherwise.

For example:

```scheme
> (string->uninterned-keyword "foo")
#:foo:
> (uninterned-keyword? (string->uninterned-keyword "foo"))
#t
> (uninterned-keyword? hello:)
#f
> (uninterned-keyword? 123)
#f
```

#### identity

```scheme
(identity obj) ;; procedure
```

This procedure returns `obj`.

#### void

```scheme
(void) ;; procedure
```

This procedure returns the `void` object. The read-eval-print loop prints
nothing when the result is the `void` object.

#### eval

```scheme
(eval expr [env]) ;; procedure
```

The first parameter is a datum representing an expression. The `eval` procedure
evaluates this expression in the global interaction environment and returns the
result. If present, the second parameter is ignored (it is provided for
compatibility with R5RS).

For example:

```scheme
> (eval '(+ 1 2))
3
> ((eval 'car) '(1 2))
1
> (eval '(define x 5))
> x
5
```

#### define-macro

```scheme
(define-macro (name define-formals) body) ;; special form
```

Define `name` as a macro special form which expands into `body`. This form can
only appear where a `define` form is acceptable. Macros are lexically scoped.
The scope of a local macro definition extends from the definition to the end of
the body of the surrounding binding construct. Macros defined at the top level
of a Scheme module are only visible in that module. To have access to the macro
definitions contained in a file, that file must be included either directly
using the `include` special form or indirectly with the `import` special form.
Macros which are visible from the REPL are also visible during the compilation
of Scheme source files.

For example:

```scheme
(define-macro (unless test . body)
  `(if ,test #f (begin ,@body)))

(define-macro (push var #!optional val)
  `(set! ,var (cons ,val ,var)))
```

To examine the code into which a macro expands you can use the compiler's
`-expansion` option or the `pp` procedure. For example:

```scheme
> (define-macro (push var #!optional val)
    `(set! ,var (cons ,val ,var)))
> (pp (lambda () (push stack 1) (push stack) (push stack 3)))
(lambda ()
  (set! stack (cons 1 stack))
  (set! stack (cons #f stack))
  (set! stack (cons 3 stack)))
```

#### define-syntax

```scheme
(define-syntax name expander) ;; special form
```

Define `name` as a macro special form whose expansion is specified by
`expander`. This form is available only when the runtime option `-:s` is used.
This option causes the loading of the `~~lib/syntax-case` support library, which
is the Hieb and Dybvig portable `syntax-case` implementation which has been
ported to the Gambit interpreter and compiler. Note that this implementation of
`syntax-case` does not support special forms that are specific to Gambit.

For example:

```scheme
$ gsi -:s
Gambit v4.9.4

> (define-syntax unless
    (syntax-rules ()
      ((unless test body ...)
       (if test #f (begin body ...)))))
> (let ((test 111)) (unless (= 1 2) (list test test)))
(111 111)
> (pp (lambda () (let ((test 111)) (unless (= 1 2) (list test test)))))
(lambda () ((lambda (%%test14) (if (= 1 2) #f (list %%test14 %%test14))) 111))
> (unless #f (pp xxx))
*** ERROR IN (console)@7.16 -- Unbound variable: xxx
```

#### compilation-target

```scheme
(compilation-target) ;; procedure
```

This procedure can only be executed during the phase of the Scheme code's
processing (compilation) that corresponds to macro expansion. Calls to this
procedure are typically contained in macro definitions but they can also be
contained in procedures that are called from a macro definition's body directly
or indirectly.

The result returned by the `compilation-target` procedure gives an indication of
the target language of the compilation. This can be used to write macros that
depend on the type of compilation and the target language.

When the result is the symbol `T` the macro expansion is in the context of
compiling to the target language `T`, e.g. `C`, `js`, etc. When the result is a
single element list `(T)` the macro expansion is for the interpreter which
itself was compiled for the target language T, e.g. `(C)`, `(js)`, etc.

For example:

```scheme
$ cat ct.scm
(define (level-0)
  (string-append "0: " (object->string (compilation-target))))

(define-macro (test)

  (define (level-1)
    (string-append "1: " (object->string (compilation-target))))

  (define-macro (level-2)
    (string-append "2: " (object->string (compilation-target))))

  `(list ,(level-1) ,(level-2)))

(pp (test))

(pp (level-0)) ;; run time exception
$ gsi ct.scm
("1: (C)" "2: (C)")
*** ERROR IN level-0, "ct.scm"@2.40 -- Not in compilation context
(compilation-target)
$ gsc -target js -exe ct.scm
$ ./ct
("1: js" "2: (C)")
*** ERROR IN level-0 -- Not in compilation context
(compilation-target)
```

Regardless of whether `ct.scm` is being processed by the interpreter or the
compiler, the body of the `level-0` procedure is not in a compilation context
and in the body of the `level-2` macro the compilation target is `(C)`
indicating that the macro expansion is being done for interpretation.

During the execution of the `level-1` procedure, the compilation target will
correspond to what is processing `ct.scm` (interpreter or compiler).

Note that the compilation target can also be tested by the `cond-expand` special
form.

#### cond-expand

```scheme
(cond-expand ce-clause ...) ;; special form
```

The `cond-expand` expression type provides a way to statically expand different
expressions depending on the presence or absence of a set of features. A
`ce-clause` takes the following form:

```scheme
(feature-requirement expression ...)
```

The last clause can be an "else clause" which has the form

```scheme
(else expression)
```

A `feature-requirement` takes one of the following forms:

- `feature-identifier`
- `(library library-name)`
- `(and feature-requirement ...)`
- `(or feature-requirement ...)`
- `(not feature-requirement)`
- `(compilation-target target ...)`

The runtime system maintains a list of feature identifiers which are present, as
well as a list of libraries which can be imported. The value of a
`feature-requirement` is determined by replacing each `feature-identifier` and
`(library library-name)` on the runtime system's lists with `#t`. Similarly,
`#t` replaces each `(compilation-target target ...)` for which one of the
`target` matches the expansion time value of `(compilation-target)`, with a
`target` of `(_)` matching any single element list (i.e. the interpreter). All
other feature-identifier, `(library library-name)`, and `(compilation-target
target ...)` are replaced with `#f`. The resulting expression is then evaluated
as a Scheme boolean expression under the normal interpretation of `and`, `or`,
and `not`.

A `cond-expand` is then expanded by evaluating the `feature-requirements` of
successive `ce-clauses` in order until one of them returns `#t`. When a true
clause is found, the corresponding expressions are expanded to a `begin`, and
the remaining clauses are ignored. If none of the `feature-requirements`
evaluate to `#t`, then if there is an `else` clause, its expressions are
included. Otherwise, an expansion time error is raised. Unlike `cond`,
`cond-expand` does not depend on the value of any variables.

The feature identifier `gambit` is always true when the `cond-expand` is
expanded by the Gambit interpreter or compiler.

For example:

```scheme
> (cond-expand (foobar 111) (gambit 222) (else 333))
222
> (cond-expand ((compilation-target js) 111) (else 222))
222
> (cond-expand ((compilation-target (_)) 111) (else 222))
111
```

#### define-cond-expand-feature

```scheme
(define-cond-expand-feature feature-identifier ...) ;; special form
```

The `define-cond-expand-feature` form can be used to add the feature identifiers
`feature-identifier ...` to the list of features maintained by the runtime
system. These features are usable for the expansion of following `cond-expand`
forms in the same file of source code, and the processing of other files and
REPL interactions.

For example:

```scheme
> (cond-expand (foobar 111) (gambit 222) (else 333))
222
> (define-cond-expand-feature foobar)
> (cond-expand (foobar 111) (gambit 222) (else 333))
111
```

#### declare

```scheme
(declare declaration…) ;; special form
```

This form introduces declarations to be used by the compiler (currently the
interpreter ignores the declarations). This form can only appear where a `define`
form is acceptable. Declarations are lexically scoped in the same way as macros.
The following declarations are accepted by the compiler:

`(dialect)`
: Use the given dialect's semantics. `dialect` can be: `ieee-scheme`,
  `r4rs-scheme`, `r5rs-scheme` or `gambit-scheme`.

`(strategy)`
: Select block compilation or separate compilation. In block compilation, the
  compiler assumes that global variables defined in the current file that are
  not mutated in the file will never be mutated. strategy can be: `block` or
  `separate`.

`([not] inline)`
: Allow (or disallow) inlining of user procedures.

`([not] inline-primitives primitive ...)`
: The given primitives `primitive ...` should (or should not) be inlined if possible (all
  primitives if none specified).

`(inlining-limit n)`
: Select the degree to which the compiler inlines user procedures. `n` is the
  upper-bound, in percent, on code expansion that will result from inlining.
  Thus, a value of `300` indicates that the size of the program will not grow by
  more than `300` percent (i.e. it will be at most 4 times the size of the
  original). A value of `0` disables inlining. The size of a program is the
  total number of subexpressions it contains (i.e. the size of an expression is
  one plus the size of its immediate subexpressions). The following conditions
  must hold for a procedure to be inlined: inlining the procedure must not cause
  the size of the call site to grow more than specified by the inlining limit,
  the site of definition (the `define` or `lambda`) and the call site must be
  declared as `(inline)`, and the compiler must be able to find the definition
  of the procedure referred to at the call site (if the procedure is bound to a
  global variable, the definition site must have a `(block)` declaration). Note
  that inlining usually causes much less code expansion than specified by the
  inlining limit (an expansion around 10% is common for `n=370`).

`(allocation-limit n)`
: Indicate the maximum size of objects allocated with make-vector, make-string, make-u8vector, etc. Knowing the maximum size allows the compiler to inline calls to these allocators for small allocations. This is only supported by the C target and only up to a size that is allowed for movable objects (typically on the order of 1-2 KB). When n is an exact nonnegative integer it is the upper-bound on the number of elements of the allocated objects. When n is #t a dynamic test of the size is done. When n is #f the allocation operation is not inlined.

`([not] lambda-lift)`
: Lambda-lift (or don't lambda-lift) locally defined procedures.

`([not] constant-fold)`
: Allow (or disallow) constant-folding of primitive procedures.

`([not] standard-bindings var ...)`
: The given global variables `var ...` are known (or not known) to be equal to
  the value defined for them in the dialect (all variables defined in the
  standard if none specified).

`([not] extended-bindings var ...)`
: The given global variables `var ...` are known (or not known) to be equal to
  the value defined for them in the runtime system (all variables defined in the
  runtime if none specified).

`([not] run-time-bindings var ...)`
: The given global variables `var ...` will be tested at run time to see if they
  are equal to the value defined for them in the runtime system (all variables
  defined in the runtime if none specified).

`([not] safe)`
: Generate (or don't generate) code that will prevent fatal errors at run time.
  Note that in `safe` mode certain semantic errors will not be checked as long
  as they can't crash the system. For example the primitive `char=?` may
  disregard the type of its arguments in `safe` as well as `not safe` mode.

`([not] interrupts-enabled)`
: Generate (or don't generate) interrupt checks. Interrupt checks are used to
  detect user interrupts and also to check for stack overflows. Interrupt
  checking should not be turned off casually.

`([not] poll-on-return)`
: Generate (or don't generate) interrupt checks on procedure returns (when
  interrupt checking is enabled). This declaration has no effect on the behavior
  of interrupt checking on procedure calls, which is needed to guarantee that
  stack overflows are handled properly.

`([not] debug)`
: Enable (or disable) the generation of debugging information. The kind of
  debugging information that is generated depends on the declarations
  `debug-location`, `debug-source`, and `debug-environments`. If any of the
  command line options `-debug`, `-debug-location`, `-debug-source` and
  `-debug-environments` are present, the `debug` declaration is initially
  enabled, otherwise it is initially disabled. When all kinds of debugging
  information are generated there is a substantial increase in the C compilation
  time and the size of the generated code. When compiling a 3000 line Scheme
  file it was observed that the total compilation time is 500% longer and the
  executable code is 150% bigger.

`([not] debug-location)`
: Select (or deselect) source code location debugging information. When this
  declaration and the `debug` declaration are in effect, run time error messages
  indicate the location of the error in the source code file. If any of the
  command line options `-debug-source` and `-debug-environments` are present and
  `-debug-location` is absent, the `debug-location` declaration is initially
  disabled, otherwise it is initially enabled. When compiling a 3000 line Scheme
  file it was observed that the total compilation time is 200% longer and the
  executable code is 60% bigger.

`([not] debug-source)`
: Select (or deselect) source code debugging information. When this declaration
  and the `debug` declaration are in effect, run time error messages indicate
  the source code, the backtraces are more precise, and the pp procedure will
  display the source code of compiled procedures. If any of the command line
  options `-debug-location` and `-debug-environments` are present and
  `-debug-source` is absent, the `debug-source` declaration is initially
  disabled, otherwise it is initially enabled. When compiling a 3000 line Scheme
  file it was observed that the total compilation time is 90% longer and the
  executable code is 90% bigger.

`([not] debug-environments)`
: Select (or deselect) environment debugging information. When this declaration
  and the `debug` declaration are in effect, the debugger will have access to
  the environments of the continuations. In other words the local variables
  defined in compiled procedures (and not optimized away by the compiler) will
  be shown by the `,e` REPL command. If any of the command line options
  `-debug-location` and `-debug-source` are present and `-debug-environments` is
  absent, the ‘debug-environments’ declaration is initially disabled, otherwise
  it is initially enabled. When compiling a 3000 line Scheme file it was
  observed that the total compilation time is 70% longer and the executable code
  is 40% bigger.

`([not] proper-tail-calls)`
: Generate (or don't generate) proper tail calls. When proper tail calls are
  turned off, tail calls are handled like non-tail calls, that is a continuation
  frame will be created for all calls regardless of their kind. This is useful
  for debugging because the caller of a procedure will be visible in the
  backtrace produced by the REPL's `,b` command even when the call is a tail
  call. Be advised that this does cause stack space to be consumed for tail
  calls which may cause the stack to overflow when performing long iterations
  with tail calls (whether they are expressed with a `letrec`, named `let`,
  `do`, or other form).

`([not] generative-lambda)`
: Force (or don't force) the creation of fresh closures when evaluating
  lambda-expressions. A fresh closure is always created when a lambda-expression
  has at least one free variable (that has not been eliminated by dead-code
  elimination or other compiler optimization) or when the `generative-lambda`
  declaration is turned on. When a lambda-expression has no free variables and
  the `generative-lambda` declaration is turned off, the value of the
  lambda-expression may be the same procedure (in the sense of `eq?`).

`([not] optimize-dead-local-variables)`
: Remove (or preserve) the dead local variables in the environment. Preserving
  the dead local variables is useful for debugging because continuations will
  contain the dead variables. Thus, if the code is also compiled with the
  declaration `debug-environments` the `,e`, `,ed`, `,be`, and `,bed` REPL
  commands will display the dead variables. On the other hand, preserving the
  dead local variables may change the space complexity of the program (i.e. some
  of the data that would normally be reclaimed by the garbage collector will not
  be). Note that due to other compiler optimizations some dead local variables
  may be removed regardless of this declaration.

`([not] optimize-dead-definitions var ...)`
: Remove (or preserve) the dead toplevel definitions of the given global
  variables `var ...` (all global variables if none specified). A toplevel
  definition is dead if it is not referenced by toplevel expressions of the
  program or toplevel definitions that aren't dead (regardless of the evaluation
  of its expression causing a side-effect). When a module is separately compiled
  and some of its definitions are only used by other modules, this declaration
  must be used with care to keep definitions that are used by other modules, for
  example if foo is referenced in another module the following declaration
  should be used: `(declare (not optimize-dead-definitions foo))`.

`(number-type primitive ...)`
: Numeric arguments and result of the specified primitives `primitive ...` are
  known to be of the given type (all primitives if none specified).
  `number-type` can be: `generic`, `fixnum`, or `flonum`.

`(mostly-number-type primitive ...)`
: Numeric arguments and result of the specified primitives `primitive ...` are
  expected to be most often of the given type (all primitives if none
  specified). `mostly-number-type` can be: `mostly-generic`, `mostly-fixnum`,
  `mostly-fixnum-flonum`, `mostly-flonum`, or `mostly-flonum-fixnum`.

The default declarations used by the compiler are equivalent to:

```scheme
(declare
  (gambit-scheme)
  (separate)
  (inline)
  (inline-primitives)
  (inlining-limit 370)
  (allocation-limit #t)
  (constant-fold)
  (lambda-lift)
  (not standard-bindings)
  (not extended-bindings)
  (run-time-bindings)
  (safe)
  (interrupts-enabled)
  (not poll-on-return)
  (not debug)           ;; depends on debugging command line options
  (debug-location)      ;; depends on debugging command line options
  (debug-source)        ;; depends on debugging command line options
  (debug-environments)  ;; depends on debugging command line options
  (proper-tail-calls)
  (not generative-lambda)
  (optimize-dead-local-variables)
  (not optimize-dead-definitions)
  (generic)
  (mostly-fixnum-flonum)
)
```

These declarations are compatible with the semantics of R5RS Scheme and includes
a few procedures from R6RS (mainly fixnum specific and flonum specific
procedures). Typically used declarations that enhance performance, at the cost
of violating the R5RS Scheme semantics, are: `(standard-bindings)`, `(block)`,
`(not safe)` and `(fixnum)`.

#### continuation?
#### continuation-capture
#### continuation-graft
#### continuation-return

```scheme
(continuation? obj) ;; procedure
(continuation-capture proc) ;; procedure
(continuation-graft cont proc obj ...) ;; procedure
(continuation-return cont obj ...) ;; procedure
```

These procedures provide access to internal first-class continuations which are
represented using continuation objects distinct from procedures.

The procedure `continuation?` returns `#t` when `obj` is a continuation object and `#f`
otherwise.

The procedure `continuation-capture` is similar to the `call/cc` procedure but
it represents the continuation with a `continuation` object. The `proc`
parameter must be a procedure accepting a single argument. The procedure
`continuation-capture` reifies its continuation and calls `proc` with the
corresponding `continuation` object as its sole argument. Like for `call/cc`,
the implicit continuation of the call to `proc` is the implicit continuation of
the call to `continuation-capture`.

The procedure `continuation-graft` performs a procedure call to the procedure
`proc` with arguments `obj ...` and the implicit continuation corresponding to
the continuation object `cont`. The current continuation of the call to
procedure `continuation-graft` is ignored.

The procedure `continuation-return` invokes the implicit continuation
corresponding to the continuation object `cont` with the result(s) `obj ...`.
This procedure can be easily defined in terms of `continuation-graft`:

```scheme
(define (continuation-return cont . objs)
  (continuation-graft cont apply values objs))
```

For example:

```scheme
> (define x #f)
> (define p (make-parameter 11))
> (pp (parameterize ((p 22))
        (cons 33 (continuation-capture
                  (lambda (c) (set! x c) 44)))))
(33 . 44)
> x
#<continuation #2>
> (continuation-return x 55)
(33 . 55)
> (continuation-graft x (lambda () (expt 2 10)))
(33 . 1024)
> (continuation-graft x expt 2 10)
(33 . 1024)
> (continuation-graft x (lambda () (p)))
(33 . 22)
> (define (map-sqrt1 lst)
    (call/cc
     (lambda (k)
       (map (lambda (x)
              (if (< x 0)
                  (k 'error)
                  (sqrt x)))
            lst))))
> (map-sqrt1 '(1 4 9))
(1 2 3)
> (map-sqrt1 '(1 -1 9))
error
> (define (map-sqrt2 lst)
    (continuation-capture
     (lambda (c)
       (map (lambda (x)
              (if (< x 0)
                  (continuation-return c 'error)
                  (sqrt x)))
            lst))))
> (map-sqrt2 '(1 4 9))
(1 2 3)
> (map-sqrt2 '(1 -1 9))
error
```

#### display-exception
#### display-exception-in-context
#### display-procedure-environment
#### display-continuation-environment
#### display-continuation-dynamic-environment
#### display-continuation-backtrace

```scheme
(display-exception exc [port]) ;; procedure
(display-exception-in-context exc cont [port]) ;; procedure
(display-procedure-environment proc [port]) ;; procedure
(display-continuation-environment cont [port]) ;; procedure
(display-continuation-dynamic-environment cont [port]) ;; procedure
(display-continuation-backtrace cont [port
                                     [display-env?
                                     [all-frames?
                                     [max-head
                                     [max-tail
                                     [depth]]]]]]) ;; procedure
```

The procedure `display-continuation-backtrace` displays the frames of the
continuation corresponding to the continuation object `cont` on the port `port`.
If it is not specified, `port` defaults to the current output-port. The frames
are displayed in the same format as the REPL's `,b` command.

The parameter `display-env?`, which defaults to `#f`, controls if the frames are
displayed with its environment (the variables accessible and their bindings).

The parameter `all-frames?`, which defaults to `#f`, controls which frames are
displayed. Some frames of ancillary importance, such as internal frames created
by the interpreter, are not displayed when `all-frames?` is `#f`. Otherwise all
frames are displayed.

The parameters `max-head` and `max-tail`, which default to `10` and `4`
respectively, control how many frames are displayed at the head and tail of the
continuation.

The parameter `depth`, which defaults to `0`, causes the frame numbers to be
offset by that value.

For example:

```scheme
> (define x #f)
> (define (fib n)
    (if (< n 2)
        (continuation-capture
         (lambda (c) (set! x c) 1))
        (+ (fib (- n 1))
           (fib (- n 2)))))
> (fib 10)
89
> (display-continuation-backtrace x)
0  fib             (console)@7:12     (fib (- n 2))
1  fib             (console)@7:12     (fib (- n 2))
2  fib             (console)@7:12     (fib (- n 2))
3  fib             (console)@7:12     (fib (- n 2))
4  fib             (console)@7:12     (fib (- n 2))
5  (interaction)   (console)@8:1      (fib 10)
#f
> (display-continuation-backtrace x (current-output-port) #f #t)
0  fib             (console)@7:12     (fib (- n 2))
1  fib             (console)@6:9      (+ (fib (- n 1)) (fib (- ...
2  fib             (console)@7:12     (fib (- n 2))
3  fib             (console)@6:9      (+ (fib (- n 1)) (fib (- ...
4  fib             (console)@7:12     (fib (- n 2))
5  fib             (console)@6:9      (+ (fib (- n 1)) (fib (- ...
6  fib             (console)@7:12     (fib (- n 2))
7  fib             (console)@6:9      (+ (fib (- n 1)) (fib (- ...
8  fib             (console)@7:12     (fib (- n 2))
9  fib             (console)@6:9      (+ (fib (- n 1)) (fib (- ...
...
13 ##with-no-result-expected-toplevel
14 ##repl-debug
15 ##repl-debug-main
16 ##kernel-handlers
#f
> (display-continuation-backtrace x (current-output-port) #t #f)
0  fib             (console)@7:12     (fib (- n 2))
        n = 2
1  fib             (console)@7:12     (fib (- n 2))
        n = 4
2  fib             (console)@7:12     (fib (- n 2))
        n = 6
3  fib             (console)@7:12     (fib (- n 2))
        n = 8
4  fib             (console)@7:12     (fib (- n 2))
        n = 10
5  (interaction)   (console)@8:1      (fib 10)
#f
> (display-continuation-backtrace x (current-output-port) #f #f 2 1 100)
100 fib            (console)@7:12     (fib (- n 2))
101 fib            (console)@7:12     (fib (- n 2))
...
105 (interaction)  (console)@8:1      (fib 10)
#f
```
