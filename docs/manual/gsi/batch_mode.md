---
title: Batch mode
---

In batch mode the command line arguments denote modules and files to execute,
REPL interactions to start (`-` option), and expressions to be evaluated (`-e`
option). Those options can be interspersed with the search directories, modules,
and files on the command line and can occur multiple times.

In addition to these options the command line may contain 3 types of
non-options: search directories, modules, and files.

`Search directories`
: Search directories are locations in the file system that are searched to resolve
references to modules. Any command line argument that ends with a path separator
or a `.` is treated as a search directory. By default the module search order is
initially `~~lib` (which contains builtin modules) followed by `~~userlib`
(which contains user installed modules and is typically the directory
`.gambit_userlib` in the user's home directory). Search directories on the
command line are added to the front of the search order, and thus take
precedence over the default module search order.

`Modules`
: Modules are either unversioned or versioned (managed by the `git` version-control
system). There are two flavors of versioned modules: hosted modules have a `git`
repository on a network accessible repository manager site such as `github.com`
and `gitlab.com`, and local modules have a `git` repository on the local file
system. Module names have a syntax similar to the paths used to identify files.
They consist of one or more non-empty parts separated by `/`. The last part may
end with a suffix of the form `@version`. Only the first part and version may
contain `.`, otherwise only the characters a-z, A-Z, 0-9, `-`, and `_` are
permitted. If there are at least 3 parts and the first part contains at least
one `.` and no `_`, then it refers to a hosted module (1st part = `host`, 2nd part
= `account`, 3rd part = `repository name`). For example
`github.com/gambit/hello@1.0` is a hosted module reference. Otherwise it refers
to a local versioned module or an unversioned module, for example `foobar` or
`A/B/C/D`.

`Files`
: Files are simple code containers located on the local file system. They are also
identified by a path. If a path is a valid module or file, it is interpreted as
a module. Note that a path with a last component containing an extension, such
as `.scm`, and no `@`, is always interpreted as a file.

The interpreter processes the command line arguments from left to right. Search
directories are added to the head of the module search order. Files are executed
using the load procedure. Modules are requested using the `##demand-module`
special form (this form is explained in Modules, but essentially it causes that
module to be searched in the module search order and executed once). The `-e`
option uses the eval procedure to evaluate expressions in the global interaction
environment. After this processing the interpreter exits.

The ports associated with `(current-input-port)`, `(current-output-port)` and
`(current-error-port)` initially refer respectively to the standard input
(`stdin`), standard output (`stdout`) and the standard error (`stderr`) of the
interpreter. This is true even in REPLs started with the `-` option. The usual
interaction channel is still used to read expressions and commands and to
display results. This makes it possible to use REPLs to debug programs which
read the standard input and write to the standard output, even when these have
been redirected.

Here is a sample use of the interpreter in batch mode, under UNIX:

```shell
$ cat h.scm
(display "hello") (newline)
$ cat w.six
display("world"); newline();
$ gsi h.scm - w.six -e "(pretty-print 1)(pretty-print 2)"
hello
> (define (display x) (write (reverse (string->list x))))
> ,c
(#\d #\l #\r #\o #\w)
1
2
$ gsi . h w   # add . to search order to load modules h and w
hello
world
```

