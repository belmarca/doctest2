---
title: Compiling modules
---

# Compiling modules

When `gsc` finds a command line argument that is the name of a module found on
the list of module search order directories (after an automatic installation if
that is appropriate) that module's main source code file will be compiled.

When a dynamic compilation is requested (which is the default compilation mode
and when the command line option `-dynamic` is used) the compiler will compile
for the selected target the main source code file to a target file and a
dynamically loadable object file with a `.o1` extension. These files will be
created in a directory next to the module's main source code file, with the same
name stripped of it's extension and suffixed with the Gambit version and the
target name. This naming strategy aims to avoid loading the compiled file in an
inappropriate context. The module loading algorithm knows it should check this
directory to find a compiled version of the module.

For example:

```shell
$ mkdir lib1 lib2
$ cat > lib1/lib1.sld
(define-library (lib1)
  (export fact)
  (import (scheme base) (scheme write))
  (begin
    (define (fact n) (if (<= n 1) 1 (* n (fact (- n 1)))))
    (display "lib1 loaded\n")))
$ cat > lib2/lib2.sld
(define-library (lib2)
  (import (lib1) (scheme base) (scheme write))
  (begin
    (display
     (cond-expand
       ((compilation-target C)   "lib2 compiled to C\n")
       ((compilation-target (_)) "lib2 interpreted\n")
       (else                     "lib2 compiled to other\n")))
    (display (fact 10))
    (newline)))
$ gsi . lib2        # loads lib1.sld and lib2.sld
lib1 loaded
lib2 interpreted
3628800
lib1
$ tree --charset=ascii --noreport lib1 lib2
`-- lib1.sld
lib2
`-- lib2.sld
$ gsc . lib1 lib2   # compile lib1.sld and lib2.sld using C target
$ gsi . lib2        # loads generated lib1.o1 and lib2.o1
lib1 loaded
lib2 compiled to C
3628800
$ gsc -target js . lib1 lib2   # also compile them for js target
$ tree --charset=ascii --noreport lib1 lib2
lib1
|-- lib1.sld
|-- lib1@gambit409003@C
|   |-- lib1.c
|   `-- lib1.o1
`-- lib1@gambit409003@js
    |-- lib1.js
    `-- lib1.o1
lib2
|-- lib2.sld
|-- lib2@gambit409003@C
|   |-- lib2.c
|   `-- lib2.o1
`-- lib2@gambit409003@js
    |-- lib2.js
    `-- lib2.o1
```

To create an executable program from a set of non-legacy modules it is important
to use the `-nopreload` linking option when linking so that the modules will be
initialized in an order that is consistent with the module dependencies. If the
default `-preload` linking option is used some modules may be initialized out of
order, leading to incorrect execution.

Here is an example that extends the previous example:

```shell
$ gsc -exe -nopreload . lib1/lib1.sld lib2/lib2.sld
$ lib2/lib2
lib1 loaded
lib2 compiled to C
3628800
$ gsc -target js -exe -nopreload . lib1/lib1.sld lib2/lib2.sld
$ lib2/lib2
lib1 loaded
lib2 compiled to other
3628800
```
