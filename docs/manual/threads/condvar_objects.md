---
title: Condition variable objects
---

# Condition variable objects

A condition variable represents a set of blocked threads. These blocked threads
are waiting for a certain condition to become true. When a thread modifies some
program state that might make the condition true, the thread unblocks some
number of threads (one or all depending on the primitive used) so they can check
if the condition is now true. This allows complex forms of interthread
synchronization to be expressed more conveniently than with mutexes alone.

Each condition variable has a `specific field` which can be used in an
application specific way to associate data with the condition variable.
