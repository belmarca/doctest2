---
title: Homogeneous numeric vectors
---

# Homogeneous numeric vectors

Homogeneous vectors are vectors containing raw numbers of the same type (signed
or unsigned exact integers or inexact reals). There are 10 types of homogeneous
vectors:

`s8vector`
: vector of exact integers in the range `-2^7` to `2^7-1`

`u8vector`
: vector of exact integers in the range `0` to `2^8-1`

`s16vector`
: vector of exact integers in the range `-2^15` to `2^15-1`

`u16vector`
: vector of exact integers in the range `0` to `2^16-1`

`s32vector`
: vector of exact integers in the range `-2^31` to `2^31-1`

`u32vector`
: vector of exact integers in the range `0` to `2^32-1`

`s64vector`
: vector of exact integers in the range `-2^63` to `2^63-1`

`u64vector`
: vector of exact integers in the range `0` to `2^64-1`

`f32vector`
: vector of 32 bit floating point numbers

`f64vector`
: vector of 64 bit floating point numbers

The lexical syntax of homogeneous vectors is specified in [Homogeneous vector
syntax](/manual/lexical_syntax_and_readtables/homogeneous_vector_syntax).

The procedures available for homogeneous vectors, listed below, are the analog
of the normal vector/string procedures for each of the homogeneous vector types.

#### s8vector?
```scheme
(s8vector? obj) ;; procedure
```

#### make-s8vector
```scheme
(make-s8vector k [fill]) ;; procedure
```

#### s8vector
```scheme
(s8vector exact-int8 ...) ;; procedure
```

#### s8vector-length
```scheme
(s8vector-length s8vector) ;; procedure
```

#### s8vector-ref
```scheme
(s8vector-ref s8vector k) ;; procedure
```

#### s8vector-set
```scheme
(s8vector-set s8vector k exact-int8) ;; procedure
```

#### s8vector-set!
```scheme
(s8vector-set! s8vector k exact-int8) ;; procedure
```

#### s8vector->list
```scheme
(s8vector->list s8vector) ;; procedure
```

#### list->s8vector
```scheme
(list->s8vector list-of-exact-int8) ;; procedure
```

#### s8vector-fill!
```scheme
(s8vector-fill! s8vector fill [start [end]]) ;; procedure
```

#### subs8vector-fill!
```scheme
(subs8vector-fill! vector start end fill) ;; procedure
```

#### s8vector-concatenate
```scheme
(s8vector-concatenate lst [separator]) ;; procedure
```

#### s8vector-copy
```scheme
(s8vector-copy s8vector [start [end]]) ;; procedure
```

#### s8vector-copy!
```scheme
(s8vector-copy! dest-s8vector dest-start s8vector [start [end]]) ;; procedure
```

#### s8vector-append
```scheme
(s8vector-append s8vector ...) ;; procedure
```

#### subs8vector
```scheme
(subs8vector s8vector start end) ;; procedure
```

#### subs8vector-move!
```scheme
(subs8vector-move! src-s8vector src-start src-end dst-s8vector dst-start) ;; procedure
```

#### s8vector-shrink!
```scheme
(s8vector-shrink! s8vector k) ;; procedure
```

#### u8vector?
```scheme
(u8vector? obj) ;; procedure
```

#### make-u8vector
```scheme
(make-u8vector k [fill]) ;; procedure
```

#### u8vector
```scheme
(u8vector exact-int8 ...) ;; procedure
```

#### u8vector-length
```scheme
(u8vector-length u8vector) ;; procedure
```

#### u8vector-ref
```scheme
(u8vector-ref u8vector k) ;; procedure
```

#### u8vector-set
```scheme
(u8vector-set u8vector k exact-int8) ;; procedure
```

#### u8vector-set!
```scheme
(u8vector-set! u8vector k exact-int8) ;; procedure
```

#### u8vector->list
```scheme
(u8vector->list u8vector) ;; procedure
```

#### list->u8vector
```scheme
(list->u8vector list-of-exact-int8) ;; procedure
```

#### u8vector-fill!
```scheme
(u8vector-fill! u8vector fill [start [end]]) ;; procedure
```

#### subu8vector-fill!
```scheme
(subu8vector-fill! vector start end fill) ;; procedure
```

#### u8vector-concatenate
```scheme
(u8vector-concatenate lst [separator]) ;; procedure
```

#### u8vector-copy
```scheme
(u8vector-copy u8vector [start [end]]) ;; procedure
```

#### u8vector-copy!
```scheme
(u8vector-copy! dest-u8vector dest-start u8vector [start [end]]) ;; procedure
```

#### u8vector-append
```scheme
(u8vector-append u8vector ...) ;; procedure
```

#### subu8vector
```scheme
(subu8vector u8vector start end) ;; procedure
```

#### subu8vector-move!
```scheme
(subu8vector-move! src-u8vector src-start src-end dst-u8vector dst-start) ;; procedure
```

#### u8vector-shrink!
```scheme
(u8vector-shrink! u8vector k) ;; procedure
```

#### s16vector?
```scheme
(s16vector? obj) ;; procedure
```

#### make-s16vector
```scheme
(make-s16vector k [fill]) ;; procedure
```

#### s16vector
```scheme
(s16vector exact-int16 ...) ;; procedure
```

#### s16vector-length
```scheme
(s16vector-length s16vector) ;; procedure
```

#### s16vector-ref
```scheme
(s16vector-ref s16vector k) ;; procedure
```

#### s16vector-set
```scheme
(s16vector-set s16vector k exact-int16) ;; procedure
```

#### s16vector-set!
```scheme
(s16vector-set! s16vector k exact-int16) ;; procedure
```

#### s16vector->list
```scheme
(s16vector->list s16vector) ;; procedure
```

#### list->s16vector
```scheme
(list->s16vector list-of-exact-int16) ;; procedure
```

#### s16vector-fill!
```scheme
(s16vector-fill! s16vector fill [start [end]]) ;; procedure
```

#### subs16vector-fill!
```scheme
(subs16vector-fill! vector start end fill) ;; procedure
```

#### s16vector-concatenate
```scheme
(s16vector-concatenate lst [separator]) ;; procedure
```

#### s16vector-copy
```scheme
(s16vector-copy s16vector [start [end]]) ;; procedure
```

#### s16vector-copy!
```scheme
(s16vector-copy! dest-s16vector dest-start s16vector [start [end]]) ;; procedure
```

#### s16vector-append
```scheme
(s16vector-append s16vector ...) ;; procedure
```

#### subs16vector
```scheme
(subs16vector s16vector start end) ;; procedure
```

#### subs16vector-move!
```scheme
(subs16vector-move! src-s16vector src-start src-end dst-s16vector dst-start) ;; procedure
```

#### s16vector-shrink!
```scheme
(s16vector-shrink! s16vector k) ;; procedure
```

#### u16vector?
```scheme
(u16vector? obj) ;; procedure
```

#### make-u16vector
```scheme
(make-u16vector k [fill]) ;; procedure
```

#### u16vector
```scheme
(u16vector exact-int16 ...) ;; procedure
```

#### u16vector-length
```scheme
(u16vector-length u16vector) ;; procedure
```

#### u16vector-ref
```scheme
(u16vector-ref u16vector k) ;; procedure
```

#### u16vector-set
```scheme
(u16vector-set u16vector k exact-int16) ;; procedure
```

#### u16vector-set!
```scheme
(u16vector-set! u16vector k exact-int16) ;; procedure
```

#### u16vector->list
```scheme
(u16vector->list u16vector) ;; procedure
```

#### list->u16vector
```scheme
(list->u16vector list-of-exact-int16) ;; procedure
```

#### u16vector-fill!
```scheme
(u16vector-fill! u16vector fill [start [end]]) ;; procedure
```

#### subu16vector-fill!
```scheme
(subu16vector-fill! vector start end fill) ;; procedure
```

#### u16vector-concatenate
```scheme
(u16vector-concatenate lst [separator]) ;; procedure
```

#### u16vector-copy
```scheme
(u16vector-copy u16vector [start [end]]) ;; procedure
```

#### u16vector-copy!
```scheme
(u16vector-copy! dest-u16vector dest-start u16vector [start [end]]) ;; procedure
```

#### u16vector-append
```scheme
(u16vector-append u16vector ...) ;; procedure
```

#### subu16vector
```scheme
(subu16vector u16vector start end) ;; procedure
```

#### subu16vector-move!
```scheme
(subu16vector-move! src-u16vector src-start src-end dst-u16vector dst-start) ;; procedure
```

#### u16vector-shrink!
```scheme
(u16vector-shrink! u16vector k) ;; procedure
```

#### s32vector?
```scheme
(s32vector? obj) ;; procedure
```

#### make-s32vector
```scheme
(make-s32vector k [fill]) ;; procedure
```

#### s32vector
```scheme
(s32vector exact-int32 ...) ;; procedure
```

#### s32vector-length
```scheme
(s32vector-length s32vector) ;; procedure
```

#### s32vector-ref
```scheme
(s32vector-ref s32vector k) ;; procedure
```

#### s32vector-set
```scheme
(s32vector-set s32vector k exact-int32) ;; procedure
```

#### s32vector-set!
```scheme
(s32vector-set! s32vector k exact-int32) ;; procedure
```

#### s32vector->list
```scheme
(s32vector->list s32vector) ;; procedure
```

#### list->s32vector
```scheme
(list->s32vector list-of-exact-int32) ;; procedure
```

#### s32vector-fill!
```scheme
(s32vector-fill! s32vector fill [start [end]]) ;; procedure
```

#### subs32vector-fill!
```scheme
(subs32vector-fill! vector start end fill) ;; procedure
```

#### s32vector-concatenate
```scheme
(s32vector-concatenate lst [separator]) ;; procedure
```

#### s32vector-copy
```scheme
(s32vector-copy s32vector [start [end]]) ;; procedure
```

#### s32vector-copy!
```scheme
(s32vector-copy! dest-s32vector dest-start s32vector [start [end]]) ;; procedure
```

#### s32vector-append
```scheme
(s32vector-append s32vector ...) ;; procedure
```

#### subs32vector
```scheme
(subs32vector s32vector start end) ;; procedure
```

#### subs32vector-move!
```scheme
(subs32vector-move! src-s32vector src-start src-end dst-s32vector dst-start) ;; procedure
```

#### s32vector-shrink!
```scheme
(s32vector-shrink! s32vector k) ;; procedure
```

#### u32vector?
```scheme
(u32vector? obj) ;; procedure
```

#### make-u32vector
```scheme
(make-u32vector k [fill]) ;; procedure
```

#### u32vector
```scheme
(u32vector exact-int32 ...) ;; procedure
```

#### u32vector-length
```scheme
(u32vector-length u32vector) ;; procedure
```

#### u32vector-ref
```scheme
(u32vector-ref u32vector k) ;; procedure
```

#### u32vector-set
```scheme
(u32vector-set u32vector k exact-int32) ;; procedure
```

#### u32vector-set!
```scheme
(u32vector-set! u32vector k exact-int32) ;; procedure
```

#### u32vector->list
```scheme
(u32vector->list u32vector) ;; procedure
```

#### list->u32vector
```scheme
(list->u32vector list-of-exact-int32) ;; procedure
```

#### u32vector-fill!
```scheme
(u32vector-fill! u32vector fill [start [end]]) ;; procedure
```

#### subu32vector-fill!
```scheme
(subu32vector-fill! vector start end fill) ;; procedure
```

#### u32vector-concatenate
```scheme
(u32vector-concatenate lst [separator]) ;; procedure
```

#### u32vector-copy
```scheme
(u32vector-copy u32vector [start [end]]) ;; procedure
```

#### u32vector-copy!
```scheme
(u32vector-copy! dest-u32vector dest-start u32vector [start [end]]) ;; procedure
```

#### u32vector-append
```scheme
(u32vector-append u32vector ...) ;; procedure
```

#### subu32vector
```scheme
(subu32vector u32vector start end) ;; procedure
```

#### subu32vector-move!
```scheme
(subu32vector-move! src-u32vector src-start src-end dst-u32vector dst-start) ;; procedure
```

#### u32vector-shrink!
```scheme
(u32vector-shrink! u32vector k) ;; procedure
```

#### s64vector?
```scheme
(s64vector? obj) ;; procedure
```

#### make-s64vector
```scheme
(make-s64vector k [fill]) ;; procedure
```

#### s64vector
```scheme
(s64vector exact-int64 ...) ;; procedure
```

#### s64vector-length
```scheme
(s64vector-length s64vector) ;; procedure
```

#### s64vector-ref
```scheme
(s64vector-ref s64vector k) ;; procedure
```

#### s64vector-set
```scheme
(s64vector-set s64vector k exact-int64) ;; procedure
```

#### s64vector-set!
```scheme
(s64vector-set! s64vector k exact-int64) ;; procedure
```

#### s64vector->list
```scheme
(s64vector->list s64vector) ;; procedure
```

#### list->s64vector
```scheme
(list->s64vector list-of-exact-int64) ;; procedure
```

#### s64vector-fill!
```scheme
(s64vector-fill! s64vector fill [start [end]]) ;; procedure
```

#### subs64vector-fill!
```scheme
(subs64vector-fill! vector start end fill) ;; procedure
```

#### s64vector-concatenate
```scheme
(s64vector-concatenate lst [separator]) ;; procedure
```

#### s64vector-copy
```scheme
(s64vector-copy s64vector [start [end]]) ;; procedure
```

#### s64vector-copy!
```scheme
(s64vector-copy! dest-s64vector dest-start s64vector [start [end]]) ;; procedure
```

#### s64vector-append
```scheme
(s64vector-append s64vector ...) ;; procedure
```

#### subs64vector
```scheme
(subs64vector s64vector start end) ;; procedure
```

#### subs64vector-move!
```scheme
(subs64vector-move! src-s64vector src-start src-end dst-s64vector dst-start) ;; procedure
```

#### s64vector-shrink!
```scheme
(s64vector-shrink! s64vector k) ;; procedure
```

#### u64vector?
```scheme
(u64vector? obj) ;; procedure
```

#### make-u64vector
```scheme
(make-u64vector k [fill]) ;; procedure
```

#### u64vector
```scheme
(u64vector exact-int64 ...) ;; procedure
```

#### u64vector-length
```scheme
(u64vector-length u64vector) ;; procedure
```

#### u64vector-ref
```scheme
(u64vector-ref u64vector k) ;; procedure
```

#### u64vector-set
```scheme
(u64vector-set u64vector k exact-int64) ;; procedure
```

#### u64vector-set!
```scheme
(u64vector-set! u64vector k exact-int64) ;; procedure
```

#### u64vector->list
```scheme
(u64vector->list u64vector) ;; procedure
```

#### list->u64vector
```scheme
(list->u64vector list-of-exact-int64) ;; procedure
```

#### u64vector-fill!
```scheme
(u64vector-fill! u64vector fill [start [end]]) ;; procedure
```

#### subu64vector-fill!
```scheme
(subu64vector-fill! vector start end fill) ;; procedure
```

#### u64vector-concatenate
```scheme
(u64vector-concatenate lst [separator]) ;; procedure
```

#### u64vector-copy
```scheme
(u64vector-copy u64vector [start [end]]) ;; procedure
```

#### u64vector-copy!
```scheme
(u64vector-copy! dest-u64vector dest-start u64vector [start [end]]) ;; procedure
```

#### u64vector-append
```scheme
(u64vector-append u64vector ...) ;; procedure
```

#### subu64vector
```scheme
(subu64vector u64vector start end) ;; procedure
```

#### subu64vector-move!
```scheme
(subu64vector-move! src-u64vector src-start src-end dst-u64vector dst-start) ;; procedure
```

#### u64vector-shrink!
```scheme
(u64vector-shrink! u64vector k) ;; procedure
```

#### f32vector?
```scheme
(f32vector? obj) ;; procedure
```

#### make-f32vector
```scheme
(make-f32vector k [fill]) ;; procedure
```

#### f32vector
```scheme
(f32vector inexact-real ...) ;; procedure
```

#### f32vector-length
```scheme
(f32vector-length f32vector) ;; procedure
```

#### f32vector-ref
```scheme
(f32vector-ref f32vector k) ;; procedure
```

#### f32vector-set
```scheme
(f32vector-set f32vector k inexact-real) ;; procedure
```

#### f32vector-set!
```scheme
(f32vector-set! f32vector k inexact-real) ;; procedure
```

#### f32vector->list
```scheme
(f32vector->list f32vector) ;; procedure
```

#### list->f32vector
```scheme
(list->f32vector list-of-inexact-real) ;; procedure
```

#### f32vector-fill!
```scheme
(f32vector-fill! f32vector fill [start [end]]) ;; procedure
```

#### subf32vector-fill!
```scheme
(subf32vector-fill! vector start end fill) ;; procedure
```

#### f32vector-concatenate
```scheme
(f32vector-concatenate lst [separator]) ;; procedure
```

#### f32vector-copy
```scheme
(f32vector-copy f32vector [start [end]]) ;; procedure
```

#### f32vector-copy!
```scheme
(f32vector-copy! dest-f32vector dest-start f32vector [start [end]]) ;; procedure
```

#### f32vector-append
```scheme
(f32vector-append f32vector ...) ;; procedure
```

#### subf32vector
```scheme
(subf32vector f32vector start end) ;; procedure
```

#### subf32vector-move!
```scheme
(subf32vector-move! src-f32vector src-start src-end dst-f32vector dst-start) ;; procedure
```

#### f32vector-shrink!
```scheme
(f32vector-shrink! f32vector k) ;; procedure
```

#### f64vector?
```scheme
(f64vector? obj) ;; procedure
```

#### make-f64vector
```scheme
(make-f64vector k [fill]) ;; procedure
```

#### f64vector
```scheme
(f64vector inexact-real ...) ;; procedure
```

#### f64vector-length
```scheme
(f64vector-length f64vector) ;; procedure
```

#### f64vector-ref
```scheme
(f64vector-ref f64vector k) ;; procedure
```

#### f64vector-set
```scheme
(f64vector-set f64vector k inexact-real) ;; procedure
```

#### f64vector-set!
```scheme
(f64vector-set! f64vector k inexact-real) ;; procedure
```

#### f64vector->list
```scheme
(f64vector->list f64vector) ;; procedure
```

#### list->f64vector
```scheme
(list->f64vector list-of-inexact-real) ;; procedure
```

#### f64vector-fill!
```scheme
(f64vector-fill! f64vector fill [start [end]]) ;; procedure
```

#### subf64vector-fill!
```scheme
(subf64vector-fill! vector start end fill) ;; procedure
```

#### f64vector-concatenate
```scheme
(f64vector-concatenate lst [separator]) ;; procedure
```

#### f64vector-copy
```scheme
(f64vector-copy f64vector [start [end]]) ;; procedure
```

#### f64vector-copy!
```scheme
(f64vector-copy! dest-f64vector dest-start f64vector [start [end]]) ;; procedure
```

#### f64vector-append
```scheme
(f64vector-append f64vector ...) ;; procedure
```

#### subf64vector
```scheme
(subf64vector f64vector start end) ;; procedure
```

#### subf64vector-move!
```scheme
(subf64vector-move! src-f64vector src-start src-end dst-f64vector dst-start) ;; procedure
```

#### f64vector-shrink!
```scheme
(f64vector-shrink! f64vector k)
```

For example:

```scheme
> (define v (u8vector 10 255 13))
> (u8vector-set! v 2 99)
> v
#u8(10 255 99)
> (u8vector-ref v 1)
255
> (u8vector->list v)
(10 255 99)
> (u8vector-shrink! v 2)
> (v)
#u8(10 255)
```

#### object->u8vector
#### u8vector->object

```scheme
(object->u8vector obj [encoder]) ;; procedure
(u8vector->object u8vector [decoder]) ;; procedure
```

The procedure `object->u8vector` returns a `u8vector` that contains the sequence
of bytes that encodes the object `obj`. The procedure `u8vector->object` decodes
the sequence of bytes contained in the u8vector `u8vector`, which was produced
by the procedure `object->u8vector`, and reconstructs an object structurally
equal to the original object. In other words the procedures `object->u8vector`
and `u8vector->object` respectively perform serialization and deserialization of
Scheme objects. Note that some objects are non-serializable (e.g. threads,
wills, some types of ports, and any object containing a non-serializable
object).

The optional `encoder` and `decoder` parameters are single parameter procedures
which default to the identity function. The `encoder` procedure is called during
serialization. As the serializer walks through `obj`, it calls the `encoder`
procedure on each sub-object `X` that is encountered. The `encoder` transforms the
object `X` into an object `Y` that will be serialized instead of `X`. Similarly the
`decoder` procedure is called during deserialization. When an object `Y` is
encountered, the `decoder` procedure is called to transform it into the object `X`
that is the result of deserialization.

The `encoder` and `decoder` procedures are useful to customize the serialized
representation of objects. In particular, it can be used to define the semantics
of serializing objects, such as threads and ports, that would otherwise not be
serializable. The `decoder` procedure is typically the inverse of the `encoder`
procedure, i.e. `(decoder (encoder X)) = X`.

For example:

```scheme
> (define (make-adder x) (lambda (y) (+ x y)))
> (define f (make-adder 10))
> (define a (object->u8vector f))
> (define b (u8vector->object a))
> (u8vector-length a)
1639
> (f 5)
15
> (b 5)
15
> (pp b)
(lambda (y) (+ x y))
```
