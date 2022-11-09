---
title: Process exit status
---

# Process exit status

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


