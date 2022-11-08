---
title: Debugging example
---

Here is a sample interaction with `gsi`:

```shell
$ gsi
Gambit v4.9.4

> (define (invsqr x) (/ 1 (expt x 2)))
> (define (mymap fn lst)
    (define (mm in)
      (if (null? in)
          '()
          (cons (fn (car in)) (mm (cdr in)))))
    (mm lst))
> (mymap invsqr '(5 2 hello 9 1))
*** ERROR IN invsqr, (console)@1.25 -- (Argument 1) NUMBER expected
(expt 'hello 2)
1> ,i
#<procedure #2 invsqr> =
(lambda (x) (/ 1 (expt x 2)))
1> ,e
x = 'hello
1> ,b
0  invsqr                    (console)@1:25          (expt x 2)
1  #<procedure #4>           (console)@6:17          (fn (car in))
2  #<procedure #4>           (console)@6:31          (mm (cdr in))
3  #<procedure #4>           (console)@6:31          (mm (cdr in))
4  (interaction)             (console)@8:1           (mymap invsqr '(5 2 hel...
1> ,+
1  #<procedure #4>           (console)@6.17          (fn (car in))
1\1> (pp #4)
(lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
1\1> ,e
in = '(hello 9 1)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\1> ,(e mm)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\1> fn
#<procedure #2 invsqr>
1\1> (pp fn)
(lambda (x) (/ 1 (expt x 2)))
1\1> ,+
2  #<procedure #4>           (console)@6.31          (mm (cdr in))
1\2> ,e
in = '(2 hello 9 1)
mm = (lambda (in) (if (null? in) '() (cons (fn (car in)) (mm (cdr in)))))
fn = invsqr
lst = '(5 2 hello 9 1)
1\2> ,(c (list 3 4 5))
(1/25 1/4 3 4 5)
> ,q
```

