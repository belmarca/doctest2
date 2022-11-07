# The Gambit Scheme Compiler

```shell
gsc [-:runtimeoption,…] [-i] [-f] [-h] [-help] [-v]
    [-target target]
    [-prelude expressions] [-postlude expressions]
    [-dynamic] [-exe] [-obj]
    [-nb-gvm-regs n] [-nb-arg-regs n] [-compactness level]
    [-cc compiler] [-cc-options options]
    [-ld-options-prelude options] [-ld-options options]
    [-pkg-config pkg-config-args] [-pkg-config-path pkg-config-path]
    [-warnings] [-verbose] [-report] [-expansion] [-gvm] [-cfg] [-dg]
    [-debug] [-debug-location] [-debug-source]
    [-debug-environments] [-track-scheme]
    [-o output] [-c] [-keep-temp] [-link] [-flat] [-l base]
    [-module-ref module-ref] [-linker-name linker-name]
    [[-] [-e expressions] [-preload] [-nopreload]
     [search-directory-or-module-or-file]]…
```

The `-h` and `-help` options print brief usage information on standard output
and exit. The `-v` option prints the system version string, system time stamp,
operating system type, and configure script options on standard output and
exits.

The `-i` option can be used to force gsc to process the command line like the
interpreter. The only difference with the interpreter is that the compilation
related procedures listed in this chapter are also available (i.e. `compile-file`,
`compile-file-to-target`, etc).

## Interactive mode

When no command line argument is present other than options `gsc` behaves like `gsi`
in interactive mode.

## Customization

Like the interpreter, the compiler will examine the initialization file unless
the `-f` option is specified. Runtime options are explained in [Runtime
options](#runtime_options).

## Batch mode

In batch mode `gsc` accepts on the command line 3 types of non-options which are
processed from left to right: search directories, modules, and files. Search
directories are added to the list of module search order directories. Every
command line argument that is the name of a module that is found in the list of
module search order directories will cause that module to be compiled.
Similarly, file names (with either no extension, or a C file extension, or some
other extension) on the command line will cause that file to be compiled. The
compilation is done for the target language specified with the -target target
option. target is either js, for JavaScript, or C, which is the default if no
target language is specified.

The recognized C file extensions are `.c`, `.C`, `.cc`, `.cp`, `.cpp`, `.CPP`,
`.cxx`, `.c++`, `.m`, `.M`, and `.mm`.

The extension can be omitted from a file name when the Scheme file has a `.scm`,
`.sld` or `.six` extension. When the extension of the Scheme file is `.six` the
content of the file will be parsed using the Scheme infix syntax extension (see
[Scheme infix syntax extension](#scheme_infix_syntax_extension)). Otherwise, `gsc`
will parse the Scheme file using the normal Scheme prefix syntax. Files with a C
file extension must have been previously produced by gsc with the C target and
the `-c` option, and are used by the C target Gambit linker.

For each Scheme file the compiler creates a file of target code, either `file.c`
or `file.js` for the C and js targets respectively. The file's name is the same
as the Scheme file, but the extension is changed to `.c` or `.js` as
appropriate. By default the file is created in the same directory as the Scheme
file. This default can be overridden with the compiler's `-o` option.

The files of target code produced by the compiler serve two purposes. They will
be processed by a C compiler or JavaScript VM, and they also contain information
to be read by Gambit’s linker to generate a link file. The link file is a file
of target code that collects various linking information for a group of modules,
such as the set of all symbols and global variables used by the modules. The
linker is only invoked when the `-link` or `-exe` options appear on the command
line.

Compiler options must be specified before the first file name and after the `-:`
runtime option (see section [Runtime options](#runtime_options)). If present, the `-i`, `-f`, and
`-v` compiler options must come first. The available options are:

`-i`
: Force interpreter mode.

`-f`
:Do not examine the initialization file.

`-h / -help`
: Print brief usage information on standard output and exit.

`-v`
: Print the system version string, system time stamp, operating system type, and
  configure script options on standard output and exit.

`-target target`
: Select the target language.

`-prelude expressions`
: Add expressions to the top of the source code being compiled.

`-postlude expressions`
: Add expressions to the bottom of the source code being compiled.

`-cc compiler`
: Select specific C compiler.

`-cc-options options`
: Add options to the command that invokes the C compiler.

`-ld-options-prelude options`
: Add options to the command that invokes the C linker.

`-ld-options options`
: Add options to the command that invokes the C linker.

`-pkg-config pkg-config-args`
: Use the pkg-config program to determine options for the C compiler and C
  linker.

`-pkg-config-path pkg-config-path`
: Add a path to the `PKG_CONFIG_PATH` environment variable.

`-warnings`
: Display warnings.

`-verbose`
: Display a trace of the compiler's activity.

`-report`
: Display a global variable usage report.

`-expansion`
: Display the source code after expansion.

`-gvm`
: Generate a listing of the GVM code.

`-cfg`
: Generate a control flow graph of the GVM code.

`-dg`
: Generate a dependency graph.

`-debug`
: Include all debugging information in the code generated.

`-debug-location`
: Include source code location debugging information in the code generated.

`-debug-source`
: Include the source code debugging information in the code generated.

`-debug-environments`
: Include environment debugging information in the code generated.

`-track-scheme`
: Generate `#line` directives referring back to the Scheme code.

`-o output`
: Set name of output file or directory where output file(s) are written.

`-dynamic`
: Compile Scheme source files to dynamically loadable object files (this is the default).

`-exe`
: Compile Scheme source files to an executable program (machine code or script).

`-obj`
: Compile Scheme source files to object files by invoking the C compiler.

`-keep-temp`
: Keep any intermediate files that are generated.

`-c`
: Compile Scheme source files to target code without generating a link file.

`-link`
: Compile Scheme source files to target code and generate a link file.

`-flat`
: Generate a flat link file instead of the default incremental link file.

`-l base`
: Specify the link file of the base library to use for the link.

`-module-ref module-ref`
: Specify the reference of the generated module.

`-linker-name linker-name`
: Specify the name of the low-level initialization function exported by the module.

`-preload`
: Turn on `preload` linker bit.

`-nopreload`
: Turn off `preload` linker bit. Start REPL interaction.

`-e expressions`
: Evaluate expressions in the interaction environment.

`-nb-gvm-regs n`
: Specify the number of available Gambit virtual machine registers.

`-nb-arg-regs n`
: Specify the number of procedure call parameters passed in Gambit virtual
  machine registers.

`-compactness level`
: Specify the compactness of the generated code.

The `-i` option forces the compiler to process the remaining command line
arguments like the interpreter.

The `-target` option selects the target language of the compilation. It is
either `js` for JavaScript, or `C` for C (which is the default).

The `-prelude` option adds the specified expressions to the top of the source
code being compiled. It can appear multiple times. The main use of this option
is to supply declarations on the command line. For example the following
invocation of the compiler will compile the file `bench.scm` in unsafe mode:

```shell
$ gsc -prelude "(declare (not safe))" bench.scm
```

The `-postlude` option adds the specified expressions to the bottom of the
source code being compiled. It can appear multiple times. The main use of this
option is to supply the expression that will start the execution of the program.
For example:

```shell
$ gsc -postlude "(start-bench)" bench.scm
```

The `-cc` option is only meaningful when the `C` target is selected. The `-cc`
option selects the specified C compiler for compiling the generated C code. When
this option is used, the default C compiler options that were determined to be
needed by the configure script are nullified because they are very likely to be
invalid for the specified C compiler. Any options needed for this C compiler
should be specified explicitly using the `-cc-options`, `-ld-options-prelude`,
and `-ld-options` options. For example:

```shell
$ gsc -cc clang -cc-options "-O0 -bundle" bench.scm # clang on macOS
$ gsc -cc tcc -cc-options -shared bench.scm         # tcc on linux
```

The `-cc-options` option is only meaningful when the `C` target is selected and a
dynamically loadable object file is being generated (neither the `-c` or `-link`
options are used). It can appear multiple times. The `-cc-options` option adds
the specified options to the command that invokes the C compiler. The main use
of this option is to specify the include path, some symbols to define or
undefine, the optimization level, or any C compiler option that is different
from the default. For example:

```shell
$ gsc -cc-options "-U___SINGLE_HOST -O2 -I../include" bench.scm
```

The `-ld-options-prelude` and `-ld-options` options are only meaningful when the
`C` target is selected and a dynamically loadable object file is being generated
(neither the `-c` or `-link` options are used). They can appear multiple times.
The `-ld-options-prelude` and `-ld-options` options add the specified options to
the command that invokes the C linker (the options in `ld-options-prelude` are
passed to the C linker before the input file and the options in `ld-options` are
passed after). The main use of this option is to specify additional object files
or libraries that need to be linked, or any C linker option that is different
from the default (such as the library search path and flags to select between
static and dynamic linking). For example:

```shell
$ gsc -ld-options "-L/usr/X11R6/lib -lX11 -dynamic" app.scm
```

The `-pkg-config` is only meaningful when the `C` target is selected. The
`-pkg-config` option will cause the `pkg-config` program to be invoked to
determine the options to add to the command that invokes the C compiler and C
linker. It can appear multiple times. The `pkg-config` program is passed the
arguments in the string `pkg-config-args` in addition to either `--cflags` or
`--libs`. It is typical for `pkg-config-args` to be the name of a system
library, such as `"sqlite3"`, but other `pkg-config` options can be specified,
such as `"--static sqlite3"`. The `-pkg-config-path` option adds a path to the
`PKG_CONFIG_PATH` environment variable for use by the `pkg-config` program to
find `.pc` files. For example:

```shell
$ gsc -pkg-config "x11" -pkg-config-path "/usr/share/pkgconfig" app.scm
```

The `-warnings` option displays on standard output all warnings that the
compiler may have.

The `-verbose` option displays on standard output a trace of the compiler's
activity.

The `-report` option displays on standard output a global variable usage report.
Each global variable used in the program is listed with 4 flags that indicate
whether the global variable is defined, referenced, mutated and called.

The `-expansion` option displays on standard output the source code after
expansion and inlining by the front end.

The `-gvm` option generates a listing of the intermediate code for the “Gambit
Virtual Machine” (GVM) of each Scheme file on `file.gvm`.

The `-cfg` option generates a visual representation of the control flow graph of
the intermediate code for the “Gambit Virtual Machine” (GVM) of each Scheme file
on `file.cfg`. The file is suitable for processing with the `dot` program. For
example, to generate the PDF file `file.cfg.pdf` from `file.cfg` the following
command can be used:

```shell
$ dot -O -Tpdf file.cfg
```

The `-dg` option generates a visual representation of the dependency graph of
each Scheme file on `file.dg`. The file is suitable for processing with the
`dot` program. For example, to generate the PDF file `file.dg.pdf` from
`file.dg` the following command can be used:

```shell
$ dot -O -Tpdf file.dg
```

The `-debug` option causes all kinds of debugging information to be saved in the
code generated. See the documentation of the `debug` declaration for details.

The `-debug-location` option causes source code location debugging information
to be saved in the code generated. See the documentation of the `debug-location`
declaration for details.

The `-debug-source` option causes source code debugging information to be saved
in the code generated. See the documentation of the `debug-source` declaration
for details.

The `-debug-environments` option causes environment debugging information to be
saved in the code generated. See the documentation of the `debug-environments`
declaration for details.

The `-track-scheme` option is only meaningful when the `C` target is selected. The
`-track-scheme` option causes the generation of `#line` directives that refer
back to the Scheme source code. This allows the use of a C debugger or profiler
to debug Scheme code.

The `-o` option sets the filename of the output file, or the directory in which
the output file(s) generated by the compiler are written.

If the `-link` or `-exe` options appear on the command line, the Gambit linker
is invoked to generate the link file from the set of files specified on the
command line or produced by the Gambit compiler. By default the link file is
named after the last file on the compiler's command line. If the last file
stripped of it's extension is `last` then the link file is `last_.c` for the `C`
target and `last_.js` for the `js` target. When the `-c` option is specified, the
Scheme source files are compiled to target files without invoking the linker,
which is useful for separate compilation of modules. When the `-exe` option is
specified, the generated target files and link file are combined to produce an
executable program whose name defaults to `last` on Unix, and `last.exe` or
`last.bat` on Windows depending on whether a machine code executable or script
is produced. When the C target is selected and the `-obj` option is specified,
the generated C files are compiled using the C compiler to produce object files
(`.o` or `.obj` extensions). If neither the `-link`, `-c`, `-exe`, or `-obj`
options appear on the command line, the Scheme source files are compiled to
dynamically loadable object files (`.on` extension). The `-keep-temp` option
will prevent the deletion of any intermediate files that are generated. Note
that in this case the intermediate file will be generated in the same directory
as the Scheme source file even if the `-o` option is used.

The `-flat` option is only meaningful when a link file is being generated (i.e.
the `-link` or `-exe` options also appear on the command line). The `-flat`
option directs the Gambit linker to generate a flat link file. By default, the
linker generates an incremental link file (see the next section for a
description of the two types of link files).

The `-l` option is only meaningful when an incremental link file is being
generated (i.e. the `-link` or `-exe` options appear on the command line and the
`-flat` option is absent). The `-l` option specifies the link file (without the
`.c` or `.js` extension) of the base library to use for the incremental link. By
default the link file of the Gambit runtime library is used (i.e.
`~~lib/_gambit`).

The `-preload` and `-nopreload` options are only meaningful when a link file is
being generated. The `-preload` option turns on the `preload` linker bit for the
modules that follow on the command line. The following modules will be loaded
unconditionally at program startup and in command line order (this is the
default for compatibility with how legacy modules have been handled in the
past). The `-nopreload` option turns off the `preload` linker bit. The following
modules will be loaded only to satisfy the module dependencies of the
`##demand-module` form.

The `-` option starts a REPL interaction.

The `-e` option evaluates the specified expressions in the interaction
environment.

The `-nb-gvm-regs` option specifies the number of Gambit virtual machine
registers that are available for the generated code. The default number depends
on configuration options and the target but it is typically `5`. All modules and
the runtime library must be compiled with the same setting. This option exists
mainly for experimentation by the developers. For example:

```shell
$ gsc -nb-gvm-regs 10 -c bench.scm
```

The `-nb-arg-regs` option specifies the number of procedure call parameters
passed in Gambit virtual machine registers. The default number depends on
configuration options and the target but it is typically `3`. All modules and the
runtime library must be compiled with the same setting. This option exists
mainly for experimentation by the developers. For example:

```shell
$ gsc -nb-arg-regs 2 -c bench.scm
```

The `-compactness` option selects the level of compactness of the generated
code. The default level depends on configuration options and the target but it
is typically `5`. Levels from `0` to `5` cause the generation of increasingly compact
code with little or no impact on execution speed. Lower values tend to make the
generated code more humanly readable. Above a level of `5` the compiler will trade
execution speed for saving code space. The detailed meaning of this option
depends on the target, some targets may ignore it and some targets may require
all modules and the runtime library to be compiled with the same compactness
level. For example:

```shell
$ gsc -target js -compactness 0 -c bench.scm
```

## Link files

Gambit can be used to create programs and libraries of Scheme modules. This
section explains the steps required to do so and the role played by the link
files.

In general, a program is composed of a set of Scheme modules and modules in the
target language. Some of the modules are part of the Gambit runtime library and
the other modules are supplied by the user. When the program is started it must
setup various global tables (including the symbol table and the global variable
table) and then sequentially execute the Scheme modules (more or less as though
they were being loaded one after another). The information required for this is
contained in one or more link files generated by the Gambit linker from the
target files produced by the Gambit compiler.

The order of execution of the Scheme modules corresponds to the order of the
modules on the command line which produced the link file. The order is usually
important because most modules define variables and procedures which are used by
other modules (for this reason the program's main computation is normally
started by the last module).

When a single link file is used to contain the linking information of all the
Scheme modules it is called a flat link file. Thus a program built with a flat
link file contains in its link file both information on the user modules and on
the runtime library. This is fine if the program is to be statically linked but
is wasteful in a shared-library context because the linking information of the
runtime library can’t be shared and will be duplicated in all programs (this
linking information typically takes hundreds of kilobytes).

Flat link files are mainly useful to bundle multiple Scheme modules to make a
runtime library (such as the Gambit runtime library) or to make a single file
that can be loaded with the `load` procedure.

An incremental link file contains only the linking information that is not
already contained in a second link file (the "base" link file). Assuming that a
flat link file was produced when the runtime library was linked, a program can
be built by linking the user modules with the runtime library's link file,
producing an incremental link file. This allows the creation of a shared-library
which contains the modules of the runtime library and its flat link file. The
program is dynamically linked with this shared-library and only contains the
user modules and the incremental link file. For small programs this approach
greatly reduces the size of the program because the incremental link file is
small. A "hello world" program built this way can be as small as 5 Kbytes. Note
that it is perfectly fine to use an incremental link file for statically linked
programs (there is very little loss compared to a single flat link file).

Incremental link files may be built from other incremental link files. This
allows the creation of shared-libraries which extend the functionality of the
Gambit runtime library.

### Building an executable program

The simplest way to create an executable program is to invoke `gsc` with the
`-exe` option. The compiler will transparently perform all the steps necessary,
including compiling Scheme source files to target files, generating the link
file, and (when the `C` target is selected) compiling the C files generated to
object files and creating the final executable file using the C linker. The
following example shows how to use the C target to build the executable program
`hello.exe` which contains the two Scheme modules `h.scm` and `w.six`.

```shell
$ cat h.scm
(display "hello") (newline)
$ cat w.six
display("world"); newline();
$ gsc -o hello.exe -exe h.scm w.six
h.scm:
/Users/feeley/gambit/doc/h.c:
w.six:
/Users/feeley/gambit/doc/w.c:
/Users/feeley/gambit/doc/w_.c:
$ ./hello.exe
hello
world
```

The detailed steps which are performed can be viewed by setting the
`GAMBUILD_VERBOSE` environment variable to a nonnull value. Alternatively, `gsc`’s
`-verbose` option can be used (it implicitly sets the `GAMBUILD_VERBOSE`
environment variable). For example:

```shell
$ export GAMBUILD_VERBOSE=yes
$ gsc -o hello.exe -exe h.scm w.six
h.scm:
/Users/feeley/gambit/doc/h.c:
gcc  -O1    -Wno-unused -Wno-write-strings -Wdisabled-optimization
fwrapv -fno-strict-aliasing -fno-trapping-math -fno-math-errno
-fschedule-insns2 -foptimize-sibling-calls -fomit-frame-pointer -fPIC
-fno-common -mpc64   -D___SINGLE_HOST  -I"/usr/local/Gambit/include"
-c -o 'h.o'  'h.c'
w.six:
/Users/feeley/gambit/doc/w.c:
gcc  -O1    -Wno-unused -Wno-write-strings -Wdisabled-optimization
-fwrapv -fno-strict-aliasing -fno-trapping-math -fno-math-errno
-fschedule-insns2 -foptimize-sibling-calls -fomit-frame-pointer -fPIC
-fno-common -mpc64   -D___SINGLE_HOST  -I"/usr/local/Gambit/include"
-c -o 'w.o'  'w.c'
/Users/feeley/gambit/doc/w_.c:
gcc  -O1    -Wno-unused -Wno-write-strings -Wdisabled-optimization
-fwrapv -fno-strict-aliasing -fno-trapping-math -fno-math-errno
-fschedule-insns2 -foptimize-sibling-calls -fomit-frame-pointer -fPIC
-fno-common -mpc64   -D___SINGLE_HOST  -I"/usr/local/Gambit/include"
-c -o 'w_.o'  'w_.c'
gcc     -Wno-unused -Wno-write-strings -Wdisabled-optimization
-fwrapv -fno-strict-aliasing -fno-trapping-math -fno-math-errno
-fschedule-insns2 -foptimize-sibling-calls -fomit-frame-pointer -fPIC
-fno-common -mpc64    -D___SINGLE_HOST  -I"/usr/local/Gambit/include"
-o 'hello.exe'   'w_.o' 'h.o' 'w.o' "/usr/local/Gambit/lib/libgambit.a"
```

Here is the same example using the js target showing the creation of a shell
script invoking `nodejs`:

```shell
$ export GAMBUILD_VERBOSE=yes
$ gsc -target js -o hello.exe -exe h.scm w.six
h.scm:
/Users/feeley/gambit/doc/h.js:
cat h.js > "h.o"
w.six:
/Users/feeley/gambit/doc/w.js:
cat w.js > "w.o"
/Users/feeley/gambit/doc/w_.js:
cat w_.js > "w_.o"
echo "#! /usr/bin/env node" > "hello.exe"
cat w_.o h.o w.o "/usr/local/Gambit/lib/_gambit.js" >> "hello.exe"
chmod +x "hello.exe"
```

Using a single invocation of gsc with the `-exe` option is sometimes
inappropriate when the build process is more complex, for example when the
program is composed of several separately compiled modules. In such a case it is
useful to decompose the build process into smaller compilation steps. The
`hello.exe` executable program could have been built with the `C` target by
separating the generation of C files from the C compilation and linking:

```shell
$ gsc -c h.scm
$ gsc -c w.six
$ gsc -o hello.exe -exe h.c w.c
```

When even finer control is desired the C target's build process can be
decomposed into smaller steps that invoke the C compiler and linker explicitly.
This is described in the rest of this section.

The `gsc` compiler can be invoked to compile each Scheme module into a C file and
to create an incremental link file. The C files and the link file must then be
compiled with a C compiler and linked (at the object file level) with the Gambit
runtime library and possibly other libraries (such as the math library and the
dynamic loading library).

Here is for example how a program with three modules (one in C and two in
Scheme) can be built. The content of the three source files (`m1.c`, `m2.scm`
and `m3.scm`) is:

```shell
/* File: "m1.c" */
int power_of_2 (int x) { return 1<<x; }

; File: "m2.scm"
(c-declare "extern int power_of_2 ();")
(define pow2 (c-lambda (int) int "power_of_2"))
(define (twice x) (cons x x))

; File: "m3.scm"
(write (map twice (map pow2 '(1 2 3 4)))) (newline)
```

The compilation of the two Scheme source files can be done with three
invocations of `gsc`:

```shell
$ gsc -c m2.scm        # create m2.c (note: .scm is optional)
$ gsc -c m3.scm        # create m3.c (note: .scm is optional)
$ gsc -link m2.c m3.c  # create the incremental link file m3_.c
```

Alternatively, the three invocations of `gsc` can be replaced by a single
invocation:

```shell
$ gsc -link m2 m3
m2:
m3:
```

At this point there will be 4 C files: `m1.c`, `m2.c`, `m3.c`, and `m3_.c`. To
produce an executable program these files must be compiled with a C compiler and
linked with the Gambit runtime library. The C compiler options needed will
depend on the C compiler and the operating system (in particular it may be
necessary to add the options `-I/usr/local/Gambit/include
-L/usr/local/Gambit/lib` to access the `gambit.h` header file and the Gambit
runtime library).

Here is an example under macOS:

```shell
$ uname -srmp
Darwin 20.6.0 x86_64 i386
$ gsc -obj m1.c m2.c m3.c m3_.c
m1.c:
m2.c:
m3.c:
m3_.c:
$ gcc m1.o m2.o m3.o m3_.o -lgambit
$ ./a.out
((2 . 2) (4 . 4) (8 . 8) (16 . 16))
```

Here is an example under Linux:

```shell
$ uname -srmp
Linux 5.10.0-9-amd64 x86_64 unknown
$ gsc -obj m1.c m2.c m3.c m3_.c
m1.c:
m2.c:
m3.c:
m3_.c:
$ gcc m1.o m2.o m3.o m3_.o -lgambit -lm -ldl -lutil
$ ./a.out
((2 . 2) (4 . 4) (8 . 8) (16 . 16))
```

### Building a loadable library

To bundle multiple modules into a single object file that can be dynamically
loaded with the load procedure, a flat link file is needed. The compiler’s `-o`
option must be used to name the C file generated as follows. If the dynamically
loadable object file is to be named `myfile.on` then the `-o` option must set
the name of the link file generated to `myfile.on.c` (note that the `.c`
extension could also be `.cc`, `.cpp` or whatever extension is appropriate for
C/C++ source files). The three modules of the previous example can be bundled by
generating a link file in this way:

```shell
$ gsc -link -flat -o foo.o1.c m2 m3
m2:
m3:
*** WARNING -- "cons" is not defined,
***            referenced in: ("m2.c")
*** WARNING -- "map" is not defined,
***            referenced in: ("m3.c")
*** WARNING -- "newline" is not defined,
***            referenced in: ("m3.c")
*** WARNING -- "write" is not defined,
***            referenced in: ("m3.c")
```

The warnings indicate that there are no definitions (`define`s or `set!`s) of the
variables `cons`, `map`, `newline` and `write` in the set of modules being linked.
Before `foo.o1` is loaded, these variables will have to be bound; either
implicitly (by the runtime library) or explicitly.

When compiling the C files and link file generated, the flag `-D___DYNAMIC` must
be passed to the C compiler and the C compiler and linker must be told to
generate a dynamically loadable shared library.

Here is an example under macOS:

```shell
$ uname -srmp
Darwin 20.6.0 x86_64 i386
$ gsc -link -flat -o foo.o1.c m2 m3 > /dev/null
m2:
m3:
$ gsc -cc-options "-D___DYNAMIC" -obj m1.c m2.c m3.c foo.o1.c
m1.c:
m2.c:
m3.c:
foo.o1.c:
$ gcc -bundle m1.o m2.o m3.o foo.o1.o -o foo.o1
$ gsi foo.o1
((2 . 2) (4 . 4) (8 . 8) (16 . 16))
```

Here is an example under Linux:

```shell
$ uname -srmp
Linux 5.10.0-9-amd64 x86_64 unknown
$ gsc -link -flat -o foo.o1.c m2 m3 > /dev/null
m2:
m3:
$ gsc -cc-options "-D___DYNAMIC" -obj m1.c m2.c m3.c foo.o1.c
m1.c:
m2.c:
m3.c:
foo.o1.c:
$ gcc -shared m1.o m2.o m3.o foo.o1.o -o foo.o1
$ gsi foo.o1
((2 . 2) (4 . 4) (8 . 8) (16 . 16))
```

Here is a more complex example, under Solaris, which shows how to build a
loadable library `mymod.o1` composed of the files `m4.scm`, `m5.scm` and `x.c`
that links to system shared libraries (for X-windows):

```shell
$ uname -srmp
SunOS ungava 5.6 Generic_105181-05 sun4m sparc SUNW,SPARCstation-20
$ gsc -link -flat -o mymod.o1.c m4 m5
m4:
m5:
*** WARNING -- "*" is not defined,
***            referenced in: ("m4.c")
*** WARNING -- "+" is not defined,
***            referenced in: ("m5.c")
*** WARNING -- "display" is not defined,
***            referenced in: ("m5.c" "m4.c")
*** WARNING -- "newline" is not defined,
***            referenced in: ("m5.c" "m4.c")
*** WARNING -- "write" is not defined,
***            referenced in: ("m5.c")
$ gsc -cc-options "-D___DYNAMIC" -obj m4.c m5.c x.c mymod.o1.c
m4.c:
m5.c:
x.c:
mymod.o1.c:
$ /usr/ccs/bin/ld -G -o mymod.o1 mymod.o1.o m4.o m5.o x.o -lX11 -lsocket
$ gsi mymod.o1
hello from m4
hello from m5
(f1 10) = 22
$ cat m4.scm
(define (f1 x) (* 2 (f2 x)))
(display "hello from m4")
(newline)

(c-declare #<<c-declare-end
#include "x.h"
c-declare-end
)
(define x-initialize (c-lambda (char-string) bool "x_initialize"))
(define x-display-name (c-lambda () char-string "x_display_name"))
(define x-bell (c-lambda (int) void "x_bell"))
$ cat m5.scm
(define (f2 x) (+ x 1))
(display "hello from m5")
(newline)

(display "(f1 10) = ")
(write (f1 10))
(newline)

(x-initialize (x-display-name))
(x-bell 50) ; sound the bell at 50%
$ cat x.c
#include <X11/Xlib.h>

static Display *display;

int x_initialize (char *display_name)
{
  display = XOpenDisplay (display_name);
  return display != NULL;
}

char *x_display_name (void)
{
  return XDisplayName (NULL);
}

void x_bell (int volume)
{
  XBell (display, volume);
  XFlush (display);
}
$ cat x.h
int x_initialize (char *display_name);
char *x_display_name (void);
void x_bell (int);
```

### Building a shared library

A shared-library can be built using an incremental link file or a flat link
file. An incremental link file is normally used when the Gambit runtime library
(or some other library) is to be extended with new procedures. A flat link file
is mainly useful when building a "primal" runtime library, which is a library
(such as the Gambit runtime library) that does not extend another library. When
compiling the C files and link file generated, the flags `-D___LIBRARY` and
`-D___SHARED` must be passed to the C compiler. The flag `-D___PRIMAL` must also
be passed to the C compiler when a primal library is being built.

A shared-library `mylib.so` containing the two first modules of the previous
example can be built this way:

```shell
$ uname -srmp
Linux 5.10.0-9-amd64 x86_64 unknown
$ gsc -link -o mylib.c m2
$ gsc -obj -cc-options "-D___SHARED" m1.c m2.c mylib.c
m1.c:
m2.c:
mylib.c:
$ gcc -shared  m1.o m2.o mylib.o -o mylib.so
```

Note that this shared-library is built using an incremental link file (it
extends the Gambit runtime library with the procedures `pow2` and `twice`). This
shared-library can in turn be used to build an executable program from the third
module of the previous example:

```shell
$ gsc -link -l mylib m3
$ gsc -obj m3.c m3_.c
m3.c:
m3_.c:
$ gcc m3.o m3_.o mylib.so -lgambit
$ LD_LIBRARY_PATH=.:/usr/local/lib ./a.out
((2 . 2) (4 . 4) (8 . 8) (16 . 16))
```

### Other compilation options

The performance of the code can be increased by passing the `-D___SINGLE_HOST`
flag to the C compiler. This will merge all the procedures of a module into a
single C procedure, which reduces the cost of intra-module procedure calls. In
addition the `-O2` option can be passed to the C compiler. For large modules, it
will not be practical to specify both `-O2` and `-D___SINGLE_HOST` for typical C
compilers because the compile time will be high and the C compiler might even
fail to compile the program for lack of memory. It has been observed that lower
levels of optimization (e.g. `-O1`) often give faster compilation and also
generate faster code. It is a good idea to experiment.

Normally C compilers will not automatically search `/usr/local/Gambit/include`
for header files so the flag `-I/usr/local/Gambit/include` should be passed to
the C compiler. Similarly, C compilers/linkers will not automatically search
`/usr/local/Gambit/lib` for libraries so the flag `-L/usr/local/Gambit/lib`
should be passed to the C compiler/linker. Alternatives are given in [Accessing
the system files](../1_the_gambit_system/#accessing-the-system-files).

A variety of flags are needed by some C compilers when compiling a
shared-library or a dynamically loadable library. Some of these flags are:
`-shared`, `-call_shared`, `-rdynamic`, `-fpic`, `-fPIC`, `-Kpic`, `-KPIC`,
`-pic`, `+z`, `-G`. Check your compiler's documentation to see which flag you
need.


## Procedures specific to the compiler

The Gambit Scheme compiler features the following procedures that are not
available in the Gambit Scheme interpreter.

### compile-file-to-target

```scheme
(compile-file-to-target file [options: options]
                             [output: output]
                             [expression: expression]) ;; procedure
```

The `file` parameter must be a string. If `expression` is not specified, `file` must
name an existing file containing Scheme source code. The extension can be
omitted from `file` when the Scheme file has a `.scm`, `.sld` or `.six` extension.
By default, this procedure compiles the source file into a file containing C
code. A different target language can be selected in the `options`. The generated
file is named after `file` with the extension replaced with `.c` or `.js`, as
appropriate for the `target` selected. The name of the generated file can also be
specified directly with the `output` parameter. If `output` is a string naming a
directory then the generated file is created in that directory. Otherwise the
name of the generated file is `output`.

Compilation options are specified through the `options` parameter which must be an
association list. Any combination of the following options can be used:
`target`, `verbose`, `report`, `expansion`, `gvm`, `debug`, `module-ref`, and
`linker-name`.

When `expression` is specified, the `file` parameter is not open or read. Instead,
`expression` is used as though it was the content of the file. This makes it
possible to compile source code without having to create a file to contain the
code. Note that `file` is used in error messages and to determine the output file
name if `output` is not specified.

When the compilation is successful, `compile-file-to-target` returns the name of
the file generated. When there is a compilation error, `#f` is returned.

```shell
$ cat h.scm
(display "hello") (newline)
$ gsc
Gambit v4.9.4

> (compile-file-to-target "h")
"/Users/feeley/gambit/doc/h.c"
```

### compile-file

```scheme
(compile-file file [options: options]
                   [output: output]
                   [base: base]
                   [expression: expression]
                   [cc-options: cc-options]
                   [ld-options-prelude: ld-options-prelude]
                   [ld-options: ld-options]) ;; procedure
```

The `file`, `options`, `output`, and `expression` parameters have the same meaning as
for the `compile-file-to-target` procedure, except that `file` may be a Scheme
source file or a file possibly generated by the Gambit Scheme compiler (for
example with the `compile-file-to-target` procedure). The `cc-options` parameter is
a string containing the options to pass to the C compiler and the
`ld-options-prelude` and `ld-options` parameters are strings containing the options
to pass to the C linker (the options in `ld-options-prelude` are passed to the C
linker before the input file and the options in `ld-options` are passed after).

The `compile-file` procedure compiles the source file file into an object file,
which is either a file dynamically loadable using the load procedure, or a C
linkable object file destined to be linked with the C linker (for example to
create a standalone executable program). The presence of the `obj` option in
options will cause the creation of a C linkable object file and therefore the
options `ld-options-prelude` and `ld-options` are ignored, otherwise a dynamically
loadable file is created. In both cases, if file is a Scheme source file, the
compiler first compiles file to a C file which is created in the same directory
as `file` regardless of the `output` parameter. Then the C file is compiled with the
C compiler.

When the compilation is successful, `compile-file` returns the name of the object
file generated. When there is a compilation error, `#f` is returned.

The name of the object file can be specified with the `output` parameter. If
`output` is a string naming a directory then the object file is created in that
directory. Otherwise the name of the object file is `output`.

In the case of a dynamically loadable object file, by default the object file is
named after `file` with the extension replaced with `.on`, where `n` is a positive
integer that acts as a version number. The next available version number is
generated automatically by `compile-file`.

When dynamically loaded object files are loaded using the `load` procedure, the
`.on` extension can be specified (to select a particular version) or omitted (to
load the file with a `.on` extension with the highest `n` consecutively from `1`).
When the `.on` extension is not specified and older versions are no longer
needed, all versions must be deleted and the compilation must be repeated (this
is necessary because the file name, including the extension, is used to name
some of the exported symbols of the object file).

Note that dynamically loadable object files can only be generated on host
operating systems that support dynamic loading.

```shell
$ cat h.scm
(display "hello") (newline)
$ gsc
Gambit v4.9.4

> (compile-file "h")
"/Users/feeley/gambit/doc/h.o1"
> (load "h")
hello
"/Users/feeley/gambit/doc/h.o1"
> (compile-file-to-target "h" output: "h.o99.c")
"/Users/feeley/gambit/doc/h.o99.c"
> (compile-file "h.o99.c")
"/Users/feeley/gambit/doc/h.o99"
> (load "h.o99")
hello
"/Users/feeley/gambit/doc/h.o99"
> (compile-file-to-target "h")
"/Users/feeley/gambit/doc/h.c"
> (compile-file "h.c" options: '(obj))
"/Users/feeley/gambit/doc/h.o"
```

### link-incremental

```scheme
(link-incremental module-list [output: output]
                              [linker-name: linker-name]
                              [base: base]
                              [warnings?: warnings?]) ;; procedure
```

The first parameter must be a non empty list of strings naming Scheme modules to
link (the file extension may be omitted). An incremental link file is generated
for the modules specified in `module-list`. By default the link file generated is
named `last_.ext`, where last is the name of the last module, without the file
extension, and `ext` is the appropriate extension for the target. The name of the
generated link file can be specified with the `output` parameter. If `output` is a
string naming a directory then the link file is created in that directory.
Otherwise the name of the link file is `output`.

The base link file is specified by the `base` parameter, which must be a string.
By default the base link file is the Gambit runtime library link file
`~~lib/_gambit` (with extension appropriate for the target). However, when `base`
is supplied it is the name of the base link file (the file extension may be
omitted).

The `warnings?` parameter controls whether warnings are generated for undefined
references.

The following example shows how to build the executable program `hello` which
contains the two Scheme modules `h.scm` and `w.six`.

```shell
$ uname -srmp
Darwin 8.1.0 Power Macintosh powerpc
$ cat h.scm
(display "hello") (newline)
$ cat w.six
display("world"); newline();
$ gsc
Gambit v4.9.4

> (compile-file-to-target "h")
"/Users/feeley/gambit/doc/h.c"
> (compile-file-to-target "w")
"/Users/feeley/gambit/doc/w.c"
> (link-incremental '("h" "w") output: "hello.c")
"/Users/feeley/gambit/doc/hello_.c"
> ,q
$ gsc -obj h.c w.c hello.c
h.c:
w.c:
hello.c:
$ gcc h.o w.o hello.o -lgambit -o hello
$ ./hello
hello
world
```

### link-flat

```scheme
(link-flat module-list [output: output]
                       [linker-name: linker-name]
                       [warnings?: warnings?]) ;; procedure
```

The first parameter must be a non empty list of strings naming Scheme modules to
link (the file extension may be omitted). The first string must be the name of a
Scheme module or the name of a link file and the remaining strings must name
Scheme modules. A flat link file is generated for the modules specified in
`module-list`. By default the link file generated is named `last_.ext`, where `last`
is the name of the last module, without the file extension, and `ext` is the
appropriate extension for the target. The name of the generated link file can be
specified with the `output` parameter. If `output` is a string naming a directory
then the link file is created in that directory. Otherwise the name of the link
file is `output`. If a dynamically loadable object file is produced from the link
file `output`, then the name of the dynamically loadable object file must be
`output` stripped of its file extension.

The `warnings?` parameter controls whether warnings are generated for undefined
references.

The following example shows how to build the dynamically loadable object file
`lib.o1` which contains the two Scheme modules `m6.scm` and `m7.scm`.

```scheme
$ uname -srmp
Darwin 8.1.0 Power Macintosh powerpc
$ cat m6.scm
(define (f x) (g (* x x)))
$ cat m7.scm
(define (g y) (+ n y))
$ gsc
Gambit v4.9.4

> (compile-file-to-target "m6")
"/Users/feeley/gambit/doc/m6.c"
> (compile-file-to-target "m7")
"/Users/feeley/gambit/doc/m7.c"
> (link-flat '("m6" "m7") output: "lib.o1.c")
*** WARNING -- "*" is not defined,
***            referenced in: ("m6.c")
*** WARNING -- "+" is not defined,
***            referenced in: ("m7.c")
*** WARNING -- "n" is not defined,
***            referenced in: ("m7.c")
"/Users/feeley/gambit/doc/lib.o1.c"
> ,q
$ gcc -bundle -D___DYNAMIC m6.c m7.c lib.o1.c -o lib.o1
$ gsc
Gambit v4.9.4

> (load "lib")
*** WARNING -- Variable "n" used in module "m7" is undefined
"/Users/feeley/gambit/doc/lib.o1"
> (define n 10)
> (f 5)
35
> ,q
```

The `warnings` indicate that there are no definitions (`defines` or `set!`s) of the
variables `*`, `+` and `n` in the modules contained in the library. Before the library
is used, these variables will have to be bound; either implicitly (by the
runtime library) or explicitly.
