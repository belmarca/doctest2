---
title: R7RS compatible modules
---

# R7RS compatible modules

The R7RS Scheme standard specifies a modularization approach based on the
concept of library. A library is defined using the `define-library` form. This
form is implemented as a macro that expands into the constructs used by
primitive modules, in particular a `##namespace` declaration with a namespace
derived from the library's name so that all variables defined by the library are
in that namespace. With the `define-library` form the `angle3` module example
given previously can be written like this:

```scheme
;;;---------------------------------------- file: angle3.sld
(define-library (angle3)

  (export deg->rad rad->deg)

  (import (scheme base)
          (scheme inexact))

  (begin
    (define factor (/ (atan 1) 45))
    (define (deg->rad x) (* x factor))
    (define (rad->deg x) (/ x factor))))
```

For this library the expansion of the `define-library` form will contain a
`##namespace` declaration that causes the definition of the global variables
`angle3#factor`, `angle3#deg->rad`, and `angle3#rad->deg`. Meanwhile an `(import
(angle3))` in another library will generate a `##namespace` declaration that
maps uses of `deg->rad` and `rad->deg` to the global variables `angle3#deg->rad`
and `angle3#rad->deg` respectively (note that the unexported global variable
`factor` is not included in the generated `##namespace` declaration).

For more complex libraries whose code is split into multiple files it is
convenient to put all the files in a dedicated subdirectory. This is the
preferred filesystem structure for a library but the runtime system supports
both styles. The previous module could be structured like this instead:

```scheme
;;;---------------------------------------- file: angle3/angle3.sld
(define-library (angle3)

  (export deg->rad rad->deg)

  (import (scheme base)
          (scheme inexact))

  (include "angle3.scm")) ;; path is relative to angle3.sld file

;;;---------------------------------------- file: angle3/angle3.scm
(define factor (/ (atan 1) 45))
(define (deg->rad x) (* x factor))
(define (rad->deg x) (/ x factor))
```


## Identifying libraries

Each library is given a name so that it can be referred to in various contexts,
most notably in import forms and the interpreter's and compiler's command line.
The R7RS defines a library name as a list whose members are identifiers and
exact non-negative integers, for example `(widget)`, `(_hamt)`, `(scheme base)`,
and `(srfi 64)`.

The system maps these R7RS library names to module identifiers that are symbols
formed by concatenating the parts of the library name separated with `/`. The
library name and module name are interchangeable. Consequently, `(import
srfi/64)` and `(import _hamt)` are respectively equivalent to `(import (srfi
64))` and `(import (_hamt))`. Using the module name to identify libraries on the
command line is convenient as it avoids having to escape parentheses and spaces.

#### define-library

```scheme
(define-library name declaration ...) ;; special form
```

In a library definition `name` specifies the name of the library and
`declaration` is one of:

```scheme
(export <export spec> ...)
(import <import set> ...)
(begin <command or definition> ...)
(include <filename> ...)
(include-ci <filename> ...)
(include-library-declarations <filename> ...)
(cond-expand <cond expand features> ...)
(namespace <namespace>)
(cc-options <options> ...)
(ld-options <options> ...)
(ld-options-prelude <options> ...)
(pkg-config <options> ...)
(pkg-config-path <path> ...)
```

#### export

```scheme
(export <export spec> ...)
```

An export declaration specifies a list of identifiers which can be made visible
to other libraries or programs. An `<export spec>` takes one of the following
forms:

```scheme
<identifier>
(rename <identifier>1 <identifier>2)
```

In an `<export spec>`, an `<identifier>` names a single binding (variable or
macro) defined within or imported into the library, where the external name for
the export is the same as the name of the binding within the library. A `rename`
spec exports the binding defined within or imported into the library and named
by `<identifier>1` in each `(<identifier>1 <identifier>2)` pairing, using
`<identifier>2` as the external name.

#### import

```scheme
(import <import set> ...)
```

A library declares a dependency to another library with the `import`
declaration. The `(import <import set> ...)` form identifies the imported
library or libraries.

Each `<import set>` names a set of bindings from a library and possibly
specifies local names for the imported bindings. An `<import set>` takes one of
the following forms:

```scheme
<library name>
(only <import set> <identifier> ...)
(except <import set> <identifier> ...)
(prefix <import set> <identifier>)
(rename <import set> (<identifier>1 <identifier>2) ...)
```

In the first form, all of the identifiers in the named library's `export`
clauses are imported with the same names (or the exported names if exported with
`rename`). The additional `<import set>` forms modify this set as follows:

- `only` produces a subset of the given `<import set>` including only the listed
  identifiers (after any renaming). It is an error if any of the listed
  identifiers are not found in the original set.
- `except` produces a subset of the given `<import set>`, excluding the listed
  identifiers (after any renaming). It is an error if any of the listed
  identifiers are not found in the original set.
- `rename` modifies the given `<import set>`, replacing each instance of
  `<identifier>1` with `<identifier>2`. It is an error if any of the listed
  `<identifier>1`s are not found in the original set.
- `prefix` automatically renames all identifiers in the given `<import set>`,
  prefixing each with the specified `<identifier>`.


As an extension to the R7RS syntax it is allowed for a `<library name>` to contain
a trailing `@version` when the library is hosted in a `git` repository. The version
must match a tag of that repository and it indicates the specific library
version required. For example, `(import (github.com/gambit hello @1.0))` or
equivalently `(import github.com/gambit/hello@1.0)`. Note that the version
specifier is not separated with a `/` in the module name.

Another extension to the R7RS syntax when the library is hosted in a `git`
repository is the use of dots before the name of the library to indicate a
relative reference within the repository. The number of dots indicates the
number of parent hops. For example, in the library `(github.com/gambit hello
demo)` an `(import (.. hello))` will resolve to the `(github.com/gambit hello)`
library. A relative library reference should not contain an explicit `@version`
because the version is implicitly the same as the referring module.

#### begin
#### include
#### include-ci

```scheme
(begin <command or definition> ...)
(include <filename> ...)
(include-ci <filename> ...)
```

The `begin`, `include`, and `include-ci` declarations are used to specify the
body of the library. They have the same syntax and semantics as the
corresponding expression types. This form of `begin` is analogous to, but not
the same as, the two types of `begin` expressions.

#### include-library-declarations

```scheme
(include-library-declarations <filename> ...)
```

The `include-library-declarations` declaration is similar to `include` except
that the contents of the file are spliced directly into the current library
definition. This can be used, for example, to share the same export declaration
among multiple libraries as a simple form of library interface.


#### cond-expand

```scheme
(cond-expand <cond expand features> ...)
```

The `cond-expand` declaration has the same syntax and semantics as the
`cond-expand` expression type, except that it expands to spliced-in library
declarations rather than expressions enclosed in `begin`.


### Extensions to the R7RS library declarations

The `(namespace <namespace>)` declaration allows overriding the namespace used
for the library. This is mainly useful for system libraries to prevent namespace
prefixing using a `(namespace "")` declaration.

The remaining declarations are relevant to the `C` target and ignored otherwise.
They provide information, in the form of strings, to be passed to the compiler
options of the same name when this library is compiled:

```scheme
(cc-options <options> ...)
(ld-options <options> ...)
(ld-options-prelude <options> ...)
(pkg-config <options> ...)
(pkg-config-path <path> ...)
```

For example, a library could force the C compiler to generate machine code for
i386 with:

```scheme
(define-library (foo)
  (export bar)
  (import (scheme base))
  (cc-options "-march=i386") ;; request compilation for i386
  (begin (define (bar) 42)))
```
