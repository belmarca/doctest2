---
title: Extensions to standard procedures
---

#### transcript-on
#### transcript-off

!!! info "R5RS procedures"

```scheme
(transcript-on file) ;; procedure
(transcript-off) ;; procedure
```

These procedures do nothing.

#### call-with-current-continuation
#### call/cc

```scheme
(call-with-current-continuation proc) ;; procedure
(call/cc proc) ;; procedure
```

The procedure `call-with-current-continuation` is bound to the global variables
`call-with-current-continuation` and `call/cc`.
