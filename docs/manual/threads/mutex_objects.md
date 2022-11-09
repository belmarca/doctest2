---
title: Mutex objects
---

A mutex can be in one of four states: `locked` (either `owned` or `not owned`)
and `unlocked` (either `abandoned` or `not abandoned`).

An attempt to lock a mutex only succeeds if the mutex is in an unlocked state,
otherwise the current thread will wait. A mutex in the locked/owned state has an
associated `owner thread`, which by convention is the thread that is responsible
for unlocking the mutex (this case is typical of critical sections implemented
as "lock mutex, perform operation, unlock mutex"). A mutex in the
locked/not-owned state is not linked to a particular thread.

A mutex becomes locked when a thread locks it using the `mutex-lock!` primitive.
A mutex becomes unlocked/abandoned when the owner of a locked/owned mutex
terminates. A mutex becomes unlocked/not-abandoned when a thread unlocks it
using the `mutex-unlock!` primitive.

The mutex primitives do not implement recursive mutex semantics. An attempt to
lock a mutex that is locked implies that the current thread waits even if the
mutex is owned by the current thread (this can lead to a deadlock if no other
thread unlocks the mutex).

Each mutex has a specific field which can be used in an application specific way
to associate data with the mutex.
