---
title: Timeouts
---

# Timeouts

All synchronization primitives which take a `timeout` parameter accept three types
of values as a timeout, with the following meaning:

- a time object represents an absolute point in time
- an exact or inexact real number represents a relative time in seconds from the
  moment the primitive was called
- `#f` means that there is no timeout

When a timeout denotes the current time or a time in the past, the
synchronization primitive claims that the timeout has been reached only after
the other synchronization conditions have been checked. Moreover the thread
remains running (it does not enter the blocked state). For example,
`(mutex-lock! m 0)` will lock mutex `m` and return `#t` if `m` is currently
unlocked, otherwise `#f` is returned because the timeout is reached.
