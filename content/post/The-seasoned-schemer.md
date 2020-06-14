+++
title = "The Seasoned Schemer"
date = 2020-06-14T01:13:00+01:00
lastmod = 2020-06-14T01:13:13+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

-   State "DONE"       from "TODO"       <span class="timestamp-wrapper"><span class="timestamp">[2020-06-14 Sun 01:13]</span></span>

This book is continuous to book The Little Schemer, which the notes can be found
in previous articles. So we begin with chapter 11.


## Chapter 11 Welcome Back to the Show {#chapter-11-welcome-back-to-the-show}

Firstly, we are defining function `(two-in-a-row?)` to test whether there are two
identical atoms in a row in a list. A initial trial is to use a function
`(is-first?)` that takes the first and the first of the `(cdr lat)` as input to
check equality. If it returns #f, the `(or)` predicate will make sure the
function recursively check the rest of list until there are two identical atoms appear.

{{< highlight scheme >}}
(define two-in-a-row?
  (lambda (lat)
    (cond
      ((null? lat) #f)
      (else
        (or (is-first? (car lat) (cdr lat))
            (two-in-a-row? (cdr lat)))))))

(define is-first?
  (lambda (a lat)
    (cond
      ((null? lat) #f)
      (else (eq? (car lat) a)))))

;(two-in-a-row? '(Italian sardines sardines spaghetti parsley)) -> true
{{< /highlight >}}

There are other ways to realize the same functionality. The trait of the above one
is that, it is the `(or two-in-a-row?)` get to decide termination. The `(is-first?)` returns #f for two cases, one is when the original lat has
one atom, the other is no two-in-row. The first case will not make the function
terminate - even though the lat is null with `(is-first?)` returns #f, the `(or
two-in-a-row?)` will continue running for at least another round. This makes the
program a bit wrong: we want to add termination judgment to `(is-first?)`, to
make it immediately terminate for empty argument, like this:

{{< highlight scheme >}}
(define two-in-a-row-2?
  (lambda (lat)
    (cond
      ((null? lat) #f)
      (else
       (is-first-b? (car lat) (cdr lat))))))

(define is-first-b?
  (lambda (a lat)
    (cond
      ((null? lat) #f)
      (else (or (eq? (car lat) a)
                (two-in-a-row-2? lat))))))
;(two-in-a-row-2? '(Italian)) will terminate in the first predicate of is-first-b?
{{< /highlight >}}

We notice that the `(is-first-b?)` has a very similar procedure to
`(two-in-a-row-2?)`. A slight change can make `(is-first?)` very similar to its
master function - so calling two functions is almost like calling one. The
core `(is-first?)` uses intermediate argument `(a)` to save the first element
for comparison, so we are using `(preceding)` for the same purpose.

{{< highlight scheme >}}
(define two-in-a-row-final?
  (lambda (lat)
    (cond
      ((null? lat) #f)
      (else (two-in-a-row-b? (car lat) (cdr lat))))))

;the original is-first?
(define two-in-a-row-b?
  (lambda (preceding lat)
    (cond
      ((null? lat) #f)
      (else (or (eq? (car lat) preceding)
                (two-in-a-row-b? (car lat) (cdr lat)))))))

;(two-in-a-row-final? '(Italian sardines sardines spaghetti parsley)) -> true
{{< /highlight >}}

With the modification, in `(two-in-a-row-b?)` part, the designated recursion function has
become itself, rather than `(two-in-a-row-final?)`. This design is inspiring to
any function that can be solved with a intermediate changing argument, and a one
by one searching procedure. For example, a function stores step-by step sums of a tuple.

{{< highlight scheme >}}
(define sum-of-prefixes-b
  (lambda (sonssf tup)     ; sonssf stands for 'sum of numbers seen so far'
    (cond
      ((null? tup) '())
      (else (cons (+ sonssf (car tup))
                  (sum-of-prefixes-b
                   (+ sonssf (car tup))
                   (cdr tup)))))))

; sum-of-prefixes function finds the running sum of a list of numbers
(define sum-of-prefixes
  (lambda (tup)
    (sum-of-prefixes-b 0 tup)))

;(sum-of-prefixes '(2 1 9 17 0)) -> '(2 3 12 29 29)
{{< /highlight >}}

And another case of selecting reversed items base on the number of the atom.

{{< highlight scheme >}}
(define scramble-b
  (lambda (tup rev-pre)
    (cond
      ((null? tup) '())
      (else
       (cons (pick (car tup) (cons (car tup) rev-pre))
             (scramble-b (cdr tup)
                         (cons (car tup) rev-pre)))))))

(define scramble
  (lambda (tup)
    (scramble-b tup '())))

; Examples of scramble
(scramble '(1 1 1 3 4 2 1 1 9 2))       ; '(1 1 1 1 1 4 1 1 1 9)
(scramble '(1 2 3 4 5 6 7 8 9))         ; '(1 1 1 1 1 1 1 1 1)
(scramble '(1 2 3 1 2 3 4 1 8 2 10))    ; '(1 1 1 1 1 1 1 1 2 8 2)
{{< /highlight >}}
