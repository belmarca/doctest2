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
examined unless the `-f` option is present (see section [Customization](./customization)). The `-h`
and `-help` options print brief usage information on standard output and exit.
The `-v` option prints the system version string, system time stamp, operating
system type, and configure script options on standard output and exits. Runtime
options are explained in Runtime options.
