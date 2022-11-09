---
title: Hashing and weak references
---

## Hashing

#### object->serial-number
#### serial-number->object

```scheme
(object->serial-number obj) ;; procedure
(serial-number->object n [default]) ;; procedure
```

All Scheme objects are uniquely identified with a serial number which is a
nonnegative exact integer. The `object->serial-number` procedure returns the
serial number of object `obj`. This serial number is only allocated the first
time the `object->serial-number` procedure is called on that object. Objects
which do not have an external textual representation that can be read by the
`read` procedure, use an external textual representation that includes a serial
number of the form `#n`. Consequently, the procedures `write`, `pretty-print`,
etc will call the `object->serial-number` procedure to get the serial number,
and this may cause the serial number to be allocated.

The `serial-number->object` procedure takes an exact integer parameter `n` and
returns the object whose serial number is `n`. If no object currently exists
with that serial number, `default` is returned if it is specified, otherwise an
`unbound-serial-number-exception` object is raised. The reader defines the
following abbreviation for calling `serial-number->object`: the syntax `#n`,
where `n` is a sequence of decimal digits and it is not followed by `=` or `#`,
is equivalent to the list `(serial-number->object n)`.

For example:

```scheme
> (define z (list (lambda (x) (* x x)) (lambda (y) (/ 1 y))))
> z
(#<procedure #2> #<procedure #3>)
> (#3 10)
1/10
> '(#3 10)
((serial-number->object 3) 10)
> car
#<procedure #4 car>
> (#4 z)
#<procedure #2>
```

#### unbound-serial-number-exception?
#### unbound-serial-number-exception-procedure
#### unbound-serial-number-exception-arguments

```scheme
(unbound-serial-number-exception? obj) ;; procedure
(unbound-serial-number-exception-procedure exc) ;; procedure
(unbound-serial-number-exception-arguments exc) ;; procedure
```

Unbound-serial-number-exception objects are raised by the procedure
`serial-number->object` when no object currently exists with that serial number.
The parameter `exc` must be an unbound-serial-number-exception object.

The procedure `unbound-serial-number-exception?` returns `#t` when `obj` is a
unbound-serial-number-exception object and `#f` otherwise.

The procedure `unbound-serial-number-exception-procedure` returns the procedure
that raised `exc`.

The procedure `unbound-serial-number-exception-arguments` returns the list of
arguments of the procedure that raised `exc`.

For example:

```scheme
> (define (handler exc)
    (if (unbound-serial-number-exception? exc)
        (list (unbound-serial-number-exception-procedure exc)
              (unbound-serial-number-exception-arguments exc))
        'not-unbound-serial-number-exception))
> (with-exception-catcher
    handler
    (lambda () (serial-number->object 1000)))
(#<procedure #2 serial-number->object> (1000))
```

#### symbol-hash

```scheme
(symbol-hash symbol) ;; procedure
```

The `symbol-hash` procedure returns the hash number of the symbol `symbol`. The
hash number is a small exact integer (fixnum). When `symbol` is an interned
symbol the value returned is the same as `(string=?-hash (symbol->string
symbol))`.

For example:

```scheme
> (symbol-hash 'car)
444471047
```


#### keyword-hash

```scheme
(keyword-hash keyword) ;; procedure
```

The `keyword-hash` procedure returns the hash number of the keyword `keyword`.
The hash number is a small exact integer (fixnum). When `keyword` is an interned
keyword the value returned is the same as `(string=?-hash (keyword->string
keyword))`.

For example:

```scheme
> (keyword-hash car:)
444471047
```

#### string=?-hash

```scheme
(string=?-hash string)	procedure
```

The `string=?-hash` procedure returns the hash number of the string `string`.
The hash number is a small exact integer (fixnum). For any two strings `s1` and
`s2`, `(string=? s1 s2)` implies `(= (string=?-hash s1) (string=?-hash s2))`.

For example:

```scheme
> (string=?-hash "car")
444471047
```

#### string-ci=?-hash

```scheme
(string-ci=?-hash string) ;; procedure
```

The `string-ci=?-hash` procedure returns the hash number of the string `string`.
The hash number is a small exact integer (fixnum). For any two strings `s1` and
`s2`, `(string-ci=? s1 s2)` implies `(= (string-ci=?-hash s1) (string-ci=?-hash
s2))`.

For example:

```scheme
> (string-ci=?-hash "CaR")
444471047
```

#### eq?-hash

```scheme
(eq?-hash obj)	procedure
```

The `eq?-hash` procedure returns the hash number of the object `obj`. The hash
number is a small exact integer (fixnum). For any two objects `o1` and `o2`,
`(eq? o1 o2)` implies `(= (eq?-hash o1) (eq?-hash o2))`.

For example:

```scheme
> (eq?-hash #t)
536870910
```

#### eqv?-hash

```scheme
(eqv?-hash obj) ;; procedure
```

The `eqv?-hash` procedure returns the hash number of the object `obj`. The hash
number is a small exact integer (fixnum). For any two objects `o1` and `o2`,
`(eqv? o1 o2)` implies `(= (eqv?-hash o1) (eqv?-hash o2))`.

For example:

```scheme
> (eqv?-hash 1.5)
496387656
```

#### equal?-hash

```scheme
(equal?-hash obj) ;; procedure
```

The `equal?-hash` procedure returns the hash number of the object `obj`. The
hash number is a small exact integer (fixnum). For any two objects `o1` and
`o2`, `(equal? o1 o2)` implies `(= (equal?-hash o1) (equal?-hash o2))`.

For example:

```scheme
> (equal?-hash (list 1 2 3))
442438567
```

## Weak references

The garbage collector is responsible for reclaiming objects that are no longer
needed by the program. This is done by analyzing the reachability graph of all
objects from the roots (i.e. the global variables, the runnable threads,
permanently allocated objects such as procedures defined in a compiled file,
nonexecutable wills, etc). If a root or a reachable object `X` contains a
reference to an object `Y` then `Y` is reachable. As a general rule, unreachable
objects are reclaimed by the garbage collector.

There are two types of references: strong references and weak references. Most
objects, including pairs, vectors, records and closures, contain strong
references. An object `X` is strongly reachable if there is a path from the
roots to `X` that traverses only strong references. Weak references only occur
in wills and tables. There are two types of weak references: will-weak
references and table-weak references. If all paths from the roots to an object
`Y` traverse at least one table-weak reference, then `Y` will be reclaimed by
the garbage collector. The will-weak references are used for finalization and
are explained in the next section.

### Wills

The following procedures implement the `will` data type. Will objects provide
support for finalization. A will is an object that contains a will-weak
reference to a testator object (the object attached to the will), and a strong
reference to an action procedure which is a one parameter procedure which is
called when the will is executed.

#### make-will
#### will?
#### will-testator
#### will-execute!

```scheme
(make-will testator action) ;; procedure
(will? obj) ;; procedure
(will-testator will) ;; procedure
(will-execute! will) ;; procedure
```

The `make-will` procedure creates a will object with the given `testator` object
and `action` procedure. The `will?` procedure tests if `obj` is a will object.
The `will-testator` procedure gets the `testator` object attached to the `will`.
The `will-execute!` procedure executes `will`.

A will becomes executable when its `testator` object is not strongly reachable
(i.e. the `testator` object is either unreachable or only reachable using paths
from the roots that traverse at least one weak reference). Some objects,
including symbols, small exact integers (fixnums), booleans and characters, are
considered to be always strongly reachable.

When the runtime system detects that a will has become executable the current
computation is interrupted, the will's `testator` is set to `#f` and the will's
`action` procedure is called with the will's `testator` as the sole argument.
Currently only the garbage collector detects when wills become executable but
this may change in future versions of Gambit (for example the compiler could
perform an analysis to infer will executability at compile time). The garbage
collector builds a list of all executable wills. Shortly after a garbage
collection, the `action` procedures of these wills will be called. The link from
the will to the `action` procedure is severed when the `action` procedure is
called.

Note that the `testator` object will not be reclaimed during the garbage
collection that determined executability of the will. It is only when an object
is not reachable from the roots that it is reclaimed by the garbage collector.

A remarkable feature of wills is that an `action` procedure can "resurrect" an
object. An `action` procedure could for example assign the `testator` object to
a global variable or create a new will with the same `testator` object.

For example:

```scheme
> (define a (list 123))
> (set-cdr! a a) ; create a circular list
> (define b (vector a))
> (define c #f)
> (define w
    (let ((obj a))
      (make-will obj
                 (lambda (x) ; x will be eq? to obj
                   (display "executing action procedure")
                   (newline)
                   (set! c x)))))
> (will? w)
#t
> (car (will-testator w))
123
> (##gc)
> (set! a #f)
> (##gc)
> (set! b #f)
> (##gc)
executing action procedure
> (will-testator w)
#f
> (car c)
123
```

## Tables

The following procedures implement the `table` data type. Tables are heterogenous
structures whose elements are indexed by keys which are arbitrary objects.
Tables are similar to association lists but are abstract and the access time for
large tables is typically smaller. Each key contained in the table is bound to a
value. The length of the table is the number of key/value bindings it contains.
New key/value bindings can be added to a table, the value bound to a key can be
changed, and existing key/value bindings can be removed.

The references to the keys can either be all strong or all table-weak and the
references to the values can either be all strong or all table-weak. The garbage
collector removes key/value bindings from a table when 1) the key is a
table-weak reference and the key is unreachable or only reachable using paths
from the roots that traverse at least one table-weak reference, or 2) the value
is a table-weak reference and the value is unreachable or only reachable using
paths from the roots that traverse at least one table-weak reference. Key/value
bindings that are removed by the garbage collector are reclaimed immediately.

Although there are several possible ways of implementing tables, the current
implementation uses hashing with open-addressing. This is space efficient and
provides constant-time access. Hash tables are automatically resized to maintain
the load within specified bounds. The load is the number of active entries (the
length of the table) divided by the total number of entries in the hash table.

Tables are parameterized with a key comparison procedure. By default the
`equal?` procedure is used, but `eq?`, `eqv?`, `string=?`, `string-ci=?`, or a
user defined procedure can also be used. To support arbitrary key comparison
procedures, tables are also parameterized with a hashing procedure accepting a
key as its single parameter and returning a fixnum result. The hashing procedure
hash must be consistent with the key comparison procedure `test`, that is, for
any two keys `k1` and `k2` in the table, `(test k1 k2)` implies `(= (hash k1)
(hash k2))`. A default hashing procedure consistent with the key comparison
procedure is provided by the system. The default hashing procedure generally
gives good performance when the key comparison procedure is `eq?`, `eqv?`,
`equal?`, `string=?`, and `string-ci=?`. However, for user defined key
comparison procedures, the default hashing procedure always returns 0. This
degrades the performance of the table to a linear search.

Tables can be compared for equality using the `equal?` procedure. Two tables `X`
and `Y` are considered equal by `equal?` when they have the same weakness
attributes, the same key comparison procedure, the same hashing procedure, the
same length, and for all the keys `k` in `X`, `(equal? (table-ref X k)
(table-ref Y k))`.

#### make-table

```scheme
(make-table [size: size]
            [init: init]
            [weak-keys: weak-keys]
            [weak-values: weak-values]
            [test: test]
            [hash: hash]
            [min-load: min-load]
            [max-load: max-load]) ;; procedure
```

The procedure `make-table` returns a new table. The optional keyword parameters
specify various parameters of the table.

The `size` parameter is a nonnegative exact integer indicating the expected length
of the table. The system uses `size` to choose an appropriate initial size of the
hash table so that it does not need to be resized too often.

The `init` parameter indicates a value that is associated to keys that are not in
the table. When `init` is not specified, no value is associated to keys that are
not in the table.

The `weak-keys` and `weak-values` parameters are extended booleans indicating
respectively whether the keys and values are table-weak references (true) or
strong references (false). By default the keys and values are strong references.

The `test` parameter indicates the key comparison procedure. The default key
comparison procedure is `equal?`. The key comparison procedures `eq?`, `eqv?`,
`equal?`, `string=?`, and `string-ci=?` are special because the system will use
a reasonably good hash procedure when none is specified.

The `hash` parameter indicates the hash procedure. This procedure must accept a
single key parameter, return a fixnum, and be consistent with the key comparison
procedure. When `hash` is not specified, a default hash procedure is used. The
default hash procedure is reasonably good when the key comparison procedure is
`eq?`, `eqv?`, `equal?`, `string=?`, or `string-ci=?`.

The `min-load` and `max-load` parameters are real numbers that indicate the
minimum and maximum load of the table respectively. The table is resized when
adding or deleting a key/value binding would bring the table's load outside of
this range. The `min-load` parameter must be no less than `0.05` and the
`max-load` parameter must be no greater than `0.95`. Moreover the difference
between `min-load` and `max-load` must be at least `0.20`. When `min-load` is
not specified, the value `0.45` is used. When `max-load` is not specified, the
value `0.90` is used.

For example:

```scheme
> (define t (make-table))
> (table? t)
#t
> (table-length t)
0
> (table-set! t (list 1 2) 3)
> (table-set! t (list 4 5) 6)
> (table-ref t (list 1 2))
3
> (table-length t)
2
```

#### table?

```scheme
(table? obj) ;; procedure
```

The procedure `table?` returns `#t` when `obj` is a table and `#f` otherwise.

For example:

```scheme
> (table? (make-table))
#t
> (table? 123)
#f
```

#### table-length

```scheme
(table-length table) ;; procedure
```

The procedure `table-length` returns the number of key/value bindings contained
in the table `table`.

For example:

```scheme
> (define t (make-table weak-keys: #t))
> (define x (list 1 2))
> (define y (list 3 4))
> (table-set! t x 111)
> (table-set! t y 222)
> (table-length t)
2
> (table-set! t x)
> (table-length t)
1
> (##gc)
> (table-length t)
1
> (set! y #f)
> (##gc)
> (table-length t)
0
```

```scheme
(table-ref table key [default]) ;; procedure
```

The procedure `table-ref` returns the value bound to the object `key` in the
table `table`. When `key` is not bound and `default` is specified, `default` is
returned. When `default` is not specified but an `init` parameter was specified
when table was created, `init` is returned. Otherwise an unbound-key-exception
object is raised.

For example:

```scheme
> (define t1 (make-table init: 999))
> (table-set! t1 (list 1 2) 3)
> (table-ref t1 (list 1 2))
3
> (table-ref t1 (list 4 5))
999
> (table-ref t1 (list 4 5) #f)
#f
> (define t2 (make-table))
> (table-ref t2 (list 4 5))
*** ERROR IN (console)@7.1 -- Unbound key
(table-ref '#<table #2> '(4 5))
```

#### table-set!

```scheme
(table-set! table key [value]) ;; procedure
```

The procedure `table-set!` binds the object `key` to `value` in the table
`table`. When `value` is not specified, if table contains a binding for `key`
then the binding is removed from table. The procedure `table-set!` returns an
unspecified value.

For example:

```scheme
> (define t (make-table))
> (table-set! t (list 1 2) 3)
> (table-set! t (list 4 5) 6)
> (table-set! t (list 4 5))
> (table-set! t (list 7 8))
> (table-ref t (list 1 2))
3
> (table-ref t (list 4 5))
*** ERROR IN (console)@7.1 -- Unbound key
(table-ref '#<table #2> '(4 5))
```

#### table-search

```scheme
(table-search proc table) ;; procedure
```

The procedure `table-search` searches the table `table` for a key/value binding
for which the two parameter procedure `proc` returns a non false result. For
each key/value binding visited by `table-search` the procedure `proc` is called
with the `key` as the first parameter and the `value` as the second parameter.
The procedure `table-search` returns the first non false value returned by
`proc`, or `#f` if `proc` returned `#f` for all key/value bindings in `table`.

The order in which the key/value bindings are visited is unspecified and may
vary from one call of `table-search` to the next. While a call to `table-search`
is being performed on `table`, it is an error to call any of the following
procedures on `table`: `table-ref`, `table-set!`, `table-search`,
`table-for-each`, `table-copy`, `table-merge`, `table-merge!`, and
`table->list`. It is also an error to compare with `equal?` (directly or
indirectly with `member`, `assoc`, `table-ref`, etc.) an object that contains
`table`. All these procedures may cause `table` to be reordered and resized.
This restriction allows a more efficient iteration over the key/value bindings.

For example:

```scheme
> (define square (make-table))
> (table-set! square 2 4)
> (table-set! square 3 9)
> (table-search (lambda (k v) (and (odd? k) v)) square)
9
```

#### table-for-each

```scheme
(table-for-each proc table) ;; procedure
```

The procedure `table-for-each` calls the two parameter procedure `proc` for each
key/value binding in the table `table`. The procedure `proc` is called with the
`key` as the first parameter and the `value` as the second parameter. The
procedure `table-for-each` returns an unspecified value.

The order in which the key/value bindings are visited is unspecified and may
vary from one call of `table-for-each` to the next. While a call to
`table-for-each` is being performed on `table`, it is an error to call any of
the following procedures on `table`: `table-ref`, `table-set!`, `table-search`,
`table-for-each`, and `table->list`. It is also an error to compare with
`equal?` (directly or indirectly with `member`, `assoc`, `table-ref`, etc.) an
object that contains `table`. All these procedures may cause `table` to be
reordered and resized. This restriction allows a more efficient iteration over
the key/value bindings.

For example:

```scheme
> (define square (make-table))
> (table-set! square 2 4)
> (table-set! square 3 9)
> (table-for-each (lambda (k v) (write (list k v)) (newline)) square)
(2 4)
(3 9)
```

#### table->list

```scheme
(table->list table) ;; procedure
```

The procedure `table->list` returns an association list containing the key/value
bindings in the table `table`. Each key/value binding yields a pair whose `car`
field is the `key` and whose `cdr` field is the `value` bound to that `key`. The
order of the bindings in the list is unspecified.

For example:

```scheme
> (define square (make-table))
> (table-set! square 2 4)
> (table-set! square 3 9)
> (table->list square)
((3 . 9) (2 . 4))
```

#### list->table

```scheme
(list->table list [size: size]
                  [init: init]
                  [weak-keys: weak-keys]
                  [weak-values: weak-values]
                  [test: test]
                  [hash: hash]
                  [min-load: min-load]
                  [max-load: max-load]) ;; procedure
```

The procedure `list->table` returns a new table containing the key/value
bindings in the association list `list`. The optional keyword parameters specify
various parameters of the table and have the same meaning as for the
`make-table` procedure.

Each element of `list` is a pair whose `car` field is a `key` and whose `cdr`
field is the `value` bound to that `key`. If a key appears more than once in
`list` (tested using the table's key comparison procedure) it is the first
key/value binding in list that has precedence.

For example:

```scheme
> (define t (list->table '((b . 2) (a . 1) (c . 3) (a . 4))))
> (table->list t)
((a . 1) (b . 2) (c . 3))
```

#### unbound-key-exception?
#### unbound-key-exception-procedure
#### unbound-key-exceptionarguments

```scheme
(unbound-key-exception? obj) ;; procedure
(unbound-key-exception-procedure exc) ;; procedure
(unbound-key-exception-arguments exc) ;; procedure
```

Unbound-key-exception objects are raised by the procedure `table-ref` when the
key does not have a binding in the table. The parameter `exc` must be an
unbound-key-exception object.

The procedure `unbound-key-exception?` returns `#t` when `obj` is a
unbound-key-exception object and `#f` otherwise.

The procedure `unbound-key-exception-procedure` returns the procedure that
raised `exc`.

The procedure `unbound-key-exception-arguments` returns the list of arguments of
the procedure that raised `exc`.

For example:

```scheme
> (define t (make-table))
> (define (handler exc)
    (if (unbound-key-exception? exc)
        (list (unbound-key-exception-procedure exc)
              (unbound-key-exception-arguments exc))
        'not-unbound-key-exception))
> (with-exception-catcher
    handler
    (lambda () (table-ref t '(1 2))))
(#<procedure #2 table-ref> (#<table #3> (1 2)))
```

#### table-copy

```scheme
(table-copy table) procedure
```

The procedure `table-copy` returns a new table containing the same key/value
bindings as `table` and the same table parameters (i.e. hash procedure, key
comparison procedure, key and value weakness, etc).

For example:

```scheme
> (define t (list->table '((b . 2) (a . 1) (c . 3))))
> (define x (table-copy t))
> (table-set! t 'b 99)
> (table->list t)
((a . 1) (b . 99) (c . 3))
> (table->list x)
((a . 1) (b . 2) (c . 3))
```

#### table-merge!

```scheme
(table-merge! table1 table2 [table2-takes-precedence?]) ;; procedure
```

The procedure `table-merge!` returns `table1` after the key/value bindings
contained in `table2` have been added to it. When a key exists both in `table1`
and `table2`, then the parameter `table2-takes-precedence?` indicates which
binding will be kept (the one in `table1` if `table2-takes-precedence?` is
false, and the one in `table2` otherwise). If `table2-takes-precedence?` is not
specified the binding in `table1` is kept.

For example:

```scheme
> (define t1 (list->table '((a . 1) (b . 2) (c . 3))))
> (define t2 (list->table '((a . 4) (b . 5) (z . 6))))
> (table->list (table-merge! t1 t2))
((a . 1) (b . 2) (c . 3) (z . 6))
> (define t1 (list->table '((a . 1) (b . 2) (c . 3))))
> (define t2 (list->table '((a . 4) (b . 5) (z . 6))))
> (table->list (table-merge! t1 t2 #t))
((a . 4) (b . 5) (c . 3) (z . 6))
```

#### table-merge

```scheme
(table-merge table1 table2 [table2-takes-precedence?]) ;; procedure
```

The procedure `table-merge` returns a copy of `table1` (created with
`table-copy`) to which the key/value bindings contained in `table2` have been
added using `table-merge!`. When a key exists both in `table1` and `table2`,
then the parameter `table2-takes-precedence?` indicates which binding will be
kept (the one in `table1` if `table2-takes-precedence?` is false, and the one in
`table2` otherwise). If `table2-takes-precedence?` is not specified the binding
in `table1` is kept.

For example:

```scheme
> (define t1 (list->table '((a . 1) (b . 2) (c . 3))))
> (define t2 (list->table '((a . 4) (b . 5) (z . 6))))
> (table->list (table-merge t1 t2))
((a . 1) (b . 2) (c . 3) (z . 6))
> (table->list (table-merge t1 t2 #t))
((a . 4) (b . 5) (c . 3) (z . 6))
```
