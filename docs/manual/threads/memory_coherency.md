---
title: Memory coherency
---

# Memory coherency

Read and write operations on the store (such as reading and writing a variable,
an element of a vector or a string) are not atomic. It is an error for a thread
to write a location in the store while some other thread reads or writes that
same location. It is the responsibility of the application to avoid write/read
and write/write races through appropriate uses of the synchronization
primitives.

Concurrent reads and writes to ports are allowed. It is the responsibility of
the implementation to serialize accesses to a given port using the appropriate
synchronization primitives.
