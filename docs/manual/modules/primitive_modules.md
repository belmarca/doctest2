---
title: Primitive modules
---

## Module forms

#### ##demand-module
#### ##supply-module

```scheme
(##demand-module mod) ;; special form
(##supply-module mod) ;; special form
```

The `##demand-module` form offers a way to avoid the issues of multiple loading
and filesystem localization of modules. The sole parameter of this form is an
(unevaluated) symbol `mod` that identifies the module on which the module
containing the `##demand-module` depends. When a module `A` contains a
`(##demand-module B)`, Gambit's runtime system will ensure that module `B` is
loaded before module `A` is loaded. It also registers the module in a cache when
it is loaded so that it is loaded exactly once. In other words the
`##demand-module` form expresses the requirement that the current module needs
the functionality of another module. A module can contain multiple uses of
`##demand-module` and possibly more than once for a given module. The
`##demand-module` form can appear anywhere a define can appear. There is also a
related `##supply-module` form that should appear in the module to declare the
module's identity.

Gambit's runtime system searches for modules in various directories, by default
in `~~lib` then in `~~userlib` (which maps to `~/.gambit_userlib` by default).
These two directories are where builtin modules and user installed modules are
located respectively. The source code for a module `M` is searched, in each of
the module search order directories, first in `M/M.ext` and then in `M.ext`,
where `.ext` is one of the acceptable Scheme source code file extensions
(`.sld`, `.scm`, `.six`, etc). The list of module search order directories can
be extended with the `-:search=DIR` runtime option or by a command line argument
to `gsi` and `gsc` that ends with a path separator or a `.`.

With `##demand-module` and `##supply-module` the previous example can be
rewritten like this:

```scheme
;;;---------------------------------------- file: angle1/angle1.scm
(##supply-module angle1) ;; declare that this is the module angle1
(define factor (/ (atan 1) 45))
(define (deg->rad x) (* x factor))
(define (rad->deg x) (/ x factor))

;;;---------------------------------------- file: app1.scm
(##demand-module angle1) ;; declare dependency on module angle1
(println "90 degrees is " (deg->rad 90) " radians")

;; run with either:  gsi . app1.scm
;;              or:  gsi -:search=. app1.scm
;;
;; or install the angle1 module to avoid the . and -:search=.
```


#### namespace
#### ##namespace
#### import
#### ##import

```scheme
(namespace ...) ;; special form
(##namespace ...) ;; special form
(import module-ref) ;; special form
(##import module-ref) ;; special form
```

The `##namespace` form offers a way to avoid name clashes by specifying a
mapping between identifiers. The mapping it specifies has the same scope as a
macro definition: it applies to the rest of a source code file if it is at top
level, or applies to the rest of the body of a binding form if it is used in the
body of a binding form. The call `(##namespace ("foo#" a b))` specifies that a
reference to `a` becomes `foo#a` and a reference to `b` becomes `foo#b`. Here
`foo#` is the namespace. Finer control over the mapping is possible by using
aliases as in `(##namespace ("foo#" (a bar) b))` which maps `a` to `foo#bar` and
`b` to `foo#b`. Multiple namespace specifications can appear in the body of the
`##namespace` form. When no identifiers are specified, the mapping maps all
identifiers not containing `#` to the namespace. For example in the scope of
`(##namespace ("foo#"))` the reference `x` maps to `foo#x` and the reference
`bar#x` remains unchanged.

Given that modules are identified by a unique symbol, the global names defined
by a module `M` can be put in the namespace `M#` to avoid name clashes with
other modules. The source code of module `M` and the modules depending on `M`
can explicitly prefix the global names defined by `M` with `M#` or use a
`##namespace` form to make this prefixing implicit. By convention the namespace
definition for the identifiers exported by module `M` is specified in the source
code file `M#.scm` in the same directory as the `M.scm` file.

Using this convention and the include and `##namespace` forms, the previous
example can be rewritten like this:

```scheme
;;;---------------------------------------- file: angle2/angle2#.scm
(##namespace ("angle2#" deg->rad rad->deg))

;;;---------------------------------------- file: angle2/angle2.scm
(include "angle2#.scm")
(##namespace ("angle2#" factor))
(##supply-module angle2)
(define factor (/ (atan 1) 45))
(define (deg->rad x) (* x factor))
(define (rad->deg x) (/ x factor))

;;;---------------------------------------- file: app2.scm
(include "angle2/angle2#.scm")
(##demand-module angle2)
(println "90 degrees is " (deg->rad 90) " radians")
```

Note that the parameters of the two include forms are different, but this is
correct because the paths are relative to the file containing the include form.
However the module localization problem has been reintroduced for the file
`angle2/angle2#.scm`.

This problem can be solved using the `##import` form that combines the semantics
of the `include` and `##demand-module` forms. The call `(##import M)` will use
the module search order directories to locate the source code file of module M
and will expand to an include of the "hash" file `M#.ext` if it exists in the
same directory, and a `(##demand-module M)`.

In addition, a builtin module `gambit` exists that contains all the global names
exported by the runtime library. The gambit module's "hash" file `gambit#.scm`
contains a `##namespace` form that lists all the names exported by the runtime
library in an empty namespace:

```scheme
;;;---------------------------------------- file: ~~lib/gambit#.scm
(##namespace ("" define if quote set! cons car cdr + - * / ;; etc
```

Using the `gambit` module and the `##import` form, the previous example can be
rewritten like this:

```scheme
;;;---------------------------------------- file: angle3/angle3#.scm
(##namespace ("angle3#" deg->rad rad->deg))

;;;---------------------------------------- file: angle3/angle3.scm
(##namespace ("angle3#")) ;; map all identifiers to angle3# namespace
(##import gambit)         ;; except those defined by Gambit's RTS
(##supply-module angle3)
(define factor (/ (atan 1) 45))
(define (deg->rad x) (* x factor))
(define (rad->deg x) (/ x factor))

;;;---------------------------------------- file: app3.scm
(##import angle3)
(println "90 degrees is " (deg->rad 90) " radians")
```

In this example the `(##import angle3)` takes care of the namespace mapping and
the loading of `angle3.scm` because it is equivalent to:

```scheme
(begin
 (##include "angle3/angle3#.scm")
 (##demand-module angle3))
```

### Macros

In addition to procedures, a module `M` may export macros. The file `M#.scm` is the
designated place to put exported macro definitions. These macro definitions will
essentially be copied at the point where the `##import` of the module is done.
Macros that are needed strictly for the implementation of a module may be
defined in the file `M.scm` and these macro definitions will not be visible
elsewhere. Note that the macros defined with `define-macro` are not hygienic, so
the macro definition writer should take care to explicitly indicate what
identifiers resolve to using fully qualified identifiers (i.e. containing a `#`
sign).

To explain these issues, lets extend our example module in the following ways.
First we want the module to export the macros `sind` and `asind` that are like
the `sin` and `asin` procedures but use degrees instead of radians. Note that it
would be a better design for `sind` and `asind` to be procedures, but we'll
implement them as macros for the sake of the example. Second we want the
procedures `deg->rad` and `rad->deg` to check that their argument is a real
number using a `check-real` macro.

In a setting where name clashes are not an issue these macros can be defined as
follows:

```scheme
(define-macro (sind x) `(sin (deg->rad ,x)))
(define-macro (asind x) `(rad->deg (asin ,x)))
(define-macro (check-real x y)
  `(if (real? ,x) ,y (error "not real!")))
```

Name clashes will occur when the locations where these macros are called are in
the scope of new bindings for `sin`, `deg->rad`, `if`, `error`, etc which are
identifiers used in the expanded code. A name clash can also happen for the name
`define-macro` itself. To remove the possibility of name clashes the
`##namespace` form and fully qualified identifiers can be used. All the Gambit
special forms, such as `let`, `if`, and `define-macro`, have a fully qualified
version (`##let`, `##if`, and `##define-macro`). Gambit predefined procedures,
such as `sin`, `real?`, and `error`, don't necessarily have a fully qualified
version (some do and some don't) but an empty namespace definition in a `##let`
form can be used to avoid the clash, i.e. `(##let () (##namespace ("") sin))`
refers to the global variable `sin` whatever scope it is in. With these forms
our example can be written like this:

```scheme
;;;---------------------------------------- file: angle4/angle4#.scm
(##namespace ("angle4#" deg->rad rad->deg))
(##define-macro (sind x) `((##let () (##namespace ("")) sin)
                           (angle4#deg->rad ,x)))
(##define-macro (asind x) `(angle4#rad->deg
                            ((##let () (##namespace ("")) asin) ,x)))

;;;---------------------------------------- file: angle4/angle4.scm
(##namespace ("angle4#")) ;; map all identifiers to angle4# namespace
(##import gambit)         ;; except those defined by Gambit's RTS
(##supply-module angle4)
(##define-macro (check-real x y)
  `(##if ((##let () (##namespace ("")) real?) ,x)
         ,y
         ((##let () (##namespace ("")) error) "not real!")))
(define factor (/ (atan 1) 45))
(define (deg->rad x) (check-real x (* x factor)))
(define (rad->deg x) (check-real x (/ x factor)))

;;;---------------------------------------- file: app4.scm
(##import angle4)
(println "90 degrees is " (deg->rad 90) " radians")
(println "sind(90) is " (sind 90))
```
