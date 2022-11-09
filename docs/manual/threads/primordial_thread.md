---
title: Primordial thread
---

The execution of a program is initially under the control of a single thread
known as the `primordial thread`. The primordial thread has an unspecified base
priority, priority boost, boosted flag, quantum, name, specific field, dynamic
environment, `dynamic-wind` stack, and `exception-handler`. All threads are
terminated when the primordial thread terminates (normally or not).

