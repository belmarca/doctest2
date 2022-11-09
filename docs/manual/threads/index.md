---
title: Threads
---

Gambit supports the execution of multiple Scheme threads. These threads are
managed entirely by Gambit's runtime and are not related to the host operating
system's threads. Gambit's runtime does not currently take advantage of
multiprocessors (i.e. at most one thread is running).
