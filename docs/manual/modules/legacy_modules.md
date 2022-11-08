---
title: Legacy modules
---

The legacy way of modularizing code, which was popular up to R5RS, is still
supported by Gambit. It consists of using the `load` procedure and the `include`
form. We discuss it first to introduce some useful terms and explain the
shortcomings of this modularization approach.

#### load

```scheme
(load path) ;;procedure
```

The `load` procedure's `path` argument, a string, specifies the location in the
file system of a file to load. Loading a file executes the code contained in the
file, which is either source code or compiled code (a dynamically loadable
object file created by the Gambit Scheme compiler, see the procedure
[`compile-file`](/manual/gsc/procedures/#compile-file)). When `path` has no
extension the `load` procedure first attempts to load the file with no extension
as a Scheme source file. If that file doesn’t exist it will search for both a
source file and an object file. The object file's path is obtained by adding to
`path` a `.on` extension with the highest consecutive version number starting
with 1. The source file's path is obtained by adding to path the file extensions
`.sld`, `.scm` and `.six` (the first found is the source file). If both a source
file and an object file exist, then the one with the latest modification time is
loaded. Otherwise the file that is found is loaded. When path has an extension,
the `load` procedure will only attempt to load the file with that specific
extension. After executing the code contained in the file, the `load` procedure
returns the path of the file that was loaded.

When a source code file is loaded its extension is used to determine how it is
parsed, unless the file's first line is a special script line (see [Scheme
scripts](/manual/gsi/scheme_scripts)). When the extension is different from
`.six` the content of the file is parsed using the normal Scheme prefix syntax.
When the extension is `.six` the content of the file is parsed using the Scheme
infix syntax extension (see [Scheme infix syntax
extension](/manual/lexical_syntax_and_readtables/scheme_infix_syntax_extension)).

Due to operating system limitations, loading a given `.on` object file more than
once in the same process is not supported. It is possible however to recompile
the source code file to create a new `.om` object file with `m > n` and `load`
that object file.

For example:

```scheme
$ cat my-mod.scm
(define (double x) (* x 2))
(display "my-mod has finished loading!!!\n")
$ gsi
Gambit v4.9.4

> (load "my-mod")
my-mod has finished loading!!!
"/Users/feeley/gambit/doc/my-mod.scm"
> (double 21)
42
> (load "my-mod.scm")
my-mod has finished loading!!!
"/Users/feeley/gambit/doc/my-mod.scm"
> ,q
$ gsc my-mod
$ gsi
Gambit v4.9.4

> (load "my-mod")
my-mod has finished loading!!!
"/Users/feeley/gambit/doc/my-mod.o1"
> (double 21)
42
> (load "my-mod")
*** ERROR IN (console)@3.1 -- Can't load a given object file more than once
(load "my-mod")
1>
```

Note that any macro definition in the loaded file is local to the file and is
not visible from the REPL or other files that loaded this file. The `include`
form can be used to access the macros defined in another file.

#### include
#### ##include
```scheme
(include path) ;; special form
(##include path) ;; special form
```

The `path` argument must be a string specifying the location of an existing file
containing Scheme source code. Relative paths are relative to the file that
contains the include form. The include special form splices the content of the
specified source file. This form can only appear where a define form is
acceptable, i.e. at top level or in the body of a binding form.

For example:

```scheme
$ cat my-defs.scm
(define-macro (double x) `(* ,x 2))
(define (quad y) (double (double y)))
(display "howdy!\n")
$ cat my-includer.scm 
(define (f x)
  (include "my-defs.scm")
  (+ 1 (quad x)))
$ gsi
Gambit v4.9.4

> (load "my-includer")
"/Users/feeley/udem-dlteam/gambit/my-includer.scm"
> (f 10)
howdy!
41
> (f 20)
howdy!
81
```

With legacy modularization, the code that implements the module's functionality
is put in a source code file and this module is accessed by other code by using
a `load` or `include` of that file. Here is an example of an `angle0` module
that is used by an `app0` main program:

```scheme
;;;---------------------------------------- file: angle0/angle0.scm
(define factor (/ (atan 1) 45))
(define (deg->rad x) (* x factor))
(define (rad->deg x) (/ x factor))

;;;---------------------------------------- file: app0.scm
(load "angle0/angle0.scm")   ;; or (include "angle0/angle0.scm")
(println "90 degrees is " (deg->rad 90) " radians")

;; run with:  gsi app0.scm
```

## Issues

This modularization approach has a number of issues:

- It hinders code sharing among different programs and users because a shared
  module's location in the filesystem must be known to all modules loading or
  including it. In the above example the path "angle0/angle0.scm" is relative so
  the `load` procedure will resolve the path incorrectly if the program executes
  `(current-directory "...")` before calling `load`.
- When a module is needed by more than one other module there will be code
  duplication, redundant evaluation/compilation, and probably incorrect
  execution if the module has side effects that should only happen once
  (displaying a message, opening a database on the filesystem, initializing the
  module's state, etc). Moreover, when the module has been compiled to an object
  file it can't be loaded more than once.
- All the definitions of a module will be put in the global environment
  (including top level macro definitions when using a top level include but not
  when using `load`). This pollutes the global environment with definitions that
  were not intended to be exported by the module's designer, such as the
  variable `factor` in the above example that is only meant to be used by the
  `deg->rad` and `rad->deg` procedures. Other modules may also need a `factor`
  variable internally, for instance to convert distances from the metric to the
  english system. Nothing prevents such accidental clashes.
