---
title: Numbers
---

## Extensions to numeric procedures

#### =
#### <
#### >
#### <=
#### >=

```scheme
(= z1 ...)  ;; procedure
(< x1 ...)  ;; procedure
(> x1 ...)  ;; procedure
(<= x1 ...) ;; procedure
(>= x1 ...) ;; procedure
```

These procedures take any number of arguments including no argument. This is
useful to test if the elements of a list are sorted in a particular order. For
example, testing that the list of numbers `lst` is sorted in nondecreasing order
can be done with the call `(apply < lst)`.

## IEEE floating point arithmetic

To better conform to IEEE floating point arithmetic the standard numeric tower
is extended with these special inexact reals:

`+inf.0`
: positive infinity

`-inf.0`
: negative infinity

`+nan.0`
: "not a number"

`-0.`
: negative zero (`0.` is the positive zero)

The infinities and "not a number" are reals (i.e. `(real? +inf.0)` is `#t`) but
are not rational (i.e. `(rational? +inf.0)` is `#f`).

Both zeros are numerically equal (i.e. `(= -0. 0.)` is `#t`) but are not
equivalent (i.e. `(eqv? -0. 0.)` and `(equal? -0. 0.)` are `#f`). All numerical
comparisons with "not a number", including `(= +nan.0 +nan.0)`, are `#f`.

## Integer square root and nth root

#### integer-sqrt

```scheme
(integer-sqrt n) ;; procedure
```

This procedure returns the integer part of the square root of the nonnegative
exact integer `n`.

For example:

```scheme
> (integer-sqrt 123)
11
```

#### integer-nth-root

```scheme
(integer-nth-root n1 n2) ;; procedure
```

This procedure returns the integer part of `n1` raised to the power `1/n2`,
where `n1` is a nonnegative exact integer and `n2` is a positive exact integer.

For example:

```scheme
> (integer-nth-root 100 3)
4
```

## Bitwise-operations on exact integers

The procedures defined in this section are compatible with the withdrawn
"Integer Bitwise-operation Library SRFI" ([SRFI
33](https://srfi.schemers.org/srfi-33/)). Note that some of the procedures
specified in SRFI 33 are not provided.

Most procedures in this section are specified in terms of the binary
representation of exact integers. The two's complement representation is assumed
where an integer is composed of an infinite number of bits. The upper section of
an integer (the most significant bits) are either an infinite sequence of ones
when the integer is negative, or they are an infinite sequence of zeros when the
integer is nonnegative.

#### arithmetic-shift

```scheme
(arithmetic-shift n1 n2) ;; procedure
```

This procedure returns `n1` shifted to the left by `n2` bits, that is `(floor (*
n1 (expt 2 n2)))`. Both `n1` and `n2` must be exact integers.

For example:

```scheme
> (arithmetic-shift 1000 7)  ; n1=...0000001111101000
128000
> (arithmetic-shift 1000 -6) ; n1=...0000001111101000
15
> (arithmetic-shift -23 -3)  ; n1=...1111111111101001
-3
```

#### bitwise-merge

```scheme
(bitwise-merge n1 n2 n3) ;; procedure
```

This procedure returns an exact integer whose bits combine the bits from `n2`
and `n3` depending on `n1`. The bit at index `i` of the result depends only on
the bits at index `i` in `n1`, `n2` and `n3`: it is equal to the bit in `n2`
when the bit in `n1` is 0 and it is equal to the bit in `n3` when the bit in
`n1` is 1. All arguments must be exact integers.

For example:

```scheme
> (bitwise-merge -4 -11 10) ; ...11111100 ...11110101 ...00001010
9
> (bitwise-merge 12 -11 10) ; ...00001100 ...11110101 ...00001010
-7
```

#### bitwise-and
```scheme
(bitwise-and n ...) ;; procedure
```

This procedure returns the bitwise "and" of the exact integers `n ...`. The
value `-1` is returned when there are no arguments.

For example:

```scheme
> (bitwise-and 6 12)  ; ...00000110 ...00001100
4
> (bitwise-and 6 -4)  ; ...00000110 ...11111100
4
> (bitwise-and -6 -4) ; ...11111010 ...11111100
-8
> (bitwise-and)
-1
```

#### bitwise-andc1

```scheme
(bitwise-andc1 n1 n2) ;; procedure
```

This procedure returns the bitwise "and" of the bitwise complement of the exact
integer `n1` and the exact integer `n2`.

For example:

```scheme
> (bitwise-andc1 11 26)  ; ...00001011 ...00011010
16
> (bitwise-andc1 -12 26) ; ...11110100 ...00011010
10
```

#### bitwise-andc2

```scheme
(bitwise-andc2 n1 n2) ;; procedure
```

This procedure returns the bitwise "and" of the exact integer `n1` and the bitwise
complement of the exact integer `n2`.

For example:

```scheme
> (bitwise-andc2 11 26)  ; ...00001011 ...00011010
1
> (bitwise-andc2 11 -27) ; ...00001011 ...11100101
10
```

#### bitwise-eqv

```scheme
(bitwise-eqv n ...) ;; procedure
```

This procedure returns the bitwise complement of the bitwise "exclusive-or" of
the exact integers `n ...`. The value `-1` is returned when there are no
arguments.

For example:

```scheme
> (bitwise-eqv 6 12)  ; ...00000110 ...00001100
-11
> (bitwise-eqv 6 -4)  ; ...00000110 ...11111100
5
> (bitwise-eqv -6 -4) ; ...11111010 ...11111100
-7
> (bitwise-eqv)
-1
```

#### bitwise-ior

```scheme
(bitwise-ior n ...) ;; procedure
```

This procedure returns the bitwise "inclusive-or" of the exact integers `n ...`.
The value `0` is returned when there are no arguments.

For example:

```scheme
> (bitwise-ior 6 12)  ; ...00000110 ...00001100
14
> (bitwise-ior 6 -4)  ; ...00000110 ...11111100
-2
> (bitwise-ior -6 -4) ; ...11111010 ...11111100
-2
> (bitwise-ior)
0
```

#### bitwise-nand

```scheme
(bitwise-nand n1 n2) ;; procedure
```

This procedure returns the bitwise complement of the bitwise "and" of the exact
integer `n1` and the exact integer `n2`.

For example:

```scheme
> (bitwise-nand 11 26)  ; ...00001011 ...00011010
-11
> (bitwise-nand 11 -27) ; ...00001011 ...11100101
-2
```

#### bitwise-nor

```scheme
(bitwise-nor n1 n2) ;; procedure
```

This procedure returns the bitwise complement of the bitwise "inclusive-or" of
the exact integer `n1` and the exact integer `n2`.

For example:

```scheme
> (bitwise-nor 11 26)  ; ...00001011 ...00011010
-28
> (bitwise-nor 11 -27) ; ...00001011 ...11100101
16
```

#### bitwise-not

```scheme
(bitwise-not n) ;; procedure
```

This procedure returns the bitwise complement of the exact integer `n`.

For example:

```scheme
> (bitwise-not 3)  ; ...00000011
-4
> (bitwise-not -1) ; ...11111111
0
```

#### bitwise-orc1

```scheme
(bitwise-orc1 n1 n2) ;; procedure
```

This procedure returns the bitwise "inclusive-or" of the bitwise complement of
the exact integer `n1` and the exact integer `n2`.

For example:

```scheme
> (bitwise-orc1 11 26)  ; ...00001011 ...00011010
-2
> (bitwise-orc1 -12 26) ; ...11110100 ...00011010
27
```

#### bitwise-orc2

```scheme
(bitwise-orc2 n1 n2) ;; procedure
```

This procedure returns the bitwise "inclusive-or" of the exact integer `n1` and
the bitwise complement of the exact integer `n2`.

For example:

```scheme
> (bitwise-orc2 11 26)  ; ...00001011 ...00011010
-17
> (bitwise-orc2 11 -27) ; ...00001011 ...11100101
27
```

#### bitwise-xor

```scheme
(bitwise-xor n ...) ;; procedure
```

This procedure returns the bitwise "exclusive-or" of the exact integers `n ...`.
The value `0` is returned when there are no arguments.

For example:

```scheme
> (bitwise-xor 6 12)  ; ...00000110 ...00001100
10
> (bitwise-xor 6 -4)  ; ...00000110 ...11111100
-6
> (bitwise-xor -6 -4) ; ...11111010 ...11111100
6
> (bitwise-xor)
0
```

#### bit-count

```scheme
(bit-count n) ;; procedure
```

This procedure returns the bit count of the exact integer `n`. If `n` is
nonnegative, the bit count is the number of 1 bits in the two's complement
representation of `n`. If `n` is negative, the bit count is the number of 0 bits
in the two's complement representation of `n`.

For example:

```scheme
> (bit-count 0)   ; ...00000000
0
> (bit-count 1)   ; ...00000001
1
> (bit-count 2)   ; ...00000010
1
> (bit-count 3)   ; ...00000011
2
> (bit-count 4)   ; ...00000100
1
> (bit-count -23) ; ...11101001
3
```

#### integer-length

```scheme
(integer-length n) ;; procedure
```

This procedure returns the bit length of the exact integer `n`. If `n` is a
positive integer the bit length is one more than the index of the highest 1 bit
(the least significant bit is at index 0). If `n` is a negative integer the bit
length is one more than the index of the highest 0 bit. If `n` is zero, the bit
length is 0.

For example:

```scheme
> (integer-length 0)   ; ...00000000
0
> (integer-length 1)   ; ...00000001
1
> (integer-length 2)   ; ...00000010
2
> (integer-length 3)   ; ...00000011
2
> (integer-length 4)   ; ...00000100
3
> (integer-length -23) ; ...11101001
5
```

#### bit-set?

```scheme
(bit-set? n1 n2) ;; procedure
```

This procedure returns a boolean indicating if the bit at index `n1` of `n2` is
set (i.e. equal to `1`) or not. Both `n1` and `n2` must be exact integers, and
`n1` must be nonnegative.

For example:

```scheme
> (map (lambda (i) (bit-set? i -23)) ; ...11101001
       '(7 6 5 4 3 2 1 0))
(#t #t #t #f #t #f #f #t)
```

#### any-bits-set?

```scheme
(any-bits-set? n1 n2) ;; procedure
```

This procedure returns a boolean indicating if the bitwise and of `n1` and `n2`
is different from zero or not. This procedure is implemented more efficiently
than the naive definition:

```scheme
(define (any-bits-set? n1 n2) (not (zero? (bitwise-and n1 n2))))
```

For example:

```scheme
> (any-bits-set? 5 10)   ; ...00000101 ...00001010
#f
> (any-bits-set? -23 32) ; ...11101001 ...00100000
#t
```

#### all-bits-set?

```scheme
(all-bits-set? n1 n2) ;; procedure
```

This procedure returns a boolean indicating if the "bitwise and" of `n1` and
`n2` is equal to `n1` or not. This procedure is implemented more efficiently
than the naive definition:

```scheme
(define (all-bits-set? n1 n2) (= n1 (bitwise-and n1 n2)))
```

For example:

```scheme
> (all-bits-set? 1 3) ; ...00000001 ...00000011
#t
> (all-bits-set? 7 3) ; ...00000111 ...00000011
#f
```

#### first-set-bit

```scheme
(first-set-bit n) ;; procedure
```

This procedure returns the bit index of the least significant bit of `n` equal
to 1 (which is also the number of 0 bits that are below the least significant 1
bit). This procedure returns -1 when `n` is zero.

For example:

```scheme
> (first-set-bit 24) ; ...00011000
3
> (first-set-bit 0)  ; ...00000000
-1
```

#### extract-bit-field
#### test-bit-field?
#### clear-bit-field
#### replace-bit-field
#### copy-bit-field

```scheme
(extract-bit-field n1 n2 n3) ;; procedure
(test-bit-field? n1 n2 n3) ;; procedure
(clear-bit-field n1 n2 n3) ;; procedure
(replace-bit-field n1 n2 n3 n4) ;; procedure
(copy-bit-field n1 n2 n3 n4) ;; procedure
```

These procedures operate on a bit-field which is `n1` bits wide starting at bit
index `n2`. All arguments must be exact integers and `n1` and `n2` must be
nonnegative.

The procedure `extract-bit-field` returns the bit-field of `n3` shifted to the
right so that the least significant bit of the bit-field is the least
significant bit of the result.

The procedure `test-bit-field?` returns `#t` if any bit in the bit-field of `n3`
is equal to `1`, otherwise `#f` is returned.

The procedure `clear-bit-field` returns `n3` with all bits in the bit-field
replaced with `0`.

The procedure `replace-bit-field` returns `n4` with the bit-field replaced with
the least-significant `n1` bits of `n3`.

The procedure `copy-bit-field` returns `n4` with the bit-field replaced with the
(same index and size) bit-field in `n3`.

For example:

```scheme
> (extract-bit-field 5 2 -37)    ; ...11011011
22
> (test-bit-field? 5 2 -37)      ; ...11011011
#t
> (test-bit-field? 1 2 -37)      ; ...11011011
#f
> (clear-bit-field 5 2 -37)      ; ...11011011
-125
> (replace-bit-field 5 2 -6 -37) ; ...11111010 ...11011011
-21
> (copy-bit-field 5 2 -6 -37)    ; ...11111010 ...11011011
-5
```

## Fixnum-specific operations

!!! bug "This documentation needs work, cleanup or clarification."

#### fixnum?
```scheme
(fixnum? obj) ;; procedure
```

#### fx*
```scheme
(fx* n1 ...) ;; procedure
```

#### fx+
```scheme
(fx+ n1 ...) ;; procedure
```

#### fx-
```scheme
(fx- n1 n2 ...) ;; procedure
```

#### fx<
```scheme
(fx< n1 ...) ;; procedure
```

#### fx<=
```scheme
(fx<= n1 ...) ;; procedure
```

#### fx=
```scheme
(fx= n1 ...) ;; procedure
```

#### fx>
```scheme
(fx> n1 ...) ;; procedure
```

#### fx>=
```scheme
(fx>= n1 ...) ;; procedure
```

#### fxabs
```scheme
(fxabs n) ;; procedure
```

#### fxand
```scheme
(fxand n1 ...) ;; procedure
```

#### fxandc1
```scheme
(fxandc1 n1 n2) ;; procedure
```

#### fxandc2
```scheme
(fxandc2 n1 n2) ;; procedure
```

#### fxarithmetic-shift
```scheme
(fxarithmetic-shift n1 n2) ;; procedure
```

#### fxarithmetic-shift-left
```scheme
(fxarithmetic-shift-left n1 n2) ;; procedure
```

#### fxarithmetic-shift-right
```scheme
(fxarithmetic-shift-right n1 n2) ;; procedure
```

#### fxbit-count
```scheme
(fxbit-count n) ;; procedure
```

#### fxbit-set?
```scheme
(fxbit-set? n1 n2) ;; procedure
```

#### fxeqv
```scheme
(fxeqv n1 ...) ;; procedure
```

#### fxeven?
```scheme
(fxeven? n) ;; procedure
```

#### fxfirst-set-bit
```scheme
(fxfirst-set-bit n) ;; procedure
```

#### fxif
```scheme
(fxif n1 n2 n3) ;; procedure
```

#### fxior
```scheme
(fxior n1 ...) ;; procedure
```

#### fxlength
```scheme
(fxlength n) ;; procedure
```

#### fxmax
```scheme
(fxmax n1 n2 ...) ;; procedure
```

#### fxmin
```scheme
(fxmin n1 n2 ...) ;; procedure
```

#### fxmodulo
```scheme
(fxmodulo n1 n2) ;; procedure
```

#### fxnegative?
```scheme
(fxnegative? n) ;; procedure
```

#### fxnand
```scheme
(fxnand n1 n2) ;; procedure
```

#### fxnor
```scheme
(fxnor n1 n2) ;; procedure
```

#### fxnot
```scheme
(fxnot n) ;; procedure
```

#### fxodd?
```scheme
(fxodd? n) ;; procedure
```

#### fxorc1
```scheme
(fxorc1 n1 n2) ;; procedure
```

#### fxorc2
```scheme
(fxorc2 n1 n2) ;; procedure
```

#### fxpositive?
```scheme
(fxpositive? n) ;; procedure
```

#### fxquotient
```scheme
(fxquotient n1 n2) ;; procedure
```

#### fxremainder
```scheme
(fxremainder n1 n2) ;; procedure
```

#### fxwrap*
```scheme
(fxwrap* n1 ...) ;; procedure
```

#### fxwrap+
```scheme
(fxwrap+ n1 ...) ;; procedure
```

#### fxwrap-
```scheme
(fxwrap- n1 n2 ...) ;; procedure
```

#### fxwrapabs
```scheme
(fxwrapabs n) ;; procedure
```

#### fxwraparithmetic-shift
```scheme
(fxwraparithmetic-shift n1 n2) ;; procedure
```

#### fxwraparithmetic-shift-left
```scheme
(fxwraparithmetic-shift-left n1 n2) ;; procedure
```

#### fxwraplogical-shift-right
```scheme
(fxwraplogical-shift-right n1 n2) ;; procedure
```

#### fxwrapquotient
```scheme
(fxwrapquotient n1 n2) ;; procedure
```

#### fxxor
```scheme
(fxxor n1 ...) ;; procedure
```

#### fxzero?
```scheme
(fxzero? n) ;; procedure
```

#### fxsquare
```scheme
(fxsquare n) ;; procedure
```

#### fxwrapsquare
```scheme
(fxwrapsquare n) ;; procedure
```

#### fixnum-overflow-exception?
```scheme
(fixnum-overflow-exception? obj) ;; procedure
```

#### fixnum-overflow-exception-procedure
```scheme
(fixnum-overflow-exception-procedure exc) ;; procedure
```

#### fixnum-overflow-exception-arguments
```scheme
(fixnum-overflow-exception-arguments exc) ;; procedure
```

Fixnum-overflow-exception objects are raised by some of the fixnum specific
procedures when the result is larger than can fit in a fixnum. The parameter `exc`
must be a fixnum-overflow-exception object.

The procedure `fixnum-overflow-exception?` returns `#t` when `obj` is a
fixnum-overflow-exception object and `#f` otherwise.

The procedure `fixnum-overflow-exception-procedure` returns the procedure that
raised `exc`.

The procedure `fixnum-overflow-exception-arguments` returns the list of arguments
of the procedure that raised `exc`.

For example:

```scheme
> (define (handler exc)
    (if (fixnum-overflow-exception? exc)
        (list (fixnum-overflow-exception-procedure exc)
              (fixnum-overflow-exception-arguments exc))
        'not-fixnum-overflow-exception))
> (with-exception-catcher
    handler
    (lambda () (fx* 100000 100000)))
(#<procedure #2 fx*> (100000 100000))
```

## Pseudo random numbers

The procedures and variables defined in this section are compatible with the
"Sources of Random Bits SRFI" ([SRFI 27](https://srfi.schemers.org/srfi-27/)).
The implementation is based on Pierre L’Ecuyer's MRG32k3a pseudo random number
generator. At the heart of SRFI 27's interface is the random source type which
encapsulates the state of a pseudo random number generator. The state of a
random source object changes every time a pseudo random number is generated from
this random source object.

#### default-random-source

```scheme
(default-random-source) ;; variable
```

The global variable `default-random-source` is bound to the random source object
which is used by the `random-integer`, `random-real`, `random-u8vector` and
`random-f64vector` procedures.

#### random-integer

```scheme
(random-integer n) ;; procedure
```

This procedure returns a pseudo random exact integer in the range `0` to `n-1`.
The random source object in the global variable `default-random-source` is used
to generate this number. The parameter `n` must be a positive exact integer.

For example:

```scheme
> (random-integer 100)
24
> (random-integer 100)
2
> (random-integer 10000000000000000000000000000000000000000)
6143360270902284438072426748425263488507
```

#### random-real

```scheme
(random-real) ;; procedure
```

This procedure returns a pseudo random inexact real between, but not including,
`0` and `1`. The random source object in the global variable
`default-random-source` is used to generate this number.

For example:

```scheme
> (random-real)
.24230672079133753
> (random-real)
.02317001922506932
```

#### random-u8vector

```scheme
(random-u8vector n) ;; procedure
```

This procedure returns a `u8vector` of length `n` containing pseudo random exact
integers in the range `0` to `255`. The random source object in the global
variable `default-random-source` is used to generate these numbers. The
parameter `n` must be a nonnegative exact integer.

For example:

```scheme
> (random-u8vector 10)
#u8(200 53 29 202 3 85 208 187 73 219)
```

#### random-f64vector

```scheme
(random-f64vector n) ;; procedure
```

This procedure returns a f64vector of length n containing pseudo random inexact
reals between, but not including, 0 and 1. The random source object in the
global variable default-random-source is used to generate these numbers. The
parameter n must be a nonnegative exact integer.

For example:

```scheme
> (random-f64vector 3)
#f64(.7145854494613069 .47089632669147946 .5400124875182746)
```

#### make-random-source

```scheme
(make-random-source) ;; procedure
```

This procedure returns a new random source object initialized to a predetermined
state (to initialize to a pseudo random state the procedure
`random-source-randomize!` should be called).

For example:

```scheme
> (define rs (make-random-source))
> ((random-source-make-integers rs) 10000000)
8583952
```

#### random-source?

```scheme
(random-source? obj) ;; procedure
```

This procedure returns `#t` when `obj` is a random source object and `#f`
otherwise.

For example:

```scheme
> (random-source? default-random-source)
#t
> (random-source? 123)
#f
```

#### random-source-state-ref
#### random-source-state-set!

````scheme
(random-source-state-ref random-source)	procedure
(random-source-state-set! random-source state)	procedure
```

The procedure `random-source-state-ref` extracts the state of the random source
object `random-source` and returns a vector containing the state.

The procedure `random-source-state-set!` restores the state of the random source
object `random-source` to `state` which must be a vector returned from a call to
the procedure `random-source-state-ref`.

For example:

```scheme
> (define s (random-source-state-ref default-random-source))
> (random-integer 10000000000000000000000000000000000000000)
7583880188903074396261960585615270693321
> (random-source-state-set! default-random-source s)
> (random-integer 10000000000000000000000000000000000000000)
7583880188903074396261960585615270693321
```

#### random-source-randomize!
#### random-source-pseudo-randomize!

```scheme
(random-source-randomize! random-source) ;; procedure
(random-source-pseudo-randomize! random-source i j) ;; procedure
```

These procedures change the state of the random source object `random-source`.
The procedure `random-source-randomize!` sets the random source object to a
state that depends on the current time (which for typical uses can be considered
to randomly initialize the state). The procedure
`random-source-pseudo-randomize!` sets the random source object to a state that
is determined only by the current state and the nonnegative exact integers `i`
and `j`. For both procedures the value returned is unspecified.

For example:

```scheme
> (define s (random-source-state-ref default-random-source))
> (random-source-pseudo-randomize! default-random-source 5 99)
> (random-integer 10000000000000000000000000000000000000000)
9816755163910623041601722050112674079767
> (random-source-state-set! default-random-source s)
> (random-source-pseudo-randomize! default-random-source 5 99)
> (random-integer 10000000000000000000000000000000000000000)
9816755163910623041601722050112674079767
> (random-source-pseudo-randomize! default-random-source 5 99)
> (random-integer 10000000000000000000000000000000000000000)
9816755163910623041601722050112674079767
> (random-source-state-set! default-random-source s)
> (random-source-randomize! default-random-source)
> (random-integer 10000000000000000000000000000000000000000)
2271441220851914333384493143687768110622
> (random-source-state-set! default-random-source s)
> (random-source-randomize! default-random-source)
> (random-integer 10000000000000000000000000000000000000000)
6247966138948323029033944059178072366895
```

#### random-source-make-integers

```scheme
(random-source-make-integers random-source) ;; procedure
```

This procedure returns a procedure for generating pseudo random exact integers
using the random source object `random-source`. The returned procedure accepts a
single parameter `n`, a positive exact integer, and returns a pseudo random
exact integer in the range `0` to `n-1`.

For example:

```scheme
> (define rs (make-random-source))
> (define ri (random-source-make-integers rs))
> (ri 10000000)
8583952
> (ri 10000000)
2879793
```


#### random-source-make-reals

```scheme
(random-source-make-reals random-source [precision]) ;; procedure
```

This procedure returns a procedure for generating pseudo random inexact reals
using the random source object `random-source`. The returned procedure accepts
no parameters and returns a pseudo random inexact real between, but not
including, `0` and `1`. The optional parameter `precision` specifies an upper
bound on the minimum amount by which two generated pseudo-random numbers can be
separated.

For example:

```scheme
> (define rs (make-random-source))
> (define rr (random-source-make-reals rs))
> (rr)
.857402537562821
> (rr)
.2876463473845367
```

#### random-source-make-u8vectors

```scheme
(random-source-make-u8vectors random-source) ;; procedure
```

This procedure returns a procedure for generating pseudo random `u8vectors` using
the random source object `random-source`. The returned procedure accepts a single
parameter `n`, a nonnegative exact integer, and returns a `u8vector` of length `n`
containing pseudo random exact integers in the range `0` to `255`.

For example:

```scheme
> (define rs (make-random-source))
> (define rv (random-source-make-u8vectors rs))
> (rv 10)
#u8(200 53 29 202 3 85 208 187 73 219)
> (rv 10)
#u8(113 8 182 120 138 103 53 192 40 176)
```


#### random-source-make-f64vectors

```scheme
(random-source-make-f64vectors random-source [precision]) ;; procedure
```

This procedure returns a procedure for generating pseudo random `f64vectors`
using the random source object `random-source`. The returned procedure accepts a
single parameter `n`, a nonnegative exact integer, and returns an `f64vector` of
length `n` containing pseudo random inexact reals between, but not including,
`0` and `1`. The optional parameter `precision` specifies an upper bound on the
minimum amount by which two generated pseudo-random numbers can be separated.

For example:

```scheme
> (define rv (random-source-make-f64vectors rs))
> (rv 3)
#f64(.7342236104231586 .2876463473845367 .8574025375628211)
> (rv 3)
#f64(.013863292728449427 .33449296573515447 .8162050798467028)
```
