---
title: Emacs interface
---

# Emacs interface

Gambit comes with the Emacs package `gambit.el` which provides a nice
environment for running Gambit from within the Emacs editor. This package
filters the standard output of the Gambit process and when it intercepts a
location information (in the format `stream@line.column` where stream is either
`(stdin)` when the expression was obtained from standard input, `(console)` when
the expression was obtained from the console, or a string naming a file) it
opens a window to highlight the corresponding expression.

To use this package, make sure the file `gambit.el` is accessible from your
`load-path` and that the following lines are in your `.emacs` file:

```scheme
(autoload 'gambit-inferior-mode "gambit" "Hook Gambit mode into cmuscheme.")
(autoload 'gambit-mode "gambit" "Hook Gambit mode into scheme.")
(add-hook 'inferior-scheme-mode-hook (function gambit-inferior-mode))
(add-hook 'scheme-mode-hook (function gambit-mode))
(setq scheme-program-name "gsi -:debug=-")
```

Alternatively, if you don’t mind always loading this package, you can simply add
this line to your `.emacs` file:

```scheme
(require 'gambit)
```

You can then start an inferior Gambit process by typing `M-x run-scheme`. The
commands provided in `cmuscheme` mode will be available in the Gambit
interaction buffer (i.e. `*scheme*`) and in buffers attached to Scheme source
files. Here is a list of the most useful commands (for a complete list type `C-h
m` in the Gambit interaction buffer):

`C-x C-e`
: Evaluate the expression which is before the cursor (the expression will be
  copied to the Gambit interaction buffer).

`C-c C-z`
: Switch to Gambit interaction buffer.

`C-c C-l`
: Load a file (file attached to current buffer is default) using (load file).

`C-c C-k`
: Compile a file (file attached to current buffer is default) using
  `(compile-file file)`.

The file `gambit.el` provides these additional commands:

`F8` or `C-c c`
: Continue the computation (same as typing `#||#,c;` to the REPL).

`F9` or `C-c ]`
: Move to newer frame (same as typing `#||#,-;` to the REPL).

`F10` or `C-c [`
: Move to older frame (same as typing `#||#,+;` to the REPL).

`F11` or `C-c s`
: Step the computation (same as typing `#||#,s;` to the REPL).

`F12` or `C-c l`
: Leap the computation (same as typing `#||#,l;` to the REPL).

`C-c _`
: Removes the last window that was opened to highlight an expression.

The two keystroke version of these commands can be shortened to `M-c`, `M-[`,
`M-]`, `M-s`, `M-l`, and `M-_` respectively by adding this line to your `.emacs`
file:

```scheme
(setq gambit-repl-command-prefix "\e")
```

This is more convenient to type than the two keystroke `C-c` based sequences but
the purist may not like this because it does not follow normal Emacs
conventions.

Here is what a typical `.emacs` file will look like:

```scheme
(setq load-path ; add directory containing gambit.el
  (cons "/usr/local/Gambit/share/emacs/site-lisp"
        load-path))
(setq scheme-program-name "/tmp/gsi -:debug=-") ; if gsi not in executable path
(setq gambit-highlight-color "gray") ; if you don't like the default
(setq gambit-repl-command-prefix "\e") ; if you want M-c, M-s, etc
(require 'gambit)
```

