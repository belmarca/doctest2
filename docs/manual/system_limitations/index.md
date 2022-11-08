---
title: System limitations
---

- On some systems floating point overflows will cause the program to terminate
  with a floating point exception.
- On some systems floating point operations involving `+nan.0` `+inf.0`,
  `-inf.0`, or `-0.` do not return the value required by the IEEE 754 floating
  point standard.
- The maximum number of arguments that can be passed to a procedure by the apply
  procedure is 8192.
