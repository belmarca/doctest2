---
title: Console line-editing
---

# Console line-editing

The console implements a simple Scheme-friendly line-editing user-interface that
is enabled by default. It offers parentheses balancing, a history of previous
commands, symbol completion, and several emacs-compatible keyboard commands. The
user's input is displayed in a bold font and the output produced by the system
is in a plain font. The history of previous commands is saved in the file
`~/.gambit_history`. It is restored when a REPL is started.

Symbol completion is triggered with the tab key. When the cursor is after a
sequence of characters that can form a symbol, typing the tab key will search
the symbol table for the first symbol (in alphabetical order) that begins with
that sequence and insert that symbol. Typing the tab key in succession will
cycle through all symbols with that prefix. When all possible symbols have been
shown or there are no possible completions, the text reverts to the uncompleted
symbol and the bell is rung.

Here are the keyboard commands available (where the `M-` prefix means the escape
key is typed and the `C-` prefix means the control key is pressed):

`C-d`
: Generate an end-of-file when the line is empty, otherwise delete character at
  cursor.

`delete` or `backspace`
: Delete character before cursor.

`M-C-d`
: Delete word forward and keep a copy of this text on the clipboard.

`M-delete`
: Delete word backward and keep a copy of this text on the clipboard.

`M-backspace`
: Delete S-expression backward and keep a copy of this text on the clipboard.

`C-a`
: Move cursor to beginning of line.

`C-e`
: Move cursor to end of line.

`C-b` or `left-arrow`
: Move cursor left one character.

`M-b`
: Move cursor left one word.

`M-C-b` or `M-left-arrow`
: Move cursor left one S-expression.

`C-f` or `right-arrow`
: Move cursor right one character.

`M-f`
: Move cursor right one word.

`M-C-f` or `M-right-arrow`
: Move cursor right one S-expression.

`C-p` or `M-p` or `up-arrow`
: Move to previous line in history.

`C-n` or `M-n` or `down-arrow`
: Move to next line in history.

`C-t`
: Transpose character at cursor with previous character.

`M-t`
: Transpose word after cursor with previous word.

`M-C-t`
: Transpose S-expression after cursor with previous S-expression.

`C-l`
: Clear console and redraw line being edited.

`C-nul`
: Set the mark to the cursor.

`C-w`
: Delete the text between the cursor and the mark and keep a copy of this text
  on the clipboard.

`C-k`
: Delete the text from the cursor to the end of the line and keep a copy of this
  text on the clipboard.

`C-y`
: Paste the text that is on the clipboard.

`F8`
: Same as typing `#||#,c;` (REPL command to continue the computation).

`F9`
: Same as typing `#||#,-;` (REPL command to move to newer frame).

`F10`
: Same as typing `#||#,+;` (REPL command to move to older frame).

`F11`
: Same as typing `#||#,s;` (REPL command to step the computation).

`F12`
: Same as typing `#||#,l;` (REPL command to leap the computation).

On macOS, depending on your configuration, you may have to press the `fn` key to
access the function key `F12` and the `option` key to access the other function
keys.

On Microsoft Windows the clipboard is the system clipboard. This allows text to
be copied and pasted between the program and other applications. On other
operating systems the clipboard is internal to the program (it is not integrated
with the operating system).


