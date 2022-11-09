---
title: Fairness
---

In various situations the scheduler must select one thread from a set of threads
(e.g. which thread to run when a running thread blocks or expires its quantum,
which thread to unblock when a mutex becomes unlocked or a condition variable is
signaled). The constraints on the selection process determine the scheduler’s
_fairness_. The selection depends on the order in which threads become runnable
or blocked and on the _priority_ attached to the threads.

The definition of fairness requires the notion of time ordering, i.e. "event A
occured before event B". For the purpose of establishing time ordering, the
scheduler uses a clock with a discrete, usually variable, resolution (a "tick").
Events occuring in a given tick can be considered to be simultaneous (i.e. if
event A occured before event B in real time, then the scheduler will claim that
event A occured before event B unless both events fall within the same tick, in
which case the scheduler arbitrarily chooses a time ordering).

Each thread _T_ has three priorities which affect fairness; the _base priority_, the
_boosted priority_, and the _effective priority_.

- The _base priority_ is the value contained in _T_'s _base priority_ field (which is
  set with the `thread-base-priority-set!` primitive).
- T's _boosted flag_ field contains a boolean that affects _T_'s _boosted
  priority_. When the boosted flag field is false, the boosted priority is equal
  to the base priority, otherwise the boosted priority is equal to the base
  priority plus the value contained in _T_'s _priority boost_ field (which is
  set with the `thread-priority-boost-set!` primitive). The boosted flag field
  is set to false when a thread is created, when its quantum expires, and when
  `thread-yield!` is called. The boosted flag field is set to true when a thread
  blocks. By carefully choosing the base priority and priority boost, relatively
  to the other threads, it is possible to set up an interactive thread so that
  it has good I/O response time without being a CPU hog when it performs long
  computations.
- The _effective priority_ is equal to the maximum of _T_'s boosted priority and
  the effective priority of all the threads that are blocked on a mutex owned by
  _T_. This _priority inheritance_ avoids priority inversion problems that would
  prevent a high priority thread blocked at the entry of a critical section to
  progress because a low priority thread inside the critical section is
  preempted for an arbitrary long time by a medium priority thread.

Let _P(T)_ be the effective priority of thread _T_ and let _R(T)_ be the most recent
time when one of the following events occurred for thread _T_, thus making it
runnable: _T_ was started by calling `thread-start!`, _T_ called `thread-yield!`, _T_
expired its quantum, or _T_ became unblocked. Let the relation _NL(T1,T2)_, "T1 no
later than T2", be true if _P(T1)<P(T2)_ or _P(T1)=P(T2)_ and _R(T1)>R(T2)_, and false
otherwise. The scheduler will schedule the execution of threads in such a way
that whenever there is at least one runnable thread

1. within a finite time at least one thread will be running and
2. there is never a pair of runnable threads _T1_ and _T2_ for which _NL(T1,T2)_
is true and _T1_ is not running and _T2_ is running.

A thread _T_ expires its quantum when an amount of time equal to _T_'s quantum
has elapsed since _T_ entered the running state and _T_ did not block, terminate
or call `thread-yield!`. At that point _T_ exits the running state to allow
other threads to run. A thread's quantum is thus an indication of the rate of
progress of the thread relative to the other threads of the same priority.
Moreover, the resolution of the timer measuring the running time may cause a
certain deviation from the quantum, so a thread's quantum should only be viewed
as an approximation of the time it can run before yielding to another thread.

Threads blocked on a given mutex or condition variable will unblock in an order
which is consistent with decreasing priority and increasing blocking time (i.e.
the highest priority thread unblocks first, and among equal priority threads the
one that blocked first unblocks first).
