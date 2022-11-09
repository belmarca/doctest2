---
title: Introduction
---

Multithreading is a paradigm that is well suited for building complex systems
such as: servers, GUIs, and high-level operating systems. Gambit's thread system
offers mechanisms for creating threads of execution and for synchronizing them.
The thread system also supports features which are useful in a real-time
context, such as priorities, priority inheritance and timeouts.

The thread system provides the following data types:

- Thread (a virtual processor which shares object space with all other threads)
- Mutex (a mutual exclusion device, also known as a lock and binary semaphore)
- Condition variable (a set of blocked threads)

