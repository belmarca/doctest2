---
title: Readtables
---

# Readtables

Readtables control the external textual representation of Scheme objects, that
is the encoding of Scheme objects using characters. Readtables affect the
behavior of the reader (i.e. the `read` procedure and the parser used by the
`load` procedure and the interpreter and compiler) and the printer (i.e. the
procedures `write`, `display`, `print`, `println`, `pretty-print`, and `pp`, and
the procedure used by the REPL to print results). To preserve write/read
invariance the printer and reader must be using compatible readtables. For
example a symbol which contains upper case letters will be printed with special
escapes if the readtable indicates that the reader is case-insensitive.

Readtables are immutable records whose fields specify various textual
representation aspects. There are accessor procedures to retrieve the content of
specific fields. There are also functional update procedures that create a copy
of a readtable, with a specific field set to a new value.

#### readtable?

```scheme
(readtable? obj) ;; procedure
```

This procedure returns `#t` when `obj` is a readtable and `#f` otherwise.

For example:

```scheme
> (readtable? (current-readtable))
#t
> (readtable? 123)
#f
```

#### readtable-case-conversion?
#### readtable-case-conversion?-set

```scheme
(readtable-case-conversion? readtable)	procedure
(readtable-case-conversion?-set readtable new-value)	procedure
```

The procedure readtable-case-conversion? returns the content of the ‘case-conversion?’ field of readtable. When the content of this field is #f, the reader preserves the case of symbols, keyword and named characters that are read (i.e. Ice and ice are distinct symbols). When the content of this field is the symbol upcase, the reader converts to uppercase (i.e. Ice is read as the symbol (string->symbol "ICE")). Otherwise the reader converts using string-foldcase, which for many letters converts them to lowercase (i.e. Ice is read as the symbol (string->symbol "ice")).

The procedure readtable-case-conversion?-set returns a copy of readtable where only the ‘case-conversion?’ field has been changed to new-value.

For example:

 	
> (output-port-readtable-set!
    (repl-output-port)
    (readtable-case-conversion?-set
      (output-port-readtable (repl-output-port))
      #f))
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-case-conversion?-set
      (input-port-readtable (repl-input-port))
      #f))
> 'Ice
Ice
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-case-conversion?-set
      (input-port-readtable (repl-input-port))
      #t))
> 'Ice
ice
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-case-conversion?-set
      (input-port-readtable (repl-input-port))
      'upcase))
> 'Ice
ICE
(readtable-keywords-allowed? readtable)	procedure
(readtable-keywords-allowed?-set readtable new-value)	procedure
The procedure readtable-keywords-allowed? returns the content of the ‘keywords-allowed?’ field of readtable. When the content of this field is #f, the reader does not recognize keyword objects (i.e. :foo and foo: are read as the symbols (string->symbol ":foo") and (string->symbol "foo:") respectively). When the content of this field is the symbol prefix, the reader recognizes keyword objects that start with a colon, as in Common Lisp (i.e. :foo is read as the keyword (string->keyword "foo")). Otherwise the reader recognizes keyword objects that end with a colon, as in DSSSL (i.e. foo: is read as the symbol (string->symbol "foo")).

The procedure readtable-keywords-allowed?-set returns a copy of readtable where only the ‘keywords-allowed?’ field has been changed to new-value.

For example:

 	
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-keywords-allowed?-set
      (input-port-readtable (repl-input-port))
      #f))
> (map keyword? '(foo :foo foo:))
(#f #f #f)
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-keywords-allowed?-set
      (input-port-readtable (repl-input-port))
      #t))
> (map keyword? '(foo :foo foo:))
(#f #f #t)
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-keywords-allowed?-set
      (input-port-readtable (repl-input-port))
      'prefix))
> (map keyword? '(foo :foo foo:))
(#f #t #f)
(readtable-sharing-allowed? readtable)	procedure
(readtable-sharing-allowed?-set readtable new-value)	procedure
The procedure readtable-sharing-allowed? returns the content of the ‘sharing-allowed?’ field of readtable. The reader recognizes the #n# and #n=datum notation for shared and circular structures and the printer uses this notation depending on the content of the ‘sharing-allowed?’ field and the printing primitive used. When the content of the ‘sharing-allowed?’ field is the symbol default, the procedure write-shared will use this notation for shared and circular structures, the procedures write, display, pp, pretty-print, print, and println will use this notation for circular structures, and the procedure write-simple does not use this notation. When the content of the ‘sharing-allowed?’ field is #f, the printing procedures will not use this notation. When the content of the ‘sharing-allowed?’ field is #t, the printing procedures will use this notation for shared and circular structures. Finally, when the content of the ‘sharing-allowed?’ field is the symbol serialize, the printer uses a special external representation that the reader understands and that extends write/read invariance to the following types: records, procedures and continuations. Note that an object can be serialized and deserialized if and only if all of its components are serializable.

The procedure readtable-sharing-allowed?-set returns a copy of readtable where only the ‘sharing-allowed?’ field has been changed to new-value.

Here is a simple example:

 	
> (define (wr obj allow?)
    (call-with-output-string
      (lambda (p)
        (output-port-readtable-set!
          p
          (readtable-sharing-allowed?-set
            (output-port-readtable p)
            allow?))
        (write obj p))))
> (define (rd str allow?)
    (call-with-input-string
      str
      (lambda (p)
        (input-port-readtable-set!
          p
          (readtable-sharing-allowed?-set
            (input-port-readtable p)
            allow?))
        (read p))))
> (define x (list 1 2 3))
> (set-car! (cdr x) (cddr x))
> (wr x #f)
"(1 (3) 3)"
> (wr x #t)
"(1 #0=(3) . #0#)"
> (define y (rd (wr x #t) #t))
> y
(1 (3) 3)
> (eq? (cadr y) (cddr y))
#t
> (define f #f)
> (let ((free (expt 2 10)))
  (set! f (lambda (x) (+ x free))))
> (define s (wr f 'serialize))
> (string-length s)
4196
> (define g (rd s 'serialize))
> (eq? f g)
#f
> (g 4)
1028
Continuations are tricky to serialize because they contain a dynamic environment and this dynamic environment may contain non-serializable objects, in particular ports attached to operating-system streams such as files, the console or standard input/output. Indeed, all dynamic environments contain a binding for the current-input-port and current-output-port. Moreover, any thread that has started a REPL has a continuation which refers to the repl-context object in its dynamic environment. A repl-context object contains the interaction channel, which is typically connected to a non-serializable port, such as the console. Another problem is that the parameterize form saves the old binding of the parameter in the continuation, so it is not possible to eliminate the references to these ports in the continuation by using the parameterize form alone.

Serialization of continuations can be achieved dependably by taking advantage of string-ports, which are serializable objects (unless there is a blocked thread), and the following features of threads: they inherit the dynamic environment of the parent thread and they start with an initial continuation that contains only serializable objects. So a thread created in a dynamic environment where current-input-port and current-output-port are bound to a dummy string-port has a serializable continuation.

Here is an example where continuations are serialized:

 	
> (define (wr obj)
    (call-with-output-string
     (lambda (p)
       (output-port-readtable-set!
        p
        (readtable-sharing-allowed?-set
         (output-port-readtable p)
         'serialize))
       (write obj p))))
> (define (rd str)
    (call-with-input-string
     str
     (lambda (p)
       (input-port-readtable-set!
        p
        (readtable-sharing-allowed?-set
         (input-port-readtable p)
         'serialize))
       (read p))))
> (define fifo (open-vector))
> (define (suspend-and-die!)
    (call-with-current-continuation
     (lambda (k)
       (write (wr k) fifo)
       (newline fifo)
       (force-output fifo)
       (thread-terminate! (current-thread)))))
> (let ((dummy-port (open-string)))
    (parameterize ((current-input-port dummy-port)
                   (current-output-port dummy-port))
      (thread-start!
       (make-thread
        (lambda ()
          (* 100
             (suspend-and-die!)))))))
#<thread #2>
> (define s (read fifo))
> (thread-join!
    (thread-start!
      (make-thread
        (lambda ()
          ((rd s) 111)))))
11100
> (thread-join!
    (thread-start!
      (make-thread
        (lambda ()
          ((rd s) 222)))))
22200
> (string-length s)
13114
(readtable-eval-allowed? readtable)	procedure
(readtable-eval-allowed?-set readtable new-value)	procedure
The procedure readtable-eval-allowed? returns the content of the ‘eval-allowed?’ field of readtable. The reader recognizes the #.expression notation for read-time evaluation if and only if the content of the ‘eval-allowed?’ field is not #f.

The procedure readtable-eval-allowed?-set returns a copy of readtable where only the ‘eval-allowed?’ field has been changed to new-value.

For example:

 	
> (input-port-readtable-set!
    (repl-input-port)
    (readtable-eval-allowed?-set
      (input-port-readtable (repl-input-port))
      #t))
> '(5 plus 7 is #.(+ 5 7))
(5 plus 7 is 12)
> '(buf = #.(make-u8vector 25))
(buf = #u8(0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0))
(readtable-write-cdr-read-macros? readtable)	procedure
(readtable-write-cdr-read-macros?-set readtable new-value)	procedure
(readtable-write-extended-read-macros? readtable)	procedure
(readtable-write-extended-read-macros?-set readtable new-value)	procedure
The procedure readtable-write-cdr-read-macros? returns the content of the ‘write-cdr-read-macros?’ field of readtable. The procedure readtable-write-extended-read-macros? returns the content of the ‘write-extended-read-macros?’ field of readtable.

At all times the printer uses read-macros in its output for datums of the form (quote datum), (quasiquote datum), (unquote datum), and (unquote-splicing datum). That is the following read-macro notations will be used respectively: 'datum, `datum, ,datum, and ,@datum. Moreover, normally the read-macros will not be used when the form appears in the cdr of a list, for example (foo quote bar), (foo . (quote bar)) and (foo . 'bar) will all be printed as (foo quote bar).

When the content of the ‘write-cdr-read-macros?’ field is not #f, the printer will use read-macros when the forms appear in the cdr of a list. For example (foo quote bar) will be printed as (foo . 'bar). When the content of the ‘write-extended-read-macros?’ field is not #f, the printer will also use extended read-macros, for example #'datum in place of (syntax datum).

The procedure readtable-write-cdr-read-macros?-set returns a copy of readtable where only the ‘write-cdr-read-macros?’ field has been changed to new-value. The procedure readtable-write-extended-read-macros?-set returns a copy of readtable where only the ‘write-extended-read-macros?’ field has been changed to new-value.

For example:

 	
> (output-port-readtable-set!
    (repl-output-port)
    (readtable-write-extended-read-macros?-set
      (output-port-readtable (repl-output-port))
      #t))
> '(foo (syntax bar))
(foo #'bar)
> '(foo syntax bar)
(foo syntax bar)
> (output-port-readtable-set!
    (repl-output-port)
    (readtable-write-cdr-read-macros?-set
      (output-port-readtable (repl-output-port))
      #t))
> '(foo syntax bar)
(foo . #'bar)
(readtable-max-write-level readtable)	procedure
(readtable-max-write-level-set readtable new-value)	procedure
The procedure readtable-max-write-level returns the content of the ‘max-write-level’ field of readtable. The printer will display an ellipsis for the elements of lists and vectors that are nested deeper than this level.

The procedure readtable-max-write-level-set returns a copy of readtable where only the ‘max-write-level’ field has been changed to new-value, which must be an nonnegative fixnum.

For example:

 	
> (define (wr obj n)
    (call-with-output-string
      (lambda (p)
        (output-port-readtable-set!
          p
          (readtable-max-write-level-set
            (output-port-readtable p)
            n))
        (write obj p))))
> (wr '(a #(b (c c) #u8(9 9 9) b) a) 3)
"(a #(b (c c) #u8(9 9 9) b) a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) a) 2)
"(a #(b (...) #u8(...) b) a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) a) 1)
"(a #(...) a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) a) 0)
"(...)"
> (wr 'hello 0)
"hello"
(readtable-max-write-length readtable)	procedure
(readtable-max-write-length-set readtable new-value)	procedure
The procedure readtable-max-write-length returns the content of the ‘max-write-length’ field of readtable. The printer will display an ellipsis for the elements of lists and vectors that are at an index beyond that length.

The procedure readtable-max-write-length-set returns a copy of readtable where only the ‘max-write-length’ field has been changed to new-value, which must be an nonnegative fixnum.

For example:

 	
> (define (wr obj n)
    (call-with-output-string
      (lambda (p)
        (output-port-readtable-set!
          p
          (readtable-max-write-length-set
            (output-port-readtable p)
            n))
        (write obj p))))
> (wr '(a #(b (c c) #u8(9 9 9) b) . a) 4)
"(a #(b (c c) #u8(9 9 9) b) . a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) . a) 3)
"(a #(b (c c) #u8(9 9 9) ...) . a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) . a) 2)
"(a #(b (c c) ...) . a)"
> (wr '(a #(b (c c) #u8(9 9 9) b) . a) 1)
"(a ...)"
> (wr '(a #(b (c c) #u8(9 9 9) b) . a) 0)
"(...)"
(readtable-max-unescaped-char readtable)	procedure
(readtable-max-unescaped-char-set readtable new-value)	procedure
The procedure readtable-max-unescaped-char returns the content of the ‘max-unescaped-char’ field of readtable. The printer will display using an escape sequence any character within symbols, strings and character objects greater than ‘max-unescaped-char’. When ‘max-unescaped-char’ is #f, the default value, the printer will take into account the output port and use an escape sequence for any character that isn’t supported by the port’s character encoding.

The procedure readtable-max-unescaped-char-set returns a copy of readtable where only the ‘max-unescaped-char’ field has been changed to new-value, which must be a character or #f.

For example:

 	
> (define rt (output-port-readtable (repl-output-port)))
> (readtable-max-unescaped-char rt)
#\delete
> (string (integer->char 233))
"\351"
> (define (f c)
    (with-output-to-string
     (list readtable: (readtable-max-unescaped-char-set rt c))
     (lambda () (write (string (integer->char 233))))))
> (f #\delete)
"\"\\351\""
> (string-length (f #\delete))
6
> (f #\U0010ffff)
"\"\351\""
> (string-length (f #\U0010ffff))
3
> (output-port-readtable-set!
   (repl-output-port)
   (readtable-max-unescaped-char-set rt #\U0010ffff))
> (string (integer->char 233))
"é"
(readtable-start-syntax readtable)	procedure
(readtable-start-syntax-set readtable new-value)	procedure
The procedure readtable-start-syntax returns the content of the ‘start-syntax’ field of readtable. The reader uses this field to determine in which syntax to start parsing the input. When the content of this field is the symbol six, the reader starts in the infix syntax. Otherwise the reader starts in the prefix syntax.

The procedure readtable-start-syntax-set returns a copy of readtable where only the ‘start-syntax’ field has been changed to new-value.

For example:

 	
> (+ 2 3)
5
> (input-port-readtable-set!
   (repl-input-port)
   (readtable-start-syntax-set
     (input-port-readtable (repl-input-port))
     'six))
> 2+3;
5
> exit();
