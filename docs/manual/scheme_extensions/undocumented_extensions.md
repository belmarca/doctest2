---
title: Undocumented extensions
---

# Undocumented extensions

The procedures in this section are not yet documented.

#### make-thread-group
```scheme
(make-thread-group [name [thread-group]]) ;; procedure
```

#### thread-group?
```scheme
(thread-group? obj) ;; procedure
```

#### thread-group-name
```scheme
(thread-group-name thread-group) ;; procedure
```

#### thread-group-parent
```scheme
(thread-group-parent thread-group) ;; procedure
```

#### thread-group-resume!
```scheme
(thread-group-resume! thread-group) ;; procedure
```

#### thread-group-suspend!
```scheme
(thread-group-suspend! thread-group) ;; procedure
```

#### thread-group-terminate!
```scheme
(thread-group-terminate! thread-group) ;; procedure
```

#### thread-group->thread-group-list
```scheme
(thread-group->thread-group-list thread-group) ;; procedure
```

#### thread-group->thread-group-vector
```scheme
(thread-group->thread-group-vector thread-group) ;; procedure
```

#### thread-group->thread-list
```scheme
(thread-group->thread-list thread-group) ;; procedure
```

#### thread-group->thread-vector
```scheme
(thread-group->thread-vector thread-group) ;; procedure
```

#### thread-group-specific
```scheme
(thread-group-specific thread-group) ;; procedure
```

#### thread-group-specific-set!
```scheme
(thread-group-specific-set! thread-group obj) ;; procedure
```

#### thread-state
```scheme
(thread-state thread) ;; procedure
```

#### thread-state-uninitialized?
```scheme
(thread-state-uninitialized? thread-state) ;; procedure
```

#### thread-state-initialized?
```scheme
(thread-state-initialized? thread-state) ;; procedure
```

#### thread-state-running?
```scheme
(thread-state-running? thread-state) ;; procedure
```

#### thread-state-running-processor
```scheme
(thread-state-running-processor thread-state) ;; procedure
```

#### thread-state-waiting?
```scheme
(thread-state-waiting? thread-state) ;; procedure
```

#### thread-state-waiting-for
```scheme
(thread-state-waiting-for thread-state) ;; procedure
```

#### thread-state-waiting-timeout
```scheme
(thread-state-waiting-timeout thread-state) ;; procedure
```

#### thread-state-normally-terminated?
```scheme
(thread-state-normally-terminated? thread-state) ;; procedure
```

#### thread-state-normally-terminated-result
```scheme
(thread-state-normally-terminated-result thread-state) ;; procedure
```

#### thread-state-abnormally-terminated?
```scheme
(thread-state-abnormally-terminated? thread-state) ;; procedure
```

#### thread-state-abnormally-terminated-reason
```scheme
(thread-state-abnormally-terminated-reason thread-state) ;; procedure
```

#### top
```scheme
(top [timeout [thread-group [port]]]) ;; procedure
```

#### thread-interrupt!
```scheme
(thread-interrupt! thread [thunk]) ;; procedure
```

#### thread-suspend!
```scheme
(thread-suspend! thread) ;; procedure
```

#### thread-resume!
```scheme
(thread-resume! thread) ;; procedure
```

#### thread-thread-group
```scheme
(thread-thread-group thread) ;; procedure
```

#### define-type-of-thread
```scheme
(define-type-of-thread name field…) ;; special form
```

#### thread-init!
```scheme
(thread-init! thread thunk [name [thread-group]]) ;; procedure
```

#### initialized-thread-exception?
```scheme
(initialized-thread-exception? obj) ;; procedure
```

#### initialized-thread-exception-procedure
```scheme
(initialized-thread-exception-procedure exc) ;; procedure
```

#### initialized-thread-exception-arguments
```scheme
(initialized-thread-exception-arguments exc) ;; procedure
```

#### uninitialized-thread-exception?
```scheme
(uninitialized-thread-exception? obj) ;; procedure
```

#### uninitialized-thread-exception-procedure
```scheme
(uninitialized-thread-exception-procedure exc) ;; procedure
```

#### uninitialized-thread-exception-arguments
```scheme
(uninitialized-thread-exception-arguments exc) ;; procedure
```

#### inactive-thread-exception?
```scheme
(inactive-thread-exception? obj) ;; procedure
```

#### inactive-thread-exception-procedure
```scheme
(inactive-thread-exception-procedure exc) ;; procedure
```

#### inactive-thread-exception-arguments
```scheme
(inactive-thread-exception-arguments exc) ;; procedure
```

#### rpc-remote-error-exception?
```scheme
(rpc-remote-error-exception? obj) ;; procedure
```

#### rpc-remote-error-exception-procedure
```scheme
(rpc-remote-error-exception-procedure exc) ;; procedure
```

#### rpc-remote-error-exception-arguments
```scheme
(rpc-remote-error-exception-arguments exc) ;; procedure
```

#### rpc-remote-error-exception-message
```scheme
(rpc-remote-error-exception-message exc) ;; procedure
```

#### invalid-utf8-encoding-exception?
```scheme
(invalid-utf8-encoding-exception? obj) ;; procedure
```

#### invalid-utf8-encoding-exception-procedure
```scheme
(invalid-utf8-encoding-exception-procedure exc) ;; procedure
```

#### invalid-utf8-encoding-exception-arguments
```scheme
(invalid-utf8-encoding-exception-arguments exc) ;; procedure
```

#### module-not-found-exception?
```scheme
(module-not-found-exception? obj) ;; procedure
```

#### module-not-found-exception-procedure
```scheme
(module-not-found-exception-procedure exc) ;; procedure
```

#### module-not-found-exception-arguments
```scheme
(module-not-found-exception-arguments exc) ;; procedure
```

#### processor?
```scheme
(processor? obj) ;; procedure
```

#### current-processor
```scheme
(current-processor) ;; procedure
```

#### processor-id
```scheme
(processor-id processor) ;; procedure
```

#### timeout->time
```scheme
(timeout->time timeout) ;; procedure
```

#### current-second
```scheme
(current-second) ;; procedure
```

#### current-jiffy
```scheme
(current-jiffy) ;; procedure
```

#### jiffies-per-second
```scheme
(jiffies-per-second) ;; procedure
```

#### get-environment-variable
```scheme
(get-environment-variable name) ;; procedure
```

#### get-environment-variables
```scheme
(get-environment-variables) ;; procedure
```

#### executable-path
```scheme
(executable-path) ;; procedure
```

#### command-name
```scheme
(command-name) ;; procedure
```

#### command-args
```scheme
(command-args) ;; procedure
```

#### script-file
```scheme
(script-file) ;; procedure
```

#### script-directory
```scheme
(script-directory) ;; procedure
```

#### open-dummy
```scheme
(open-dummy) ;; procedure
```

#### port-settings-set!
```scheme
(port-settings-set! port settings) ;; procedure
```

#### port-io-exception-handler-set!
```scheme
(port-io-exception-handler-set! port handler) ;; procedure
```

#### input-port-bytes-buffered
```scheme
(input-port-bytes-buffered port) ;; procedure
```

#### input-port-characters-buffered
```scheme
(input-port-characters-buffered port) ;; procedure
```

#### nonempty-input-port-character-buffer-exception?
```scheme
(nonempty-input-port-character-buffer-exception? obj) ;; procedure
```

#### nonempty-input-port-character-buffer-exception-arguments
```scheme
(nonempty-input-port-character-buffer-exception-arguments exc) ;; procedure
```

#### nonempty-input-port-character-buffer-exception-procedure
```scheme
(nonempty-input-port-character-buffer-exception-procedure exc) ;; procedure
```

#### repl-input-port
```scheme
(repl-input-port) ;; procedure
```

#### repl-output-port
```scheme
(repl-output-port) ;; procedure
```

#### repl-error-port
```scheme
(repl-error-port) ;; procedure
```

#### console-port
```scheme
(console-port) ;; procedure
```

#### current-user-interrupt-handler
```scheme
(current-user-interrupt-handler [handler]) ;; procedure
```

#### default-user-interrupt-handler
```scheme
(default-user-interrupt-handler) ;; procedure
```

#### defer-user-interrupts
```scheme
(defer-user-interrupts) ;; procedure
```

#### primordial-exception-handler
```scheme
(primordial-exception-handler exc) ;; procedure
```

#### err-code->string
```scheme
(err-code->string code) ;; procedure
```

#### foreign?
```scheme
(foreign? obj) ;; procedure
```

#### foreign-tags
```scheme
(foreign-tags foreign) ;; procedure
```

#### foreign-address
```scheme
(foreign-address foreign) ;; procedure
```

#### foreign-release!
```scheme
(foreign-release! foreign) ;; procedure
```

#### foreign-released?
```scheme
(foreign-released? foreign) ;; procedure
```

#### invalid-hash-number-exception?
```scheme
(invalid-hash-number-exception? obj) ;; procedure
```

#### invalid-hash-number-exception-procedure
```scheme
(invalid-hash-number-exception-procedure exc) ;; procedure
```

#### invalid-hash-number-exception-arguments
```scheme
(invalid-hash-number-exception-arguments exc) ;; procedure
```

#### tcp-client-local-socket-info
```scheme
(tcp-client-local-socket-info tcp-client-port) ;; procedure
```

#### tcp-client-peer-socket-info
```scheme
(tcp-client-peer-socket-info tcp-client-port) ;; procedure
```

#### tcp-client-self-socket-info
```scheme
(tcp-client-self-socket-info tcp-client-port) ;; procedure
```

#### tcp-server-socket-info
```scheme
(tcp-server-socket-info tcp-server-port) ;; procedure
```

#### socket-info?
```scheme
(socket-info? obj) ;; procedure
```

#### socket-info-address
```scheme
(socket-info-address socket-info) ;; procedure
```

#### socket-info-family
```scheme
(socket-info-family socket-info) ;; procedure
```

#### socket-info-port-number
```scheme
(socket-info-port-number socket-info) ;; procedure
```

#### system-version
```scheme
(system-version) ;; procedure
```

#### system-version-string
```scheme
(system-version-string) ;; procedure
```

#### system-type
```scheme
(system-type) ;; procedure
```

#### system-type-string
```scheme
(system-type-string) ;; procedure
```

#### configure-command-string
```scheme
(configure-command-string) ;; procedure
```

#### system-stamp
```scheme
(system-stamp) ;; procedure
```

#### future
```scheme
(future expr) ;; special form
```

#### touch
```scheme
(touch obj) ;; procedure
```

#### tty?
```scheme
(tty? obj) ;; procedure
```

#### tty-history
```scheme
(tty-history tty) ;; procedure
```

#### tty-history-set!
```scheme
(tty-history-set! tty history) ;; procedure
```

#### tty-history-max-length-set!
```scheme
(tty-history-max-length-set! tty n) ;; procedure
```

#### tty-paren-balance-duration-set!
```scheme
(tty-paren-balance-duration-set! tty duration) ;; procedure
```

#### tty-text-attributes-set!
```scheme
(tty-text-attributes-set! tty attributes) ;; procedure
```

#### tty-mode-set!
```scheme
(tty-mode-set! tty mode) ;; procedure
```

#### tty-type-set!
```scheme
(tty-type-set! tty type) ;; procedure
```

#### with-input-from-port
```scheme
(with-input-from-port port thunk) ;; procedure
```

#### with-output-to-port
```scheme
(with-output-to-port port thunk) ;; procedure
```

#### input-port-char-position
```scheme
(input-port-char-position port) ;; procedure
```

#### output-port-char-position
```scheme
(output-port-char-position port) ;; procedure
```

#### open-event-queue
```scheme
(open-event-queue selector) ;; procedure
```

#### main
```scheme
(main …) ;; procedure
```

#### dead-end
```scheme
(dead-end) ;; procedure
```

#### poll-point
```scheme
(poll-point) ;; procedure
```

#### define-record-type
```scheme
(define-record-type …) ;; special form
```

#### define-type
```scheme
(define-type …) ;; special form
```

#### this-source-file
```scheme
(this-source-file) ;; special form
```

#### receive
```scheme
(receive …) ;; special form
```

#### define-values
```scheme
(define-values …) ;; special form
```

#### define-module-alias
```scheme
(define-module-alias …) ;; special form
```

#### r7rs-guard
```scheme
(r7rs-guard …) ;; special form
```

#### case-lambda
```scheme
(case-lambda …) ;; special form
```

#### syntax-case
```scheme
(syntax-case …) ;; special form
```

#### syntax
```scheme
(syntax …) ;; special form
```

#### datum->syntax
```scheme
(datum->syntax obj) ;; procedure
```

#### syntax->datum
```scheme
(syntax->datum stx) ;; procedure
```

#### syntax->list
```scheme
(syntax->list stx) ;; procedure
```

#### syntax->vector
```scheme
(syntax->vector stx) ;; procedure
```

#### length+
```scheme
(length+ clist) ;; procedure
```

#### car+cdr
```scheme
(car+cdr pair) ;; procedure
```

#### first
```scheme
(first pair) ;; procedure
```

#### second
```scheme
(second pair) ;; procedure
```

#### third
```scheme
(third pair) ;; procedure
```

#### fourth
```scheme
(fourth pair) ;; procedure
```

#### fifth
```scheme
(fifth pair) ;; procedure
```

#### sixth
```scheme
(sixth pair) ;; procedure
```

#### seventh
```scheme
(seventh pair) ;; procedure
```

#### eighth
```scheme
(eighth pair) ;; procedure
```

#### ninth
```scheme
(ninth pair) ;; procedure
```

#### tenth
```scheme
(tenth pair) ;; procedure
```

#### not-pair?
```scheme
(not-pair? x) ;; procedure
```

#### null-list?
```scheme
(null-list? list) ;; procedure
```

#### proper-list?
```scheme
(proper-list? s) ;; procedure
```

#### circular-list?
```scheme
(circular-list? s) ;; procedure
```

#### dotted-list?
```scheme
(dotted-list? s) ;; procedure
```

#### filter
```scheme
(filter pred list) ;; procedure
```

#### remove
```scheme
(remove pred list) ;; procedure
```

#### remq
```scheme
(remq elem list) ;; procedure
```

#### concatenate
```scheme
(concatenate list-of-lists [separator]) ;; procedure
```

#### concatenate!
```scheme
(concatenate! list-of-lists) ;; procedure
```

#### list=
```scheme
(list= elt= list …) ;; procedure
```

#### list-set
```scheme
(list-set list k val) ;; procedure
```

#### list-set!
```scheme
(list-set! list k val) ;; procedure
```

#### fold
```scheme
(fold proc base list …) ;; procedure
```

#### fold-right
```scheme
(fold-right proc base list …) ;; procedure
```

#### iota
```scheme
(iota count [start [step]]) ;; procedure
```

#### circular-list
```scheme
(circular-list x y…) ;; procedure
```

#### cons*
```scheme
(cons* x y…) ;; procedure
```

#### list-copy
```scheme
(list-copy list) ;; procedure
```

#### list-tabulate
```scheme
(list-tabulate n init-proc) ;; procedure
```

#### make-list
```scheme
(make-list n [fill]) ;; procedure
```

#### reverse!
```scheme
(reverse! list) ;; procedure
```

#### append-reverse
```scheme
(append-reverse list tail) ;; procedure
```

#### append-reverse!
```scheme
(append-reverse! list tail) ;; procedure
```

#### xcons
```scheme
(xcons d a) ;; procedure
```

#### take
```scheme
(take x i) ;; procedure
```

#### drop
```scheme
(drop x i) ;; procedure
```

#### last
```scheme
(last pair) ;; procedure
```

#### last-pair
```scheme
(last-pair pair) ;; procedure
```

#### list-sort
```scheme
(list-sort proc list) ;; procedure
```

#### list-sort!
```scheme
(list-sort! proc list) ;; procedure
```

#### finite?
```scheme
(finite? x) ;; procedure
```

#### infinite?
```scheme
(infinite? x) ;; procedure
```

#### nan?
```scheme
(nan? x) ;; procedure
```

#### acosh
```scheme
(acosh x) ;; procedure
```

#### asinh
```scheme
(asinh x) ;; procedure
```

#### atanh
```scheme
(atanh x) ;; procedure
```

#### cosh
```scheme
(cosh x) ;; procedure
```

#### sinh
```scheme
(sinh x) ;; procedure
```

#### tanh
```scheme
(tanh x) ;; procedure
```

#### conjugate
```scheme
(conjugate x) ;; procedure
```

#### bits
```scheme
(bits bool…) ;; procedure
```

#### bits->list
```scheme
(bits->list i [len]) ;; procedure
```

#### list->bits
```scheme
(list->bits list) ;; procedure
```

#### bits->vector
```scheme
(bits->vector i [len]) ;; procedure
```

#### vector->bits
```scheme
(vector->bits vector) ;; procedure
```

#### six.infix
```scheme
(six.infix datum) ;; special form
```

#### six.!
```scheme
(six.!) ;; undefined
```

#### six.!x
```scheme
(six.!x x) ;; special form
```

#### six.&x
```scheme
(six.&x x) ;; special form
```

#### six.**x
```scheme
(six.**x x) ;; special form
```

#### six.*x
```scheme
(six.*x x) ;; special form
```

#### six.++x
```scheme
(six.++x x) ;; special form
```

#### six.+x
```scheme
(six.+x x) ;; special form
```

#### six.--x
```scheme
(six.--x x) ;; special form
```

#### six.-x
```scheme
(six.-x x) ;; special form
```

#### six.arrow
```scheme
(six.arrow expr ident) ;; special form
```

#### six.asyncx
```scheme
(six.asyncx x) ;; undefined
```

#### six.awaitx
```scheme
(six.awaitx x) ;; undefined
```

#### six.break
```scheme
(six.break) ;; undefined
```

#### six.call
```scheme
(six.call func arg…) ;; special form
```

#### six.case
```scheme
(six.case) ;; undefined
```

#### six.clause
```scheme
(six.clause) ;; undefined
```

#### six.compound
```scheme
(six.compound statement…) ;; special form
```

#### six.cons
```scheme
(six.cons x y) ;; special form
```

#### six.continue
```scheme
(six.continue) ;; undefined
```

#### six.define-procedure
```scheme
(six.define-procedure ident proc) ;; special form
```

#### six.define-variable
```scheme
(six.define-variable ident type dims init) ;; special form
```

#### six.do-while
```scheme
(six.do-while stat expr) ;; special form
```

#### six.dot
```scheme
(six.dot expr ident) ;; special form
```

#### six.for
```scheme
(six.for stat1 expr2 expr3 stat2) ;; special form
```

#### six.goto
```scheme
(six.goto expr) ;; undefined
```

#### six.from-import
```scheme
(six.from-import expr1 expr2) ;; undefined
```

#### six.from-import-*
```scheme
(six.from-import-* expr) ;; undefined
```

#### six.identifier
```scheme
(six.identifier ident) ;; special form
```

#### six.if
```scheme
(six.if expr stat1 [stat2]) ;; special form
```

#### six.import
```scheme
(six.import expr) ;; undefined
```

#### six.index
```scheme
(six.index expr1 expr2) ;; special form
```

#### six.label
```scheme
(six.label ident stat) ;; undefined
```

#### six.list
```scheme
(six.list x y) ;; special form
```

#### six.literal
```scheme
(six.literal value) ;; special form
```

#### six.make-array
```scheme
(six.make-array init dim…) ;; procedure
```

#### six.new
```scheme
(six.new ident arg…) ;; special form
```

#### six.null
```scheme
(six.null) ;; special form
```

#### six.procedure
```scheme
(six.procedure type params stat) ;; special form
```

#### six.procedure-body
```scheme
(six.procedure-body stat…) ;; special form
```

#### six.return
```scheme
(six.return) ;; undefined
```

#### six.switch
```scheme
(six.switch) ;; undefined
```

#### six.typeofx
```scheme
(six.typeofx x) ;; undefined
```

#### six.while
```scheme
(six.while expr stat…) ;; special form
```

#### six.x!==y
```scheme
(six.x!==y x y) ;; special form
```

#### six.x!=y
```scheme
(six.x!=y x y) ;; special form
```

#### six.x%=y
```scheme
(six.x%=y x y) ;; special form
```

#### six.x%y
```scheme
(six.x%y x y) ;; special form
```

#### six.x&&y
```scheme
(six.x&&y x y) ;; special form
```

#### six.x&=y
```scheme
(six.x&=y x y) ;; special form
```

#### six.x&y
```scheme
(six.x&y x y) ;; special form
```

#### six.x**=y
```scheme
(six.x**=y x y) ;; special form
```

#### six.x**y
```scheme
(six.x**y x y) ;; special form
```

#### six.x*=y
```scheme
(six.x*=y x y) ;; special form
```

#### six.x*y
```scheme
(six.x*y x y) ;; special form
```

#### six.x@=y
```scheme
(six.x@=y x y) ;; special form
```

#### six.x@y
```scheme
(six.x@y x y) ;; special form
```

#### six.x++
```scheme
(six.x++ x) ;; special form
```

#### six.x+=y
```scheme
(six.x+=y x y) ;; special form
```

#### six.x+y
```scheme
(six.x+y x y) ;; special form
```

#### |six.x,y|
```scheme
(|six.x,y| x y) ;; special form
```

#### six.x--
```scheme
(six.x-- x) ;; special form
```

#### six.x-=y
```scheme
(six.x-=y x y) ;; special form
```

#### six.x-y
```scheme
(six.x-y x y) ;; special form
```

#### six.x//=y
```scheme
(six.x//=y x y) ;; special form
```

#### six.x//y
```scheme
(six.x//y x y) ;; special form
```

#### six.x/=y
```scheme
(six.x/=y x y) ;; special form
```

#### six.x/y
```scheme
(six.x/y x y) ;; special form
```

#### six.x:-y
```scheme
(six.x:-y x y) ;; undefined
```

#### six.x:=y
```scheme
(six.x:=y x y) ;; special form
```

#### six.x:y
```scheme
(six.x:y x y) ;; special form
```

#### six.x<<=y
```scheme
(six.x<<=y x y) ;; special form
```

#### six.x<<y
```scheme
(six.x<<y x y) ;; special form
```

#### six.x<=y
```scheme
(six.x<=y x y) ;; special form
```

#### six.x<y
```scheme
(six.x<y x y) ;; special form
```

#### six.x===y
```scheme
(six.x===y x y) ;; special form
```

#### six.x==y
```scheme
(six.x==y x y) ;; special form
```

#### six.x=y
```scheme
(six.x=y x y) ;; special form
```

#### six.x>=y
```scheme
(six.x>=y x y) ;; special form
```

#### six.x>>>=y
```scheme
(six.x>>>=y x y) ;; special form
```

#### six.x>>>y
```scheme
(six.x>>>y x y) ;; special form
```

#### six.x>>=y
```scheme
(six.x>>=y x y) ;; special form
```

#### six.x>>y
```scheme
(six.x>>y x y) ;; special form
```

#### six.x>y
```scheme
(six.x>y x y) ;; special form
```

#### six.x?y:z
```scheme
(six.x?y:z x y z) ;; special form
```

#### six.x^=y
```scheme
(six.x^=y x y) ;; special form
```

#### six.x^y
```scheme
(six.x^y x y) ;; special form
```

#### |six.x\|=y|
```scheme
(|six.x\|=y| x y) ;; special form
```

#### |six.x\|y|
```scheme
(|six.x\|y| x y) ;; special form
```

#### |six.x\|\|y|
```scheme
(|six.x\|\|y| x y) ;; special form
```

#### six.xandy
```scheme
(six.xandy x y) ;; special form
```

#### six.xinstanceofy
```scheme
(six.xinstanceofy x y) ;; undefined
```

#### six.xiny
```scheme
(six.xiny x y) ;; special form
```

#### six.xisy
```scheme
(six.xisy x y) ;; special form
```

#### six.notx
```scheme
(six.notx x y) ;; special form
```

#### six.xory
```scheme
(six.xory x y) ;; special form
```

#### six.~x
```scheme
(six.~x x) ;; special form
```

#### six.yieldx
```scheme
(six.yieldx x) ;; undefined
```

#### readtable-comment-handler
```scheme
(readtable-comment-handler readtable) ;; procedure
```

#### readtable-comment-handler-set
```scheme
(readtable-comment-handler-set readtable new-value) ;; procedure
```

#### open-output-bytevector
```scheme
(open-output-bytevector [u8vector-or-settings]) ;; procedure
```
