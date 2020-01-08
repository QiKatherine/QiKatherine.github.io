+++
title = "The Little Schemer speedy referring note (3/3)"
lastmod = 2020-01-08T23:55:57+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

## Chapter 9 and Again and Again and Again {#chapter-9-and-again-and-again-and-again}

The key thing of writing recursion is making sure the function works in meaningful
direction with `termination condition`. That's the basic requirement for any
function: it takes in an argument and produces an output accordingly.
For example, the `(pick)` will return the n-th element in lat. The
`(keep-looking)` calls `(pick)` to see if `a` is equal to the random atom in lat (assuming the numbers in lat is random).

{{< highlight scheme >}}
;pick return n-th element in lat:
(define pick
  (lambda (n lat)
    (cond
      ((zero? (sub1 n)) (car lat))
      (else
        (pick (sub1 n) (cdr lat))))))

(define keep-looking
  (lambda (a sorn lat)
    (cond
      ((number? sorn)
       (keep-looking a (pick sorn lat) lat))
      (else (eq? sorn a )))))

(define looking
  (lambda (a lat)
    (keep-looking a (pick 1 lat) lat)))

; Example of looking
(looking 'caviar '(6 2 4 caviar 5 7 3))         ; #t
(looking 'caviar '(6 2 grits caviar 5 7 3))     ; #f
{{< /highlight >}}

From the examples we can find that the `'caviar` is the 4th element in the
first example, and the list contains 4. So running it is very likely to hit the
termination condition. But in second example the `'caviar` is the 4th element whereas no 4
is contained in the list, so the recursion will run forever, which means the
function won't always return a value for an input. This illed function is called **partial function** as opposed to **total function** defined by us previously.

Let's see another example. We've defined **pair** is a list containing two
s-expressions (i.e. a binary tree). The `shift` takes a pair whose first component is a pair and builds a pair by shifting the second part of the first component into
the second component.

{{< highlight scheme >}}
(define first
  (lambda (p)
    (car p)))

(define second
  (lambda (p)
    (car (cdr p))))

(define build
  (lambda (s1 s2)
    (cons s1 (cons s2 '()))))

;(shift '((a b) c)) -> '(a (b c))
;(shift '((a b) (c d))) -> '(a (b (c d)))
(define shift
  (lambda (pair)
    (build (first (first pair))
      (build (second (first pair))
        (second pair)))))

(define a-pair?
  (lambda (x)
    (cond
      ((atom? x) #f)
      ((null? x) #f)
      ((null? (cdr x)) #f)
      ((null? (cdr (cdr x))) #t)
      (else #f))))

(define align
  (lambda (pora)
    (cond
      ((atom? pora) pora)
      ((a-pair? (first pora))
       (align (shift pora)))
      (else (build (first pora)
              (align (second pora)))))))
{{< /highlight >}}

Based on `shift` we further creat `align`. Don't rush to run the function. We
 notice something wrong in the  second predicate where `shift` is called as argument. The seventh commandment emphasize that recursion should happen on the subparts that are **of the same nature**:
 either on the sublists of a list; or on the subexpressions of an arithmetic
 expression. But the `align` as well as `(keep-looking)` all creat new argument
 in recursion that is **not part** of the original argument. It's an indicator of
 ill, but `(align)` will still generate output for every input, so it's not
 partial function.

We define a very similar function `(shuffle)`, which is partial. It won't
produce value since the `a-pair` predicate will always swap the items of pair, which
makes any input with form of `((a b) (c d))` trapped in infinite item swapping loop.

{{< highlight scheme >}}
;(revpair '((a b) (c d))) -> ((c d) (a b))
(define revpair
  (lambda (p)
    (build (second p) (first p))))

(define shuffle
  (lambda (pora)
    (cond
      ((atom? pora) pora)
      ((a-pair? (first pora))
       (shuffle (revpair pora)))
      (else
        (build (first pora)
          (shuffle (second pora)))))))

(shuffle '(a (b c)))        ; '(a (b c))
(shuffle '(a b))            ; '(a b)
(shuffle '((a b) (c d)))    ; infinite swap pora  Ctrl + c  to break and input q to exit
{{< /highlight >}}

---

This seems unrelated to the above issue so far. We just define two different
ways to measure the mass of the first component of `(align)`. The `(length*)` measures every
atom with same weight, whereas the `(weight*)` puts twice as much weight to the first component.

{{< highlight scheme >}}
(define length*
  (lambda (pora)
    (cond
      ((atom? pora) 1)
      (else
        (+ (length* (first pora))
           (length* (second pora)))))))
;(length* '((a b) c)) -> 3
;(length* '(a (b c)) -> 3

(define weight*
  (lambda (pora)
    (cond
      ((atom? pora) 1)
      (else
        (+ (* (weight* (first pora)) 2)
           (weight* (second pora)))))))
;(weight* '((a b) c)) -> 7
;(weight* '(a (b c)) -> 5
{{< /highlight >}}

---

From `(align)` and `(shuffle)`, we realize that whether the arguments will
decrease in recursion is not the key to infer whether a function is total. We
still want to develop a tool function to detect the partial function.

{{< highlight scheme >}}
(define eternity
  (lambda (x)
    (eternity x)))
{{< /highlight >}}
