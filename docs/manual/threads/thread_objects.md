---
title: Thread objects
---

A running thread is a thread that is currently executing. A runnable thread is a
thread that is ready to execute or running. A thread is blocked if it is waiting
for a mutex to become unlocked, an I/O operation to become possible, the end of
a "sleep" period, etc. A new thread is a thread that has been allocated but has
not yet been initialized. An initialized thread is a thread that can be made
runnable. A new thread becomes runnable when it is started by calling
`thread-start!`. A terminated thread is a thread that can no longer become
runnable (but deadlocked threads are not considered terminated). The only valid
transitions between the thread states are from new to initialized, from
initialized to runnable, between runnable and blocked, and from any state except
new to terminated as indicated in the following diagram:

```
                                            unblock
                          start            <-------
NEW -------> INITIALIZED -------> RUNNABLE -------> BLOCKED
                       \             |      block  /
                        \            v            /
                         +-----> TERMINATED <----+
```

Each thread has a base priority, which is a real number (where a higher
numerical value means a higher priority), a priority boost, which is a
nonnegative real number representing the priority increase applied to a thread
when it blocks, and a quantum, which is a nonnegative real number representing a
duration in seconds.

Each thread has a specific field which can be used in an application specific
way to associate data with the thread (some thread systems call this "thread
local storage").

Each thread has a mailbox which is used for inter-thread communication.
