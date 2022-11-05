# The Gambit Scheme Interpreter

```shell
gsi [-:runtimeoption,…] [-i] [-f] [-h] [-help] [-v]
    [[-] [-e expressions] [-install] [-uninstall] [-update]
     [search-directory-or-module-or-file]]…
```

The interpreter is executed in batch mode when the command line contains a
module or file or a `-`, or `-e` option. The interpreter is executed in module
management mode when the command line contains the `-install`, `-uninstall`, or
`-update` option. Otherwise the interpreter is executed in interactive mode. The
`-i` option is ignored by the interpreter. The initialization file will be
examined unless the `-f` option is present (see section [Customization](#customization)). The `-h`
and `-help` options print brief usage information on standard output and exit.
The `-v` option prints the system version string, system time stamp, operating
system type, and configure script options on standard output and exits. Runtime
options are explained in Runtime options.


## Interactive mode
In interactive mode a read-eval-print loop (REPL) is started for the user to
interact with the interpreter. At each iteration of this loop the interpreter
displays a prompt, reads a command and executes it. The commands can be
expressions to evaluate (the typical case) or special commands related to
debugging, for example `,q` to terminate the process (for a complete list of
commands see Debugging or use the `,help` command). Most commands produce some
output, such as the value or error message resulting from an evaluation.

The input and output of the interaction is done on the interaction channel. The
interaction channel can be specified through the runtime options but if none is
specified the system uses a reasonable default that depends on the system's
configuration. Typically the program's standard input and output are used as the
interaction channel. When using the runtime option `-:debug=c`, the interaction
channel is the user`s console, also known as the controlling terminal in the
UNIX world.

When the REPL starts, the ports associated with `(current-input-port)`,
`(current-output-port)` and `(current-error-port)` all refer to the interaction
channel.

Expressions are evaluated in the global interaction environment. The interpreter
adds to this environment any definition entered using the define and
define-macro special forms. Once the evaluation of an expression is completed,
the value or values resulting from the evaluation are output to the interaction
channel by the pretty printer. The special void object is not output. This
object is returned by most procedures and special forms which are defined as
returning an unspecified value (e.g. `write`, `set!`, `define`).

Here is a sample interaction with gsi:

```shell
$ gsi
Gambit v4.9.4

> (define (fact n) (if (< n 2) 1 (* n (fact (- n 1)))))
> (map fact '(1 2 3 4 5 6))
(1 2 6 24 120 720)
> (values (fact 10) (fact 40))
3628800
815915283247897734345611269596115894272000000000
> ,q
```

What happens when errors occur is explained in Debugging.

## Batch mode

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

## Module management mode

Package management operations are executed using the command line options
`-install`, `-uninstall`, and `-update` which respectively install, uninstall
and update packages. Package installation is explained in detail in Modules, but
here are a few examples:

```shell
$ gsi -install github.com/gambit/hello
installing github.com/gambit/hello to /Users/feeley/.gambit_userlib/
$ gsi github.com/gambit/hello@1.0
hello world!
$ gsi -uninstall github.com/gambit/hello
uninstalling github.com/gambit/hello from /Users/feeley/.gambit_userlib/
```

## Customization

There are two ways to customize the interpreter. When the interpreter starts off
it tries to execute a `(load "~~lib/gambext")` (for an explanation of how file
names are interpreted see Host environment). An error is not signaled when the
file does not exist. Interpreter extensions and patches that are meant to apply
to all users and all modes should go in that file.

Extensions which are meant to apply to a single user or to a specific working
directory are best placed in the initialization file, which is a file containing
Scheme code. In all modes, the interpreter first tries to locate the
initialization file by searching the following locations: ‘.gambini’ and
`~/.gambini` (with no extension, a `.sld` extension, a `.scm` extension, and a
`.six` extension in that order). The first file that is found is examined as
though the expression `(include initialization-file)` had been entered at the
read-eval-print loop where `initialization-file` is the file that was found.
Note that by using an include the macros defined in the initialization file will
be visible from the read-eval-print loop (this would not have been the case if
`load` had been used). The initialization file is not searched for or examined
when the `-f` option is specified.

## Process exit status

The status is zero when the interpreter exits normally and is nonzero when the
interpreter exits due to an error. Here is the meaning of the exit statuses:

`0`
: The execution of the primordial thread (i.e. the main thread) did not
  encounter any error. It is however possible that other threads terminated
  abnormally (by default threads other than the primordial thread terminate
  silently when they raise an exception that is not handled).

`64`
: The runtime options or the environment variable `GAMBOPT` contained a syntax
  error or were invalid.

`70`
: This normally indicates that an exception was raised in the primordial thread
  and the exception was not handled.

`71`
: There was a problem initializing the runtime system, for example insufficient
  memory to allocate critical tables.

For example, if the shell is `sh`:

```shell
$ gsi -e "(pretty-print (expt 2 100))"
1267650600228229401496703205376
$ echo $?
0
$ gsi -e "(pretty-print (expo 2 100))"
*** ERROR IN (string)@1.16 -- Unbound variable: expo
$ echo $?
70
$ gsi -:debug=0 -e "(pretty-print (expo 2 100))"
$ echo $?
70
$ gsi -:debug=0,unknown # try to use an unknown runtime option
$ echo $?
64
$ gsi -:debug=0 nonexistent.scm # try to load a file that does not exist
$ echo $?
70
$ gsi nonexistent.scm
*** ERROR IN ##load-module-or-file -- No such file or directory
(load "nonexistent.scm")
$ echo $?
70
```

Note the use of the runtime option `-:debug=0` that prevents error messages from
being output.

## Scheme scripts

The load procedure treats specially files that begin with the two characters
`#!` and `@;`. Such files are called script files and the first line is called
the script line. In addition to indicating that the file is a script, the script
line provides information about the source code language to be used by the load
procedure. After the two characters `#!` and `@;` the system will search for the
first substring matching one of the following language specifying tokens:

`scheme-r4rs`
: R4RS language with prefix syntax, case-insensitivity, keyword syntax not
  supported

`scheme-r5rs`
: R5RS language with prefix syntax, case-insensitivity, keyword syntax not
  supported

`scheme-ieee-1178-1990`
: IEEE 1178-1990 language with prefix syntax, case-insensitivity, keyword syntax
  not supported

`scheme-srfi-0`
: R5RS language with prefix syntax and SRFI 0 support (i.e. `cond-expand` special
  form), case-insensitivity, keyword syntax not supported

`gsi-script`
: Full Gambit Scheme language with prefix syntax, case-sensitivity, keyword
  syntax supported

`gsc-script`
: Full Gambit Scheme language with prefix syntax, case-sensitivity, keyword
  syntax supported

`six-script`
: Full Gambit Scheme language with infix syntax, case-sensitivity, keyword
  syntax supported

If a language specifying token is not found, load will use the same language as
a nonscript file (i.e. it uses the file extension and runtime system options to
determine the language).

After processing the script line, load will parse the rest of the file (using
the syntax of the language indicated) and then execute it. When the file is
being loaded because it is an argument on the interpreter's command line, the
interpreter will:

Setup the `command-line` procedure so that it returns a list containing the
expanded file name of the script file and the arguments following the script
file on the command line. This is done before the script is executed. The
expanded file name of the script file can be used to determine the directory
that contains the script (i.e. `(path-directory (car (command-line)))`). After the
script is loaded the procedure main is called with the command line arguments.
The way this is done depends on the language specifying token. For `scheme-r4rs`,
`scheme-r5rs`, `scheme-ieee-1178-1990`, and `scheme-srfi-0`, the `main` procedure is
called with the equivalent of `(main (cdr (command-line)))` and `main` is expected
to return a process exit status code in the range 0 to 255. This conforms to the
"Running Scheme Scripts on Unix SRFI" (SRFI 22). For `gsi-script` and `six-script`
the `main` procedure is called with the equivalent of `(apply main (cdr
(command-line)))` and the process exit status code is 0 (`main`’s result is
ignored). The Gambit system has a predefined `main` procedure which accepts any
number of arguments and returns 0, so it is perfectly valid for a script to not
define `main` and to do all its processing with top-level expressions (examples
are given in the next section). When `main` returns, the interpreter exits. The
command line arguments after a script file are consequently not processed
(however they do appear in the list returned by the command-line procedure,
after the script file's expanded file name, so it is up to the script to process
them).

### Scripts under UNIX and macOS

Under UNIX and macOS, the Gambit installation process creates the executable
`gsi` and also the executables `six`, `gsi-script`, `six-script`, `scheme-r5rs`,
`scheme-srfi-0`, etc as links to `gsi`. A Scheme script need only start with the
name of the desired Scheme language variant prefixed with `#!` and the directory
where the Gambit executables are stored. This script should be made executable
by setting the execute permission bits (with a `chmod +x script`). Here is an
example of a script which lists on standard output the files in the current
directory:

```scheme
#!/usr/local/Gambit/bin/gsi-script
(for-each pretty-print (directory-files))
```

Here is another UNIX script, using the Scheme infix syntax extension, which
takes a single integer argument and prints on standard output the numbers from 1
to that integer:

```c
#!/usr/local/Gambit/bin/six-script

function main(n_str)
{
  scmobj n = \string->number(n_str);
  for (scmobj i=1; i<=n; i++)
    \pretty-print(i);
}
```

For maximal portability it is a good idea to start scripts indirectly through
the `/usr/bin/env` program, so that the executable of the interpreter will be
searched in the user's `PATH`. This is what SRFI 22 recommends. For example here
is a script that mimics the UNIX `cat` utility for text files:

```scheme
#!/usr/bin/env gsi-script

(define (display-file filename)
  (display (call-with-input-file filename
             (lambda (port)
               (read-line port #f)))))

(for-each display-file (cdr (command-line)))
```


### Scripts under Microsoft Windows

Under Microsoft Windows, the Gambit installation process creates the executable
`gsi.exe` and `six.exe` and also the batch files `gsi-script.bat`,
`six-script.bat`, `scheme-r5rs.bat`, `scheme-srfi-0.bat`, etc which simply
invoke `gsi.exe` with the same command line arguments. A Scheme script need only
start with the name of the desired Scheme language variant prefixed with `@;`. A
UNIX script can be converted to a Microsoft Windows script simply by changing
the script line and storing the script in a file whose name has a `.bat` or
`.cmd` extension:

```scheme
@;gsi-script %~f0 %*
(display "files:\n")
(pretty-print (directory-files))
```

Note that Microsoft Windows always searches executables in the user's `PATH`, so
there is no need for an indirection such as the UNIX `/usr/bin/env`. However the
script line must end with `%~f0 %*` to pass the expanded filename of the script
and command line arguments to the interpreter.


### Compiling scripts

A script file can be compiled using the Gambit Scheme compiler (see section The
Gambit Scheme compiler) into a standalone executable. The script line will
provide information to the compiler on which language to use. The script line
also provides information on which runtime options to use when executing the
compiled script. This is useful to set the default runtime options of an
executable program.

The compiled script will be executed similarly to an interpreted script (i.e.
the list of command line arguments returned by the `command-line` procedure and
the invocation of the `main` procedure).

For example:

```shell
$ cat square.scm
#!/usr/local/Gambit/bin/gsi-script -:debug=0
(define (main arg)
  (pretty-print (expt (string->number arg) 2)))
$ gsi square 30        # gsi will load square.scm
900
$ gsc -exe square      # compile the script to a standalone program
$ ./square 30
900
$ ./square 1 2 3       # too many arguments to main
$ echo $?
70
$ ./square -:debug=1 1 2 3  # ask for error message
*** ERROR IN ##start-main -- Wrong number of arguments passed to procedure
(main "1" "2" "3")
```
