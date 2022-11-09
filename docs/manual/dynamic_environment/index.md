---
title: Dynamic environment
---

# Dynamic environment

The dynamic environment is the structure which allows the system to find the
value returned by the standard procedures `current-input-port` and
`current-output-port`. The standard procedures `with-input-from-file` and
`with-output-to-file` extend the dynamic environment to produce a new dynamic
environment which is in effect for the dynamic extent of the call to the thunk
passed as their last argument. These procedures are essentially special purpose
dynamic binding operations on hidden dynamic variables (one for
`current-input-port` and one for `current-output-port`). Gambit generalizes this
dynamic binding mechanism to allow the user to introduce new dynamic variables,
called parameter objects, and dynamically bind them. The parameter objects
implemented by Gambit are compatible with the specification of the "Parameter
objects SRFI" ([SRFI 39](https://srfi.schemers.org/srfi-39/)).

One important issue is the relationship between the dynamic environments of the
parent and child threads when a thread is created. Each thread has its own
dynamic environment that is accessed when looking up the value bound to a
parameter object by that thread. When a thread’s dynamic environment is extended
it does not affect the dynamic environment of other threads. When a thread is
created it is given a dynamic environment whose bindings are inherited from the
parent thread. In this inherited dynamic environment the parameter objects are
bound to the same cells as the parent's dynamic environment (in other words an
assignment of a new value to a parameter object is visible in the other thread).

Another important issue is the interaction between the `dynamic-wind` procedure
and dynamic environments. When a thread creates a continuation, the thread's
dynamic environment and the `dynamic-wind` stack are saved within the
continuation (an alternate but equivalent point of view is that the
`dynamic-wind` stack is part of the dynamic environment). When this continuation
is invoked the required `dynamic-wind` before and after thunks are called and
the saved dynamic environment is reinstated as the dynamic environment of the
current thread. During the call to each required `dynamic-wind` before and after
thunk, the dynamic environment and the `dynamic-wind` stack in effect when the
corresponding `dynamic-wind` was executed are reinstated. Note that this
specification precisely defines the semantics of calling
`call-with-current-continuation` or invoking a continuation within a before or
after thunk. The semantics are well defined even when a continuation created by
another thread is invoked. Below is an example exercising the subtleties of this
semantics.

```scheme
(with-output-to-file
 "foo"
 (lambda ()
   (let ((k (call-with-current-continuation
             (lambda (exit)
               (with-output-to-file
                "bar"
                (lambda ()
                  (dynamic-wind
                   (lambda ()
                     (write '(b1))
                     (force-output))
                   (lambda ()
                     (let ((x (call-with-current-continuation
                               (lambda (cont) (exit cont)))))
                       (write '(t1))
                       (force-output)
                       x))
                   (lambda ()
                     (write '(a1))
                     (force-output)))))))))
     (if k
         (dynamic-wind
          (lambda ()
            (write '(b2))
            (force-output))
          (lambda ()
            (with-output-to-file
             "baz"
             (lambda ()
               (write '(t2))
               (force-output)
               ; go back inside (with-output-to-file "bar" ...)
               (k #f))))
          (lambda ()
            (write '(a2))
            (force-output)))))))
```

The following actions will occur when this code is executed: `(b1)(a1)` is
written to "bar", `(b2)` is then written to "foo", `(t2)` is then written to
"baz", `(a2)` is then written to "foo", and finally `(b1)(t1)(a1)` is written to
"bar".

#### make-parameter

```scheme
(make-parameter obj [set-filter [get-filter]]) ;; procedure
```

The dynamic environment is composed of two parts: the local dynamic environment
and the global dynamic environment. There is a single global dynamic
environment, and it is used to lookup parameter objects that can’t be found in
the local dynamic environment.

The `make-parameter` procedure returns a new parameter object. The `set-filter`
argument is a one argument "set" conversion procedure. The `get-filter` argument
is a one argument "get" conversion procedure. If they are not specified the
conversion procedures default to the identity function.

The global dynamic environment is updated to associate the parameter object to a
new cell. The initial content of the cell is the result of applying the "set"
conversion procedure to `obj`.

A parameter object is a procedure which accepts zero or one argument. The cell
bound to a particular parameter object in the dynamic environment is accessed by
calling the parameter object. When no argument is passed, the value returned is
the result of applying the "get" conversion procedure to the content of the
cell. When one argument is passed the content of the cell is updated with the
result of applying the parameter object's "set" conversion procedure to the
argument. Note that the conversion procedures can be used for guaranteeing the
type of the parameter object's binding and/or to perform some conversion of the
value.

For example:

```scheme
> (define radix (make-parameter 10))
> (radix)
10
> (radix 2)
> (radix)
2
> (define prompt
    (make-parameter
      123
      (lambda (x)
        (if (string? x)
            x
            (object->string x)))))
> (prompt)
"123"
> (prompt "$")
> (prompt)
"$"
> (define p
    (make-parameter
      100
      (lambda (val) ;; set filter
        (pp (list val: val))
        (list 0 val))
      (lambda (state) ;; get filter
        (pp (list state: state))
        (set-car! state (+ 1 (car state)))
        (+ (car state) (cadr state)))))
(val: 100)
> (p)
(state: (0 100))
101
> (p)
(state: (1 100))
102
> (p)
(state: (2 100))
103
> (p 555)
(val: 555)
> (p)
(state: (0 555))
556
> (p)
(state: (1 555))
557
> (define write-shared
    (make-parameter
      #f
      (lambda (x)
        (if (boolean? x)
            x
            (error "only booleans are accepted by write-shared")))))
> (write-shared 123)
*** ERROR IN ##make-parameter -- only booleans are accepted by write-shared
```

#### parameterize

```scheme
(parameterize ((procedure value) ...) body) ;; special form
```

The `parameterize` form, evaluates all procedure and value expressions in an
unspecified order. All the procedure expressions must evaluate to procedures,
either parameter objects or procedures accepting zero and one argument. Then,
for each procedure `p` and in an unspecified order:

- If `p` is a parameter object a new cell is created, initialized, and bound to the
parameter object in the local dynamic environment. The value contained in the
cell is the result of applying the parameter object's "set" conversion procedure
to `value`. The resulting dynamic environment is then used for processing the
remaining bindings (or the evaluation of body if there are no other bindings).
- Otherwise `p` will be used according to the following protocol: we say that the
call `(p)` "gets `p`'s value" and that the call `(p x)` "sets `p`'s value to x". First,
the parameterize form gets `p`'s value and saves it in a local variable. It then
sets `p`'s value to value. It then processes the remaining bindings (or evaluates
body if there are no other bindings). Then it sets `p`'s value to the saved value.
These steps are performed in a `dynamic-wind` so that it is possible to use
continuations to jump into and out of the body (i.e. the `dynamic-wind`'s before
thunk sets `p`'s value to value and the after thunk sets `p`'s value to the saved
value). The result(s) of the `parameterize` form are the result(s) of the `body`.

!!! note 
    Using procedures instead of parameter objects may lead to unexpected results
    in multithreaded programs because the before and after thunks of the
    `dynamic-wind` are not called when control switches between threads.

For example:

```scheme
> (define radix (make-parameter 2))
> (define prompt
    (make-parameter
      123
      (lambda (x)
        (if (string? x)
            x
            (object->string x)))))
> (radix)
2
> (parameterize ((radix 16)) (radix))
16
> (radix)
2
> (define (f n) (number->string n (radix)))
> (f 10)
"1010"
> (parameterize ((radix 8)) (f 10))
"12"
> (parameterize ((radix 8) (prompt (f 10))) (prompt))
"1010"
> (define p
    (let ((x 1))
      (lambda args
        (if (null? args) x (set! x (car args))))))
> (let* ((a (p))
         (b (parameterize ((p 2)) (list (p))))
         (c (p)))
    (list a b c))
(1 (2) 1)
```
