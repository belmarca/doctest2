---
title: Batch mode
---

# Batch mode

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

