---
title: Scheme scripts
---

# Scheme scripts

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

A script file can be compiled using the Gambit Scheme compiler (see section [The
Gambit Scheme compiler](/manual/gsc)) into a standalone executable. The script
line will provide information to the compiler on which language to use. The
script line also provides information on which runtime options to use when
executing the compiled script. This is useful to set the default runtime options
of an executable program.

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
