# Runtime options

Both `gsi` and `gsc` as well as executable programs compiled and linked using `gsc`
take a `-:` option which supplies parameters to the runtime system. This option
must appear first on the command line. The colon is followed by a comma
separated list of options with no intervening spaces. The available options are:

`min-heap=SIZE` or the shorthand `mSIZE`
: Set minimum heap size.

`max-heap=SIZE` or the shorthand `hSIZE`
: Set maximum heap size.

`live-ratio=RATIO` or the shorthand `lRATIO`
: Set the ratio of heap that is live after a garbage collection.

`gambit` or the (deprecated) shorthand `S`
: Select Gambit Scheme mode. This is the default mode.

`r5rs` or the (deprecated) shorthand `s`
: Select R5RS Scheme mode.

`r7rs`
: Select R7RS Scheme mode.

`debug[=[OPT...]]` or the shorthand `d[OPT...]`
: Set debugging options.

`~~NAME=DIRECTORY`
: Override the `NAME` installation directory.

`add-arg=ARGUMENT` or the shorthand `+ARGUMENT`
: Add `ARGUMENT` to the command line before other arguments.

`io-settings=[IO...]` or the shorthand `i[IO...]`
: Set general I/O settings.

`file-settings=[IO...]` or the shorthand `f[IO...]`
: Set general file I/O settings.

`stdio-settings=[IO...]` or the shorthand `-[IO...]`
: Set general stdio settings.

`0[IO...]`
: Set `stdin` settings.

`1[IO...]`
: Set `stdout` settings.

`2[IO...]`
: Set `stderr` settings.

`terminal-settings=[IO...]` or the shorthand `t[IO...]`
: Set terminal I/O settings.

`search=[DIR]`
: Set or reset module search order.

`whitelist=[SOURCE]`
: Set or reset the whitelist of trusted sources for automatic installation of hosted modules.

`ask-install=WHEN`
: Set automatic installation confirmation mode.

The `min-heap=SIZE` and `max-heap=SIZE` options set limits on the size of the
heap. The `SIZE` is an integer that may be followed by `G` (gigabytes), `M`
(megabytes), or `K` or nothing (kilobytes). The heap will not shrink lower than
the minimum heap size which defaults to `0`. The heap will not grow larger than
the maximum heap size if it is set (by default the heap may grow until the
virtual memory is exhausted).

The `live-ratio=RATIO` option sets the percentage of the heap that will be
occupied with live objects after the heap is resized at the end of a garbage
collection. `RATIO` is an integer between `1` and `100` inclusively indicating
the desired percentage. The garbage collector resizes the heap to reach this
percentage occupation (roughly), within the limits of the `min-heap` and
`max-heap` options. By default, the percentage is `50`.

The `gambit`, `r5rs` and `r7rs` options configure the runtime system to conform
to Gambit Scheme, R5RS Scheme and R7RS Scheme respectively. The reader is
case-insensitive in `r5rs` mode, and is case-sensitive in `r7rs` and `gambit`
modes. The reader supports keywords only in `gambit` mode, which is the default
mode.

The `debug=OPT,...` option sets various debugging options. The equal sign is
followed by a sequence of letters indicating suboptions.

`p`
: Uncaught exceptions will be treated as "errors" in the primordial thread only.

`a`
: Uncaught exceptions will be treated as "errors" in all threads.

`r`
: When an "error" occurs a new REPL will be started.

`s`
: When an "error" occurs a new REPL will be started. Moreover the program starts
  in single-stepping mode.

`q`
: When an "error" occurs the program will terminate with a nonzero exit status.

`R`
When a user interrupt occurs a new REPL will be started. User interrupts are
typically obtained by typing `<^C>`. Note that with some system configurations
`<^C>` abruptly terminates the process. For example, under Microsoft Windows,
`<^C>` works fine with the standard console but with the MSYS terminal window it
terminates the process.

`D`
: When a user interrupt occurs it will be deferred until the parameter object
  `current-user-interrupt-handler` is set or bound.

`Q`
: When a user interrupt occurs the program will terminate with a nonzero exit
  status.

`LEVEL`
The verbosity level is set to `LEVEL`, a digit from `0` to `9`. At level `0` the
runtime system will not display error messages and warnings. At level `1` and
above error messages and warnings are displayed. At level `2` and above a
backtrace is displayed. At level `3` and above variable bindings are displayed
in the backtrace. At level `5` and above garbage collection reports are
displayed during program execution.

`c`
: The REPL interaction channel will be the console.

`-`
: The REPL interaction channel will be standard input and standard output.

`+`
: The REPL interaction channel will be standard input and standard output and
  standard error.

`@[HOST][:PORT]`
: When a REPL is started by a thread a connection will be established with the
address `HOST:PORT` and that will be the REPL’s interaction channel. The default
`HOST` is `127.0.0.1` and the default `PORT` is `44556`.

`$[INTF][:PORT]`
: The runtime system will open a socket to listen on port number `PORT` for incoming
connections on the network interface with address `INTF`. The default `INTF` is
`127.0.0.1` and the default `PORT` is `44555`.

The default debugging options are equivalent to `debug=pqQ1-` (i.e. an uncaught
exception in the primordial thread terminates the program after displaying an
error message). When the option debug is used without suboptions it is
equivalent to `debug=prR1-` (i.e. a new REPL is started only when an uncaught
exception occurs in the primordial thread). When `gsi` and `gsc` are running the
main REPL, the debugging options are changed to cause errors in the primordial
thread and user interrupts to start a nested REPL.

The `~~NAME=DIRECTORY` option overrides the setting of the `NAME` installation
directory. If `NAME` is empty, it will override the central installation
directory.

The `add-arg=ARGUMENT` option adds the text that follows to the command line
before other arguments.

The option `io-settings=[IO...]` sets the default I/O settings of all types of
ports. The option `file-settings=[IO...]` sets the default I/O settings for
ports associated to files. The option `stdio-settings=[IO...]` sets the default
I/O settings for ports associated to `stdio` (but finer control is possible with
`0[IO...]`, `1[IO...]`, and `2[IO...]` that set the I/O settings of `stdin`,
`stdout`, and stderr respectively). The option `terminal-settings=[IO...]`
overrides the default I/O settings for ports associated to terminals. The
default character encoding, end-of-line encoding and buffering can be set.
Moreover, for terminals the line-editing feature can be enabled or disabled.
Each IO is a one or two letter code as follows:

`A`
: ASCII character encoding.

`1`
: ISO-8859-1 character encoding.

`2`
: UCS-2 character encoding.

`4`
: UCS-4 character encoding.

`6`
: UTF-16 character encoding.

`8`
: UTF-8 character encoding.

`U`
: UTF character encoding with fallback to UTF-8 on input if no BOM is present.

`UA`
: UTF character encoding with fallback to ASCII on input if no BOM is present.

`U1`
: UTF character encoding with fallback to ISO-8859-1 on input if no BOM is
  present.

`U6`
: UTF character encoding with fallback to UTF-16 on input if no BOM is present.

`U8`
: UTF character encoding with fallback to UTF-8 on input if no BOM is present.

`L`
: If the `LC_ALL` or `LC_CTYPE` or `LANG` environment variables end with
  `.UTF-8` or `.ISO-8859-1` or `.LATIN-1` (or a variation) set the character
  encoding accordingly.

`c`
: End-of-line is encoded as `CR` (carriage-return).

`l`
: End-of-line is encoded as `LF` (linefeed)

`cl`
: End-of-line is encoded as `CR-LF`.

`u`
: Unbuffered I/O.

`n`
: Line buffered I/O (`n` for "at newline").

`f`
: Fully buffered I/O.

`r`
: Illegal character encoding is treated as an error (exception raised).

`R`
: Silently replace illegal character encodings with Unicode character `#xfffd`
  (replacement character).

`e`
: Enable line-editing (applies to terminals only).

`E`
: Disable line-editing (applies to terminals only).

The `search=[DIR]` option adds `DIR` to the head of the list of module search
order directories, unless `DIR` is empty, in which case it is set to the empty
list. The initial setting of the list of module search order directories is
`~~lib` followed by `~~userlib`.

When a hosted module can't be found in the directories on the list of module
search order directories it will be automatically installed if it is from a
source on the whitelist of trusted sources, which initially contains only
`github.com/gambit`. The `whitelist=[SOURCE]` option adds `SOURCE` to the
whitelist, unless `SOURCE` is empty in which case the whitelist will be set to
the empty list (no source is trusted).

The `ask-install=WHEN` option sets the automatic installation mode confirmation
mode to `WHEN`, which is one of `always`, `repl`, and `never`. When a hosted
module can't be found in the directories on the list of module search order
directories and it is from a source not on the whitelist the runtime system will
ask for installation confirmation when `WHEN` is `always`, or when a REPL has
already been started for the current thread and WHEN is `repl`. In the `never`
mode the runtime system will not install the module automatically. The default
mode is `repl`.

When a program's execution starts, the runtime system obtains the runtime
options by processing in turn various sources of runtime options: the defaults,
the environment variable `GAMBOPT`, the script line of the source code, and,
unless the program is an interpreted script, the first command line argument of
the program. Any runtime option can be overriden by a subsequent source of
runtime options. It is sometimes useful to prevent overriding the runtime
options of the script line. This can be achieved by starting the script line
runtime options with `-:,`. In this case the environment variable `GAMBOPT` is
ignored, and the first command line argument of the program is not used for
runtime options (it is treated like a normal command line argument even if it
starts with `-:`).

For example:

```shell
$ export GAMBOPT=debug=0,~~=~/my-gambit2
$ gsi -e '(pretty-print (path-expand "~~")) (/ 1 0)'
"/Users/feeley/my-gambit2/"
$ echo $?
70
$ gsi -:debug=1 -e '(pretty-print (path-expand "~~")) (/ 1 0)'
"/Users/feeley/my-gambit2/"
*** ERROR IN (string)@1.35 -- Divide by zero
(/ 1 0)
```

