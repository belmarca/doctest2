---
title: Characters and strings
---

Gambit supports the Unicode character encoding standard. Scheme characters can
be any of the characters whose Unicode encoding is in the range `0` to
`#x10ffff` (inclusive) but not in the range `#xd800` to `#xdfff`. Source code
can also contain any Unicode character, however to read such source code
properly `gsi` and `gsc` must be told which character encoding to use for
reading the source code (i.e. ISO-8859-1, UTF-8, UTF-16, etc). This can be done
by specifying the runtime option `-:file-settings=...` or `-:io-settings=...`
when `gsi` and `gsc` are started.

## Extensions to character procedures

#### char->integer
#### integer->char

```scheme
(char->integer char) ;; procedure
(integer->char n) ;; procedure
```

The procedure `char->integer` returns the Unicode encoding of the character
`char`.

The procedure `integer->char` returns the character whose Unicode encoding is
the exact integer `n`.

For example:

```scheme
> (char->integer #\!)
33
> (integer->char 65)
#\A
> (integer->char (char->integer #\u1234))
#\u1234
> (integer->char #xd800)
*** ERROR IN (console)@4.1 -- (Argument 1) Out of range
(integer->char 55296)
```

#### char=?
#### char<?
#### char>?
#### char<=?
#### char>=?
#### char-ci=?
#### char-ci<?
#### char-ci>?
#### char-ci<=?
#### char-ci>=?

```scheme
(char=? char1 ...)     ;; procedure
(char<? char1 ...)     ;; procedure
(char>? char1 ...)     ;; procedure
(char<=? char1 ...)    ;; procedure
(char>=? char1 ...)    ;; procedure
(char-ci=? char1 ...)  ;; procedure
(char-ci<? char1 ...)  ;; procedure
(char-ci>? char1 ...)  ;; procedure
(char-ci<=? char1 ...) ;; procedure
(char-ci>=? char1 ...) ;; procedure
```

These procedures take any number of arguments including no argument. This is
useful to test if the elements of a list are sorted in a particular order. For
example, testing that the list of characters `lst` is sorted in nondecreasing
order can be done with the call `(apply char<? lst)`.

## Extensions to string procedures

#### string=?
#### string<?
#### string>?
#### string<=?
#### string>=?
#### string-ci=?
#### string-ci<?
#### string-ci>?
#### string-ci<=?
#### string-ci>=?

```scheme
(string=? string1 ...)     ;; procedure
(string<? string1 ...)     ;; procedure
(string>? string1 ...)     ;; procedure
(string<=? string1 ...)    ;; procedure
(string>=? string1 ...)    ;; procedure
(string-ci=? string1 ...)  ;; procedure
(string-ci<? string1 ...)  ;; procedure
(string-ci>? string1 ...)  ;; procedure
(string-ci<=? string1 ...) ;; procedure
(string-ci>=? string1 ...) ;; procedure
```

These procedures take any number of arguments including no argument. This is
useful to test if the elements of a list are sorted in a particular order. For
example, testing that the list of strings `lst` is sorted in nondecreasing order
can be done with the call `(apply string<? lst)`.
