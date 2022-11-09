---
title: Customization
---

# Customization

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
