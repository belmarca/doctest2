# Records

```scheme
(define-structure name field ...) ;; special form
```

Record data types similar to Pascal records and C struct types can be defined
using the `define-structure` special form. The identifier name specifies the name
of the new data type. The structure name is followed by `k` identifiers naming
each field of the record. The `define-structure` expands into a set of definitions
of the following procedures:

- `make-name` – A `k` argument procedure which constructs a new record from the
value of its `k` fields.
- `name?` – A procedure which tests if its single argument is of the given
record type.
- `name-field` – For each field, a procedure taking as its single argument a
value of the given record type and returning the content of the corresponding
field of the record.
- `name-field-set!` – For each field, a two argument procedure taking as its
first argument a value of the given record type. The second argument gets
assigned to the corresponding field of the record and the void object is
returned.

Record data types have a printed representation that includes the name of the
type and the name and value of each field. Record data types can not be read by
the read procedure.

For example:

```scheme
> (define-structure point x y color)
> (define p (make-point 3 5 'red))
> p
#<point #2 x: 3 y: 5 color: red>
> (point-x p)
3
> (point-color p)
red
> (point-color-set! p 'black)
> p
#<point #2 x: 3 y: 5 color: black>
```
