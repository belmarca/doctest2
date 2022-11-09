---
title: Module management mode
---

# Module management mode

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


