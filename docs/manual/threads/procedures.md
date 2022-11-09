---
title: Procedures
---

# Procedures

#### current-thread

```scheme
(current-thread) ;; procedure
```

This procedure returns the current thread. For example:

```scheme
> (current-thread)
#<thread #1 primordial>
> (eq? (current-thread) (current-thread))
#t
```

#### thread?

```scheme
(thread? obj) ;; procedure
```

This procedure returns `#t` when `obj` is a thread object and `#f` otherwise.

For example:

```scheme
> (thread? (current-thread))
#t
> (thread? 'foo)
#f
```

#### make-thread
#### make-root-thread

```scheme
(make-thread thunk [name [thread-group]]) ;; procedure
(make-root-thread thunk [name
                        [thread-group
                        [input-port
                        [output-port]]]]) ;; procedure
```

The `make-thread` procedure creates and returns an initialized thread. This
thread is not automatically made runnable (the procedure `thread-start!` must be
used for this). A thread has the following fields: base priority, priority
boost, boosted flag, quantum, name, specific, end-result, end-exception, and a
list of locked/owned mutexes it owns. The thread's execution consists of a call
to `thunk` with the initial continuation. This continuation causes the (then)
current thread to store the result in its `end-result` field, abandon all
mutexes it owns, and finally terminate. The `dynamic-wind` stack of the initial
continuation is empty. The optional `name` is an arbitrary Scheme object which
identifies the thread (useful for debugging); it defaults to an unspecified
value. The specific field is set to an unspecified value. The optional
`thread-group` indicates which thread group this thread belongs to; it defaults
to the thread group of the current thread. The base priority, priority boost,
and quantum of the thread are set to the same value as the current thread and
the boosted flag is set to false. The thread's mailbox is initially empty. The
thread inherits the dynamic environment from the current thread. Moreover, in
this dynamic environment the exception-handler is bound to the _initial
exception-handler_ which is a unary procedure which causes the (then) current
thread to store in its end-exception field an uncaught-exception object whose
"reason" is the argument of the handler, abandon all mutexes it owns, and
finally terminate.

The `make-root-thread` procedure behaves like the `make-thread` procedure except the
created thread does not inherit the dynamic environment from the current thread
and the base priority is set to `0`, the priority boost is set to `1.0e-6`, and the
quantum is set to `0.02`. The dynamic environment of the thread has the global
bindings of the parameter objects, except `current-input-port` which is bound to
`input-port`, `current-output-port` which is bound to `output-port`, and
`current-directory` which is bound to the initial current working directory of the
current process. If `input-port` is not specified it defaults to a port
corresponding to the standard input (`stdin`). If `output-port` is not specified
it defaults to a port corresponding to the standard output (`stdout`).

For example:

```scheme
> (make-thread (lambda () (write 'hello)))
#<thread #2>
> (make-root-thread (lambda () (write 'world)) 'a-name)
#<thread #3 a-name>
```

#### thread

```scheme
(thread thunk) ;; procedure
```

The `thread` procedure creates, starts and returns a new thread. The call
`(thread thunk)` is equivalent to `(thread-start! (make-thread thunk))`.

For example:

```scheme
> (define a (thread (lambda () (expt 2 1005))))
> (define b (thread (lambda () (expt 2 1000))))
> (/ (thread-join! a) (thread-join! b))
32
```

#### thread-name

```scheme
(thread-name thread) ;; procedure
```

This procedure returns the name of the thread. For example:

```scheme
> (thread-name (make-thread (lambda () #f) 'foo))
foo
```

#### thread-specific
#### thread-specific-set!

```scheme
(thread-specific thread) ;; procedure
(thread-specific-set! thread obj) ;; procedure
```

The `thread-specific` procedure returns the content of the thread's specific
field.

The `thread-specific-set!` procedure stores obj into the thread's specific field
and returns an unspecified value.

For example:

```scheme
> (thread-specific-set! (current-thread) "hello")
> (thread-specific (current-thread))
"hello"
```


#### thread-base-priority
#### thread-base-priority-set!

```scheme
(thread-base-priority thread) ;; procedure
(thread-base-priority-set! thread priority) ;; procedure
```

The procedure `thread-base-priority` returns a real number which corresponds to
the base priority of the thread.

The procedure `thread-base-priority-set!` changes the base priority of the
thread to priority and returns an unspecified value. The priority must be a real
number.

For example:

```scheme
> (thread-base-priority-set! (current-thread) 12.3)
> (thread-base-priority (current-thread))
12.3
```

#### thread-priority-boost
#### thread-priority-boost-set!

```scheme
(thread-priority-boost thread) ;; procedure
(thread-priority-boost-set! thread priority-boost) ;; procedure
```

The procedure `thread-priority-boost` returns a real number which corresponds to
the priority boost of the thread.

The procedure `thread-priority-boost-set!` changes the priority boost of the
thread to `priority-boost` and returns an unspecified value. The
`priority-boost` must be a nonnegative real.

For example:

```scheme
> (thread-priority-boost-set! (current-thread) 2.5)
> (thread-priority-boost (current-thread))
2.5
```

#### thread-quantum
#### thread-quantum-set!

```scheme
(thread-quantum thread) ;; procedure
(thread-quantum-set! thread quantum) ;; procedure
```

The procedure `thread-quantum` returns a real number which corresponds to the
quantum of the thread.

The procedure `thread-quantum-set!` changes the quantum of the thread to `quantum`
and returns an unspecified value. The `quantum` must be a nonnegative real. A
value of zero selects the smallest quantum supported by the implementation.

For example:

```scheme
> (thread-quantum-set! (current-thread) 1.5)
> (thread-quantum (current-thread))
1.5
> (thread-quantum-set! (current-thread) 0)
> (thread-quantum (current-thread))
0.
```

#### thread-start!

```scheme
(thread-start! thread) ;; procedure
```

This procedure makes `thread` runnable and returns the thread. The thread must
be an initialized thread.

For example:

```scheme
> (let ((t (thread-start! (make-thread (lambda () (write 'a))))))
    (write 'b)
    (thread-join! t))
ab> or ba>
```

!!! note "Note"
    It is useful to separate thread creation and thread activation to avoid the
    race condition that would occur if the created thread tries to examine a
    table in which the current thread stores the created thread. See the last
    example of the `thread-terminate!` procedure which contains mutually
    recursive threads.

#### thread-yield!

```scheme
(thread-yield!) ;; procedure
```

This procedure causes the current thread to exit the running state as if its
quantum had expired and returns an unspecified value.

For example:

```scheme
; a busy loop that avoids being too wasteful of the CPU

(let loop ()
  (if (mutex-lock! m 0) ; try to lock m but don't block
      (begin
        (display "locked mutex m")
        (mutex-unlock! m))
      (begin
        (do-something-else)
        (thread-yield!) ; relinquish rest of quantum
        (loop))))
```

#### thread-sleep!

```scheme
(thread-sleep! timeout) ;; procedure
```

This procedure causes the current thread to wait until the `timeout` is reached
and returns an unspecified value. This blocks the thread only if `timeout`
represents a point in the future. It is an error for `timeout` to be `#f`.

For example:

```scheme
; a clock with a gradual drift:

(let loop ((x 1))
  (thread-sleep! 1)
  (write x)
  (loop (+ x 1)))

; a clock with no drift:

(let ((start (time->seconds (current-time)))
  (let loop ((x 1))
    (thread-sleep! (seconds->time (+ x start)))
    (write x)
    (loop (+ x 1))))
```

#### thread-terminate!

```scheme
(thread-terminate! thread) ;; procedure
```

This procedure causes an abnormal termination of the `thread`. If the thread is
not already terminated, all mutexes owned by the thread become
unlocked/abandoned and a terminated-thread-exception object is stored in the
thread's end-exception field. If `thread` is the current thread,
`thread-terminate!` does not return. Otherwise `thread-terminate!` returns an
unspecified value; the termination of the thread will occur at some point
between the calling of `thread-terminate!` and a finite time in the future (an
explicit thread synchronization is needed to detect termination, see
`thread-join!`).

For example:

```scheme
(define (amb thunk1 thunk2)
  (let ((result #f)
        (result-mutex (make-mutex))
        (done-mutex (make-mutex)))
    (letrec ((child1
              (make-thread
                (lambda ()
                  (let ((x (thunk1)))
                    (mutex-lock! result-mutex #f #f)
                    (set! result x)
                    (thread-terminate! child2)
                    (mutex-unlock! done-mutex)))))
             (child2
              (make-thread
                (lambda ()
                  (let ((x (thunk2)))
                    (mutex-lock! result-mutex #f #f)
                    (set! result x)
                    (thread-terminate! child1)
                    (mutex-unlock! done-mutex))))))
      (mutex-lock! done-mutex #f #f)
      (thread-start! child1)
      (thread-start! child2)
      (mutex-lock! done-mutex #f #f)
      result)))
```

!!! node "Note"
    This operation must be used carefully because it terminates a thread
    abruptly and it is impossible for that thread to perform any kind of
    cleanup. This may be a problem if the thread is in the middle of a critical
    section where some structure has been put in an inconsistent state. However,
    another thread attempting to enter this critical section will raise an
    abandoned-mutex-exception object because the mutex is unlocked/abandoned.
    This helps avoid observing an inconsistent state. Clean termination can be
    obtained by polling, as shown in the example below.

For example:

```scheme
(define (spawn thunk)
  (let ((t (make-thread thunk)))
    (thread-specific-set! t #t)
    (thread-start! t)
    t))

(define (stop! thread)
  (thread-specific-set! thread #f)
  (thread-join! thread))

(define (keep-going?)
  (thread-specific (current-thread)))

(define count!
  (let ((m (make-mutex))
        (i 0))
    (lambda ()
      (mutex-lock! m)
      (let ((x (+ i 1)))
        (set! i x)
        (mutex-unlock! m)
        x))))

(define (increment-forever!)
  (let loop () (count!) (if (keep-going?) (loop))))

(let ((t1 (spawn increment-forever!))
      (t2 (spawn increment-forever!)))
  (thread-sleep! 1)
  (stop! t1)
  (stop! t2)
  (count!))  ==>  377290
```

#### thread-join!

```scheme
(thread-join! thread [timeout [timeout-val]])	procedure
```

This procedure causes the current thread to wait until the `thread` terminates
(normally or not) or until the timeout is reached if `timeout` is supplied. If
the timeout is reached, `thread-join!` returns `timeout-val` if it is supplied,
otherwise a join-timeout-exception object is raised. If the thread terminated
normally, the content of the end-result field is returned, otherwise the content
of the end-exception field is raised.

For example:

```scheme
(let ((t (thread-start! (make-thread (lambda () (expt 2 100))))))
  (do-something-else)
  (thread-join! t))  ==>  1267650600228229401496703205376

(let ((t (thread-start! (make-thread (lambda () (raise 123))))))
  (do-something-else)
  (with-exception-handler
    (lambda (exc)
      (if (uncaught-exception? exc)
          (* 10 (uncaught-exception-reason exc))
          99999))
    (lambda ()
      (+ 1 (thread-join! t)))))  ==>  1231

(define thread-alive?
  (let ((unique (list 'unique)))
    (lambda (thread)
      ; Note: this procedure raises an exception if
      ; the thread terminated abnormally.
      (eq? (thread-join! thread 0 unique) unique))))

(define (wait-for-termination! thread)
  (let ((eh (current-exception-handler)))
    (with-exception-handler
      (lambda (exc)
        (if (not (or (terminated-thread-exception? exc)
                     (uncaught-exception? exc)))
            (eh exc))) ; unexpected exceptions are handled by eh
      (lambda ()
        ; The following call to thread-join! will wait until the
        ; thread terminates.  If the thread terminated normally
        ; thread-join! will return normally.  If the thread
        ; terminated abnormally then one of these two exception
        ; objects is raised by thread-join!:
        ;   - terminated-thread-exception object
        ;   - uncaught-exception object
        (thread-join! thread)
        #f)))) ; ignore result of thread-join!
```

#### thread-send

```scheme
(thread-send thread msg) ;; procedure
```

Each thread has a mailbox which stores messages delivered to the thread in the
order delivered.

The procedure `thread-send` adds the message `msg` at the end of the mailbox of
thread `thread` and returns an unspecified value.

For example:

```scheme
> (thread-send (current-thread) 111)
> (thread-send (current-thread) 222)
> (thread-receive)
111
> (thread-receive)
222
```

#### thread-receive
#### thread-mailbox-next
#### thread-mailbox-rewind
#### thread-mailbox-extract-and-rewind

```scheme
(thread-receive [timeout [default]]) ;; procedure
(thread-mailbox-next [timeout [default]]) ;; procedure
(thread-mailbox-rewind) ;; procedure
(thread-mailbox-extract-and-rewind) ;; procedure
```

To allow a thread to examine the messages in its mailbox without removing them
from the mailbox, each thread has a mailbox cursor which normally points to the
last message accessed in the mailbox. When a mailbox cursor is rewound using the
procedure `thread-mailbox-rewind` or `thread-mailbox-extract-and-rewind` or
`thread-receive`, the cursor does not point to a message, but the next call to
`thread-receive` and `thread-mailbox-next` will set the cursor to the oldest
message in the mailbox.

The procedure `thread-receive` advances the mailbox cursor of the current thread
to the next message, removes that message from the mailbox, rewinds the mailbox
cursor, and returns the message. When `timeout` is not specified, the current
thread will wait until a message is available in the mailbox. When `timeout` is
specified and `default` is not specified, a mailbox-receive-timeout-exception
object is raised if the timeout is reached before a message is available. When
`timeout` is specified and `default` is specified, `default` is returned if the
timeout is reached before a message is available.

The procedure `thread-mailbox-next` behaves like `thread-receive` except that
the message remains in the mailbox and the mailbox cursor is not rewound.

The procedures `thread-mailbox-rewind` or `thread-mailbox-extract-and-rewind`
rewind the mailbox cursor of the current thread so that the next call to
`thread-mailbox-next` and `thread-receive` will access the oldest message in the
mailbox. Additionally the procedure `thread-mailbox-extract-and-rewind` will
remove from the mailbox the message most recently accessed by a call to
`thread-mailbox-next`. When `thread-mailbox-next` has not been called since the
last call to `thread-receive or thread-mailbox-rewind` or
`thread-mailbox-extract-and-rewind`, a call to
`thread-mailbox-extract-and-rewind` only resets the mailbox cursor (no message
is removed).

For example:

```scheme
> (thread-send (current-thread) 111)
> (thread-receive 1 999)
111
> (thread-send (current-thread) 222)
> (thread-send (current-thread) 333)
> (thread-mailbox-next 1 999)
222
> (thread-mailbox-next 1 999)
333
> (thread-mailbox-next 1 999)
999
> (thread-mailbox-extract-and-rewind)
> (thread-receive 1 999)
222
> (thread-receive 1 999)
999
```

#### mailbox-receive-timeout-exception?
#### mailbox-receive-timeout-exception-procedure
#### mailbox-receive-timeout-exception-arguments

```scheme
(mailbox-receive-timeout-exception? obj)	procedure
(mailbox-receive-timeout-exception-procedure exc)	procedure
(mailbox-receive-timeout-exception-arguments exc)	procedure
```

Mailbox-receive-timeout-exception objects are raised by the procedures
`thread-receive` and `thread-mailbox-next` when a timeout expires before a message
is available and no default value is specified. The parameter `exc` must be a
mailbox-receive-timeout-exception object.

The procedure `mailbox-receive-timeout-exception?` returns `#t` when `obj` is a
mailbox-receive-timeout-exception object and `#f` otherwise.

The procedure `mailbox-receive-timeout-exception-procedure` returns the
procedure that raised `exc`.

The procedure `mailbox-receive-timeout-exception-arguments` returns the list of
arguments of the procedure that raised `exc`.

For example:

```scheme
> (define (handler exc)
    (if (mailbox-receive-timeout-exception? exc)
        (list (mailbox-receive-timeout-exception-procedure exc)
              (mailbox-receive-timeout-exception-arguments exc))
        'not-mailbox-receive-timeout-exception))
> (with-exception-catcher
    handler
    (lambda () (thread-receive 1)))
(#<procedure #2 thread-receive> (1))
```

#### mutex?

```scheme
(mutex? obj) ;; procedure
```

This procedure returns `#t` when `obj` is a mutex object and `#f` otherwise.

For example:

```scheme
> (mutex? (make-mutex))
#t
> (mutex? 'foo)
#f
```

#### make-mutex

```scheme
(make-mutex [name]) ;; procedure
```

This procedure returns a new mutex in the unlocked/not-abandoned state. The
optional `name` is an arbitrary Scheme object which identifies the mutex (useful
for debugging); it defaults to an unspecified value. The mutex's specific field
is set to an unspecified value.

For example:

```scheme
> (make-mutex)
#<mutex #2>
> (make-mutex 'foo)
#<mutex #3 foo>
```

#### mutex-name

```scheme
(mutex-name mutex) ;; procedure
```

Returns the name of the mutex. For example:

```scheme
> (mutex-name (make-mutex 'foo))
foo
```

#### mutex-specific
#### mutex-specific-set!

```scheme
(mutex-specific mutex) ;; procedure
(mutex-specific-set! mutex obj) ;; procedure
```

The `mutex-specific` procedure returns the content of the mutex's specific
field.

The `mutex-specific-set!` procedure stores `obj` into the mutex's specific field
and returns an unspecified value.

For example:

```scheme
> (define m (make-mutex))
> (mutex-specific-set! m "hello")
> (mutex-specific m)
"hello"
> (define (mutex-lock-recursively! mutex)
    (if (eq? (mutex-state mutex) (current-thread))
        (let ((n (mutex-specific mutex)))
          (mutex-specific-set! mutex (+ n 1)))
        (begin
          (mutex-lock! mutex)
          (mutex-specific-set! mutex 0))))
> (define (mutex-unlock-recursively! mutex)
    (let ((n (mutex-specific mutex)))
      (if (= n 0)
          (mutex-unlock! mutex)
          (mutex-specific-set! mutex (- n 1)))))
> (mutex-lock-recursively! m)
> (mutex-lock-recursively! m)
> (mutex-lock-recursively! m)
> (mutex-specific m)
2
```

#### mutex-state

```scheme
(mutex-state mutex) ;; procedure
```

This procedure returns information about the state of the mutex. The possible
results are:

- thread `T`: the mutex is in the locked/owned state and thread `T` is the owner of the mutex
- symbol `not-owned`: the mutex is in the locked/not-owned state
- symbol `abandoned`: the mutex is in the unlocked/abandoned state
- symbol `not-abandoned`: the mutex is in the unlocked/not-abandoned state

For example:

```scheme
(mutex-state (make-mutex))  ==>  not-abandoned

(define (thread-alive? thread)
  (let ((mutex (make-mutex)))
    (mutex-lock! mutex #f thread)
    (let ((state (mutex-state mutex)))
      (mutex-unlock! mutex) ; avoid space leak
      (eq? state thread))))
```

#### mutex-lock!

```scheme
(mutex-lock! mutex [timeout [thread]]) ;; procedure
```

This procedure locks mutex. If the mutex is currently locked, the current thread
waits until the mutex is unlocked, or until the timeout is reached if timeout is
supplied. If the timeout is reached, mutex-lock! returns ‘#f’. Otherwise, the
state of the mutex is changed as follows:

- if `thread` is `#f` the mutex becomes locked/not-owned,
- otherwise, let _T_ be `thread` (or the current thread if `thread` is not
  supplied),
    - if _T_ is terminated the mutex becomes unlocked/abandoned,
    - otherwise mutex becomes locked/owned with _T_ as the owner.

After changing the state of the mutex, an abandoned-mutex-exception object is
raised if the mutex was unlocked/abandoned before the state change, otherwise
`mutex-lock!` returns `#t`. It is not an error if the mutex is owned by the
current thread (but the current thread will have to wait).

For example:

```scheme
; an implementation of a mailbox object of depth one; this
; implementation does not behave well in the presence of forced
; thread terminations using thread-terminate! (deadlock can occur
; if a thread is terminated in the middle of a put! or get! operation)

(define (make-empty-mailbox)
  (let ((put-mutex (make-mutex)) ; allow put! operation
        (get-mutex (make-mutex))
        (cell #f))

    (define (put! obj)
      (mutex-lock! put-mutex #f #f) ; prevent put! operation
      (set! cell obj)
      (mutex-unlock! get-mutex)) ; allow get! operation

    (define (get!)
      (mutex-lock! get-mutex #f #f) ; wait until object in mailbox
      (let ((result cell))
        (set! cell #f) ; prevent space leaks
        (mutex-unlock! put-mutex) ; allow put! operation
        result))

    (mutex-lock! get-mutex #f #f) ; prevent get! operation

    (lambda (msg)
      (case msg
        ((put!) put!)
        ((get!) get!)
        (else (error "unknown message"))))))

(define (mailbox-put! m obj) ((m 'put!) obj))
(define (mailbox-get! m) ((m 'get!)))

; an alternate implementation of thread-sleep!

(define (sleep! timeout)
  (let ((m (make-mutex)))
    (mutex-lock! m #f #f)
    (mutex-lock! m timeout #f)))

; a procedure that waits for one of two mutexes to unlock

(define (lock-one-of! mutex1 mutex2)
  ; this procedure assumes that neither mutex1 or mutex2
  ; are owned by the current thread
  (let ((ct (current-thread))
        (done-mutex (make-mutex)))
    (mutex-lock! done-mutex #f #f)
    (let ((t1 (thread-start!
               (make-thread
                (lambda ()
                  (mutex-lock! mutex1 #f ct)
                  (mutex-unlock! done-mutex)))))
          (t2 (thread-start!
               (make-thread
                (lambda ()
                  (mutex-lock! mutex2 #f ct)
                  (mutex-unlock! done-mutex))))))
      (mutex-lock! done-mutex #f #f)
      (thread-terminate! t1)
      (thread-terminate! t2)
      (if (eq? (mutex-state mutex1) ct)
          (begin
            (if (eq? (mutex-state mutex2) ct)
                (mutex-unlock! mutex2)) ; don't lock both
            mutex1)
          mutex2))))
```

#### mutex-unlock!

```scheme
(mutex-unlock! mutex [condition-variable [timeout]]) ;; procedure
```

This procedure unlocks the mutex by making it unlocked/not-abandoned. It is not
an error to unlock an unlocked mutex and a mutex that is owned by any thread. If
`condition-variable` is supplied, the current thread is blocked and added to the
`condition-variable` before unlocking `mutex`; the thread can unblock at any
time but no later than when an appropriate call to `condition-variable-signal!`
or `condition-variable-broadcast!` is performed (see below), and no later than
the timeout (if `timeout` is supplied). If there are threads waiting to lock
this mutex, the scheduler selects a thread, the mutex becomes locked/owned or
locked/not-owned, and the thread is unblocked. `mutex-unlock!` returns `#f` when
the timeout is reached, otherwise it returns `#t`.

!!! note "Note"
    The reason the thread can unblock at any time (when `condition-variable` is
    supplied) is that the scheduler, when it detects a serious problem such as a
    deadlock, must interrupt one of the blocked threads (such as the primordial
    thread) so that it can perform some appropriate action. After a thread
    blocked on a condition-variable has handled such an interrupt it would be
    wrong for the scheduler to return the thread to the blocked state, because
    any calls to `condition-variable-broadcast!` during the interrupt will have
    gone unnoticed. It is necessary for the thread to remain runnable and return
    from the call to `mutex-unlock!` with a result of `#t`.

!!! note "Note"
    `mutex-unlock!` is related to the "wait" operation on condition variables
    available in other thread systems. The main difference is that "wait"
    automatically locks mutex just after the thread is unblocked. This operation
    is not performed by `mutex-unlock!` and so must be done by an explicit call
    to `mutex-lock!`. This has the advantages that a different timeout and
    exception-handler can be specified on the `mutex-lock!` and `mutex-unlock!`
    and the location of all the mutex operations is clearly apparent.

For example:

```scheme
(let loop ()
  (mutex-lock! m)
  (if (condition-is-true?)
      (begin
        (do-something-when-condition-is-true)
        (mutex-unlock! m))
      (begin
        (mutex-unlock! m cv)
        (loop))))
```

#### condition-variable?

```scheme
(condition-variable? obj) ;; procedure
```

This procedure returns `#t` when `obj` is a condition-variable object and `#f`
otherwise.

For example:

```scheme
> (condition-variable? (make-condition-variable))
#t
> (condition-variable? 'foo)
#f
```

#### make-condition-variable

```scheme
(make-condition-variable [name]) ;; procedure
```

This procedure returns a new empty condition variable. The optional `name` is an
arbitrary Scheme object which identifies the condition variable (useful for
debugging); it defaults to an unspecified value. The condition variable's
specific field is set to an unspecified value.

For example:

```scheme
> (make-condition-variable)
#<condition-variable #2>
```

#### condition-variable-name

```scheme
(condition-variable-name condition-variable) ;; procedure
```

This procedure returns the name of the `condition-variable`. For example:

```scheme
> (condition-variable-name (make-condition-variable 'foo))
foo
```

#### condition-variable-specific
#### condition-variable-specific-set!

```scheme
(condition-variable-specific condition-variable) ;; procedure
(condition-variable-specific-set! condition-variable obj) ;; procedure
```

The `condition-variable-specific` procedure returns the content of the
`condition-variable`’s specific field.

The `condition-variable-specific-set!` procedure stores `obj` into the
`condition-variable`’s specific field and returns an unspecified value.

For example:

```scheme
> (define cv (make-condition-variable))
> (condition-variable-specific-set! cv "hello")
> (condition-variable-specific cv)
"hello"
```

#### condition-variable-signal!

```scheme
(condition-variable-signal! condition-variable) ;; procedure
```

This procedure unblocks a thread blocked on the `condition-variable` (if there
is at least one) and returns an unspecified value.

For example:

```scheme
; an implementation of a mailbox object of depth one; this
; implementation behaves gracefully when threads are forcibly
; terminated using thread-terminate! (an abandoned-mutex-exception
; object will be raised when a put! or get! operation is attempted
; after a thread is terminated in the middle of a put! or get!
; operation)

(define (make-empty-mailbox)
  (let ((mutex (make-mutex))
        (put-condvar (make-condition-variable))
        (get-condvar (make-condition-variable))
        (full? #f)
        (cell #f))

    (define (put! obj)
      (mutex-lock! mutex)
      (if full?
          (begin
            (mutex-unlock! mutex put-condvar)
            (put! obj))
          (begin
            (set! cell obj)
            (set! full? #t)
            (condition-variable-signal! get-condvar)
            (mutex-unlock! mutex))))

    (define (get!)
      (mutex-lock! mutex)
      (if (not full?)
          (begin
            (mutex-unlock! mutex get-condvar)
            (get!))
          (let ((result cell))
            (set! cell #f) ; avoid space leaks
            (set! full? #f)
            (condition-variable-signal! put-condvar)
            (mutex-unlock! mutex)
            result)))

    (lambda (msg)
      (case msg
        ((put!) put!)
        ((get!) get!)
        (else (error "unknown message"))))))

(define (mailbox-put! m obj) ((m 'put!) obj))
(define (mailbox-get! m) ((m 'get!)))
```

#### condition-variable-broadcast!

```scheme
(condition-variable-broadcast! condition-variable) ;; procedure
```

This procedure unblocks all the thread blocked on the `condition-variable` and
returns an unspecified value.

For example:

```scheme
(define (make-semaphore n)
  (vector n (make-mutex) (make-condition-variable)))

(define (semaphore-wait! sema)
  (mutex-lock! (vector-ref sema 1))
  (let ((n (vector-ref sema 0)))
    (if (> n 0)
        (begin
          (vector-set! sema 0 (- n 1))
          (mutex-unlock! (vector-ref sema 1)))
        (begin
          (mutex-unlock! (vector-ref sema 1) (vector-ref sema 2))
          (semaphore-wait! sema))))

(define (semaphore-signal-by! sema increment)
  (mutex-lock! (vector-ref sema 1))
  (let ((n (+ (vector-ref sema 0) increment)))
    (vector-set! sema 0 n)
    (if (> n 0)
        (condition-variable-broadcast! (vector-ref sema 2)))
    (mutex-unlock! (vector-ref sema 1))))
```
