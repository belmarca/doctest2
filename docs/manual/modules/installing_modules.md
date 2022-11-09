---
title: Installing modules
---

# Installing modules

When a module is imported, the processing of the `import` form must locate and
read the source code of the module at macro expansion time to determine which
names are exported and to what they are mapped. The list of module search
directories (`~~lib` followed by `~~userlib` by default) is searched to find the
module's source code. At execution time the same search algorithm is used to
locate and load the module, either in source code form or compiled form. The
`~~lib` directory is where the system's builtin modules are put when Gambit is
installed. The `~~userlib` directory is a convenient place where other modules
can be installed by the user because locating them does not require extending
the list of module search directories.

#### module-search-order-reset!
#### module-search-order-add!

```scheme
(module-search-order-reset!) ;; procedure
(module-search-order-add! dir) ;; procedure
```

The list of module search directories can be modified using the procedures
`module-search-order-reset!` and `module-search-order-add!` that respectively
clear the list and extend the list with the directory dir which must be a
string. The list can also be extended by using the `-:search=DIR` runtime option
or by a command line argument to `gsi` and `gsc` that ends with a path separator
or a `.` (see section `##demand-module` and `##supply-module` forms).

For example:

```scheme
$ cat foobar.sld
(define-library (foobar)
  (import (scheme write))
  (begin (display "foobar library has executed\n")))
$ gsi
Gambit v4.9.4

> (import (foobar))
*** ERROR IN (stdin)@1.9 -- Cannot find library (foobar)
> (module-search-order-add! ".")
> (import (foobar))
foobar library has executed
> ,q
$ gsi -:search=. foobar
foobar library has executed
$ gsi . foobar
foobar library has executed
```

#### module-whitelist-reset!
#### module-whitelist-add!

```scheme
(module-whitelist-reset!) ;; procedure
(module-whitelist-add! source) ;; procedure
```

When modules are installed it is done at the granularity of a package, which is
defined as a `git` repository possibly containing more than one module. For
example, if the hosted module `github.com/gambit/hello/demo` needs to be
installed it is all of the code at `github.com/gambit/hello` that is installed
(this includes the three modules `github.com/gambit/hello`,
`github.com/gambit/hello/demo`, and `github.com/gambit/hello/test`).

For convenience the runtime system will automatically install in the `~~userlib`
directory any hosted module that is from a trusted source. The whitelist of
trusted sources, which initially contains only `github.com/gambit`, can be
modified using the procedures `module-whitelist-reset!` and
`module-whitelist-add!` that respectively clear the list and extend the list
with the source source which must be a string. The list can also be extended
with the `-:whitelist=SOURCE` runtime option.

For example:

```scheme
$ gsi github.com/gambit/hello/demo  # auto-install of github.com/gambit/hello package
People customarily greet each other when they meet.
In English you can say: hello Bob, nice to see you!
In French you can say: bonjour Bob, je suis enchant�!
Demo source code: /Users/feeley/.gambit_userlib/github.com/gambit/hello/@/demo.scm
$ gsi github.com/feeley/roman/demo  # no auto-install because not on whitelist
*** ERROR IN ##main -- No such file or directory
(load "github.com/feeley/roman/demo")
$ gsi -:whitelist=github.com/feeley github.com/feeley/roman/demo
1 is I in roman numerals
2 is II in roman numerals
4 is IV in roman numerals
8 is VIII in roman numerals
16 is XVI in roman numerals
32 is XXXII in roman numerals
64 is LXIV in roman numerals
$ gsi github.com/feeley/roman/demo   # OK because module is now installed
1 is I in roman numerals
2 is II in roman numerals
4 is IV in roman numerals
8 is VIII in roman numerals
16 is XVI in roman numerals
32 is XXXII in roman numerals
64 is LXIV in roman numerals
$ gsi github.com/feeley/roman/test   # the test module was also installed
*** all tests passed out of a total of 19 tests
```

The use of the runtime option `-:whitelist=` (with no `SOURCE`) will disable the
automatic installation of modules, even from `github.com/gambit`.

For example:

```scheme
$ gsi -:whitelist= github.com/gambit/hello/demo
*** ERROR IN ##main -- No such file or directory
(load "github.com/gambit/hello/demo")
```

A manual management of packages is nevertheless possible with the `gsi` package
management operations. These are invoked with the command line options
`-install`, `-uninstall`, and `-update` which respectively install, uninstall
and update packages. The package management operations accept a list of
packages. Packages are installed in `~~userlib` which is mapped to
`~/.gambit_userlib` by default. An optional `-dir dir` option can be used to
install the package in some other directory.

For example:

```scheme
$ gsi -install github.com/feeley/roman
installing github.com/feeley/roman to /Users/feeley/.gambit_userlib/
$ gsi github.com/feeley/roman/demo
1 is I in roman numerals
2 is II in roman numerals
4 is IV in roman numerals
8 is VIII in roman numerals
16 is XVI in roman numerals
32 is XXXII in roman numerals
64 is LXIV in roman numerals
$ gsi -uninstall github.com/feeley/roman
uninstalling github.com/feeley/roman from /Users/feeley/.gambit_userlib/
$ gsi -install -dir ~/mylibs github.com/feeley/roman
installing github.com/feeley/roman to /Users/feeley/mylibs
$ gsi ~/mylibs/ github.com/feeley/roman/demo
1 is I in roman numerals
2 is II in roman numerals
4 is IV in roman numerals
8 is VIII in roman numerals
16 is XVI in roman numerals
32 is XXXII in roman numerals
64 is LXIV in roman numerals
```

Local `git` repositories can also be installed manually with the package
management operations using a path to the local repository. This can be useful
during the development phase before a library becomes hosted.

For example:

```scheme
$ mkdir some
$ mkdir some/dir
$ mkdir some/dir/mylib
$ cd some/dir/mylib
$ cat > mylib.sld
(define-library (mylib)
  (import (scheme write))
  (begin (display "mylib library has executed\n")))
$ git init
Initialized empty Git repository in /Users/feeley/doc/some/dir/mylib/.git/
$ git add mylib.sld
$ git commit -m "first commit"
[master (root-commit) c3f6aff] first commit
 1 file changed, 3 insertions(+)
 create mode 100644 mylib.sld
$ cd ../../..
$ gsi some/dir/ mylib   # execution of mylib without installation
mylib library has executed
$ gsi -install some/dir/mylib
installing some/dir/mylib to /Users/feeley/.gambit_userlib/
$ gsi mylib   # execution of mylib after installation
mylib library has executed
```
