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
