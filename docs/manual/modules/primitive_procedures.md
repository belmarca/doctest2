---
title: Primitive procedures
---

# Primitive procedures

!!! bug "This documentation needs work, cleanup or clarification."

Identifiers with a `##` prefix are not valid identifiers according to RnRS. This
means that code containing `##` prefixed identifiers cannot be processed by and
shared with other Scheme implementations. They are hard to read by people that
aren't used to that extension. Moreover the code lacks abstraction and safety
because using `##car` rather than `car` has a specific meaning: avoiding type
checks. Consequently it is hard to "turn on" safe execution of the code when it
needs to be debugged. Many parts of the runtime library are expressed at a low
level of abstraction (with `##` prefixed identifiers) even when not required.

For those reasons `##` prefixed identifiers should be used sparingly in new code,
and existing code should gradually be rewritten to avoid them. The primitive
operations which are used to build higher-level operations are all defined as
procedures with a `##` prefix.

#### primitive
#### standard
#### define-procedure
#### define-primitive

The file `~~lib/_gambit#.scm` contains the definition of the `primitive` macro whose
purpose is to abstract from the `##` prefix. The call `(primitive foo)` is
equivalent to `##foo` and `(primitive (foo a b))` is equivalent to `(##foo a b)`. The
file `~~lib/_gambit#.scm` also contains the definition of the standard macro whose
purpose is similar, but forces the use of the empty namespace. The call
`(standard +)` is equivalent to `(##let () (##namespace ("" +)) +)` and `(standard (+
a b))` is equivalent to `((##let () (##namespace ("" +)) +) a b)`. Code that uses
the `primitive` and `standard` macros can be ported to other Scheme implementations
by defining implementation specific `primitive` and `standard` macros that implement
the appropriate mapping for that implementation.

The file `~~lib/_gambit#.scm` also contains definitions for the
`define-procedure` and `define-primitive` macros. The `primitive` and `standard`
macros work in tandem with the `define-procedure` and `define-primitive` macros
and the `~~lib/gambit/prim/prim#.scm` file and `(gambit prim)` library. The file
`~~lib/gambit/prim/prim#.scm` contains namespace declarations that map
operations exported by the runtime library without a `##` prefix to their `##`
prefixed names if this preserves the meaning of the operation but possibly (and
usually) with no type checking. The `(gambit prim)` library is similar but in
the form of a R7RS library. For example the following code:

```scheme
(include "~~lib/gambit/prim/prim#.scm")
(define (foo x) (square (car x)))
(println (foo (bar 0.5)))
(pp "hello")
```

is equivalent to this code:

```scheme
(##define (foo x) (##square (##car x)))
(##println (foo (bar 0.5)))
(##unimplemented#pp "hello")
```

The namespace declarations in `~~lib/gambit/prim/prim#.scm` have caused a
mapping of square to `##square`, `car` to `##car` and `println` to `##println`
because those primitives perform the same operations (when the code has no
errors). Note that `foo` and `bar` have remained the same, because they are not
procedures exported by the runtime library, and `pp` has been mapped to
`##unimplemented#pp` because `pp` is a procedure exported by the runtime library
but `##pp` is not defined. Having `unimplemented` in the name helps catch
situations where the programmer expected a primitive operation to exist but this
isn't the case.

The `define-procedure` macro does two things. It supports type annotations in
the parameter list and it inserts a `(include "~~lib/gambit/prim/prim#.scm")` in
the body so that primitive operations can be used without the `##` prefix. Type
checking and automatic forcing of promise arguments are also added implicitly.
The macro `define-primitive` is similar, but the procedure defined is implicitly
prefixed with `##`.

So all of these things work together to abstract away from the concept of
primitive operations. Primitives are implemented using procedures with a `##`
prefix, but other Scheme implementations could do it differently.

Finally, there's the `(declare-safe-define-procedure <bool>)` macro that can be
used to enable/disable the mapping of names exported by the runtime library to
the corresponding primitives. This is useful to enable type checks in the code.
For example the following definition:

```scheme
(define-procedure (foo (x vector))
  (vector-ref x 5))
```

which expands to

```scheme
(define (foo x)
  (macro-check-vector x '(1 . x) (foo x)
    (##vector-ref x 5)))
```

which expands to

```scheme
(define (foo x)
    (if (##vector? x)
        (##vector-ref x 5)
        (##fail-check-vector '(1 . x) foo x)))
```

If the code is in the scope of a `(declare-safe-define-procedure #t)` then it is
`vector-ref` that is called instead of `##vector-ref` which will both check that
`x` is a vector (redundantly) and that the index is in range. However, the use
of `##vector-ref` can be forced by writing the code with an explicit use of the
`primitive` macro:

```scheme
(define-procedure (foo (x vector))
  (primitive (vector-ref x 5)))
```

The expectation is that the `primitive` special form will be used sparingly.
Searching the source code for the pattern "(primitive" is a good way to find
potentially unsafe code.


## Type specifiers

Here is a list of the available type specifiers for a `define-procedure`
parameter `x` and the associated constraint on the value of `x`.

Note that there is no direct way for checking for a "list" or "list of elements
of type T". A procedure taking a list parameter will likely iterate on the
list's pairs going from `cdr` to `cdr` until a non-pair is found. Then a check
for the empty list with `(macro-check-proper-list-null lst <arg-id>
(<procedure-name> <args>...) <body>)` will check that the parameter is a proper
list (i.e. that it ends with the empty list).

### Basic types (other than numbers)
`boolean`
: `x` is a boolean

`char`
: `x` is a character

`pair`
: `x` is a pair

`procedure`
: `x` is a procedure

`string`
: `x` is a string

`symbol`
: `x` is a symbol

`vector`
: `x` is a vector

### Numbers

`number`
: `x` is a number (possibly complex, rational, etc)

`real`
: `x` is a real number (any number except complex)

`fixnum`
: `x` is a fixnum and `-2^(W-3) <= x <= 2^(W-3) - 1`

`(fixnum-range lo hi)`
: `x` is a fixnum and `lo <= x < hi`

`(fixnum-range-incl lo hi)`
: `x` is a fixnum and `lo <= x <= hi`

`index`
: `x` is a fixnum and `0 <= x`

`(index-range lo hi)`
: `x` is a fixnum and `0 <= lo <= x < hi`

`(index-range-incl lo hi)`
: `x` is a fixnum and `0 <= lo <= x <= hi`

`exact-signed-int8`
: `x` is an exact integer, `-128 <= x <= 127`

`exact-signed-int16`
: `x` is an exact integer `n`, `-32768 <= x <= 32767`

`exact-signed-int32`
: `x` is an exact integer `n`, `-2^31 <= x <= 2^31 - 1`

`exact-signed-int64`
: `x` is an exact integer `n`, `-2^63 <= x <= 2^63 - 1`

`exact-unsigned-int8`
: `x` is an exact integer `n`, `0 <= x <= 255`

`exact-unsigned-int16`
: `x` is an exact integer `n`, `0 <= x <= 65535`

`exact-unsigned-int32`
: `x` is an exact integer `n`, `0 <= x <= 2^32 - 1`

`exact-unsigned-int64`
: `x` is an exact integer `n`, `0 <= x <= 2^64 - 1`

`flonum`
: `x` is a flonum, exception mentions `FLONUM`

`inexact-real`
: `x` is a flonum, exception mentions `Inexact REAL`

`inexact-real-list`
: `x` is a flonum, exception mentions `Inexact REAL LIST`

### Time types

`time`
: `x` is a time object

`absrel-time`
: `x` is a real or a time object

`absrel-time-or-false`
: `x` is `#f` or a real or a time object

### Ports

`port`
: `x` is a port (input, output, or input-output)

`input-port`
: `x` is an input port

`output-port`
: `x` is an output port

`object-input-port`
: `x` is an object input port

`object-output-port`
: `x` is an object output port

`vector-input-port`
: `x` is a vector input port

`vector-output-port`
: `x` is a vector output port

`character-input-port`
: `x` is a character input port

`character-output-port`
: `x` is a character output port

`string-input-port`
: `x` is a string input port

`string-output-port`
: `x` is a string output port

`byte-port`
: `x` is a byte port (input, output, or input-output)

`byte-input-port`
: `x` is a byte input port

`byte-output-port`
: `x` is a byte output port

`u8vector-input-port`
: `x` is a `u8vector` input port

`u8vector-output-port`
: `x` is a `u8vector` output port

`device-input-port`
: `x` is a device intput port

`device-output-port`
: `x` is a device output port

`process-port`
: `x` is a process port

`tcp-client-port`
: `x` is a tcp-client port

`tcp-server-port`
: `x` is a tcp-server port

`udp-port`
: `x` is a udp port

`udp-input-port`
: `x` is a udp input port

`udp-output-port`
: `x` is a udp output port

`tty-port`
: `x` is a tty port

### List and vector variants of the above

`list`
: no type checking (a non-null non-pair object is in fact a degenerate dotted
  list), exception mentions `LIST`

`proper-list`
: no type checking (code traversing the list must check for a proper-list),
  exception mentions `PROPER LIST`

`proper-list-null`
: `x` is the empty list, exception mentions `PROPER LIST`

`proper-or-circular-list`
: no type checking (code traversing the list must check for a proper-list or
  circular-list), exception mentions `PROPER` or `CIRCULAR LIST`

`proper-or-circular-list-null`
: `x` is the empty list, exception mentions `PROPER LIST`

`char-list`
: `x` is a character, exception mentions `CHARACTER LIST`

`char-vector`
: `x` is a character, exception mentions `CHARACTER VECTOR`

`pair-list`
: `x` is a pair, exception mentions `PAIR LIST`

`exact-unsigned-int8-list-exact-unsigned-int8`
: `x` is an `exact-unsigned-int8`, exception mentions `INTEGER LIST`

`exact-unsigned-int16-list-exact-unsigned-int16`
: `x` is an `exact-unsigned-int16`, exception mentions `INTEGER LIST`

`exact-unsigned-int32-list-exact-unsigned-int32`
: `x` is an exact-unsigned-int32, exception mentions `INTEGER LIST`

`exact-unsigned-int64-list-exact-unsigned-int64`
: `x` is an exact-unsigned-int64, exception mentions `INTEGER LIST`

`exact-signed-int8-list-exact-signed-int8`
: `x` is an exact-signed-int8, exception mentions `INTEGER LIST`

`exact-signed-int16-list-exact-signed-int16`
: `x` is an exact-signed-int16, exception mentions `INTEGER LIST`

`exact-signed-int32-list-exact-signed-int32`
: `x` is an exact-signed-int32, exception mentions `INTEGER LIST`

`exact-signed-int64-list-exact-signed-int64`
: `x` is an exact-signed-int64, exception mentions `INTEGER LIST`

### Gambit types

`error-exception`
: `x` is an error-exception object

`box`
: `x` is a box

`condvar`
: `x` is a condition variable

`f32vector`
: `x` is a `f32vector`

`f64vector`
: `x` is a `f64vector`

`foreign`
: `x` is a foreign object

`keyword`
: `x` is a keyword

`mutex`
: `x` is a mutex

`processor`
: `x` is a processor object

`s16vector`
: `x` is a `s16vector`

`s32vector`
: `x` is a `s32vector`

`s64vector`
: `x` is a `s64vector`

`s8vector`
: `x` is a `s8vector`

`table`
: `x` is a table

`tgroup`
: `x` is a thread group

`thread`
: `x` is a thread

`u16vector`
: `x` is a `u16vector`

`u32vector`
: `x` is a `u32vector`

`u64vector`
: `x` is a `u64vector`

`u8vector`
: `x` is a `u8vector`

`will`
: `x` is a will

`continuation`
: `x` is a continuation object

`random-source`
: `x` is a random-source object

`readtable`
: `x` is a readtable

`type`
: `x` is a structure type descriptor

`mutable`
: `x` is a mutable object

### Others

```
not-initialized-thread
not-started-thread
not-started-thread-given-initialized
string-or-ip-address
string-or-nonnegative-fixnum
```
