+++
title = "The Little Schemer speedy referring note"
date = 2019-12-10T23:20:00+00:00
lastmod = 2019-12-12T01:13:03+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


## Chapter 1-3 Do It Again and Again {#chapter-1-3-do-it-again-and-again}

**atom** is the smallest element, something NOT enclosed by pair of
parenthesis: a string of digits/characters/numbers.

**list** is something enclosed by pair of parenthesis: something can be nothing, can be
atom, can be another list.

**s-expression** describe both atom and list/pair. A series of s-expression
enclosed by parenthesis is **list**.

---

`(car argument)`: returns first s-expression among all the first-level
 s-expression within a list.

`(cdr argument)`: returns the complement set of `(car argument)`, including
the parenthesis.

`car/cdr` both take and output non-empty list. `cdr` cannot work on null list or
atom. For `(han), han, ()` cdr can only work on the first.

`(cons argument1 argument2)`: add arg1(s-exp) onto arg2(list), the output is
 list.

`(null? argument)`: returns T when the argument(list) is `(quote()), '() and ()` returns
 error when argument is `atom`; returns F for others.

`(atom? argument)`: checks if the argument(any s-expression) is atom.

`(eq? argument1 argument2)`: returns T when arguments(atoms) are equal; returns
 error when arguments are numerical or list; returns F for others.

`(lat? argument)`: checks if every s-expression in a list is atom. Since the
 s-expression is either atom or list/pair, `(lat?)` use `(atom?)` as core function.

{{< highlight scheme >}}
; (lat? (Jack Sprat could eat no chicken fat)) -> T, for every element is atom.
(define lat?
  (lambda (l)
    (cond
      ((null? l) #t)
      ((atom? (car l)) (lat? (cdr l)))
      (else #f))))
{{< /highlight >}}

`(member? argument1 argument2)`: checks if argument1(atom) is in
argument2(non-empty list).

{{< highlight scheme >}}
; (member? meat (mashed potatoes and meat gravy)) -> T, for meat is in the list.
(define member?
  (lambda (a lat)
    (cond
      ((null? lat) #f)
      (else (or (eq? (car lat) a)
                (member? a (cdr lat)))))))
{{< /highlight >}}

`(rember argument1 argument2)`: removes the first occurrence of argument1(atom) from
argument2(non-empty list).

{{< highlight scheme >}}
; (rember cup (coffee cup tea cup and hick cup)) -> (coffee tea cup and hick cup)
(define rember
  (lambda (a lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) a) (cdr lat))
      (else (cons (car lat)
                  (rember a (cdr lat)))))))
{{< /highlight >}}

`(first argument)`: the argument is a non-empty list, possibly consists of more
lists. The function returns the a list consists of all the first s-expressions within
the first level lists in argument.

{{< highlight scheme >}}
; (firsts ((five plums) (four) (eleven green oranges))) -> (five four eleven)
(define firsts
  (lambda (l)
    (cond
      ((null? l) '())
      (else
        (cons (car (car l)) (firsts (cdr l)))))))
{{< /highlight >}}

`(insetR new old lat)` and `(insetL new old lat)`: insert the new atom at the
RIGHT/LEFT side of the old atom in lat(list).

{{< highlight scheme >}}
; (insertR topping fudge (ice cream with fudge for dessert)) -> (ice cream with fudge topping for dessert)
(define insertR
  (lambda (new old lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) old)
       (cons old (cons new (cdr lat))))
      (else
        (cons (car lat) (insertR new old (cdr lat)))))))

; (insertL topping fudge (ice cream with fudge for dessert)) -> (ice cream with topping fudge for dessert)
(define insertL
  (lambda (new old lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) old)
       (cons new (cons old (cdr lat))))
      (else
        (cons (car lat) (insertL new old (cdr lat)))))))
{{< /highlight >}}

`(subst new old lat)`: in lat(list), this function replaces old atom with new
atom.
`(subst2 new o1 o2 lat)`: in lat(list), this function checks o1 o2, whichever
occurs firstly is replaced by new atom.

{{< highlight scheme >}}
; (subst topping fudge (ice cream with fudge for dessert)) -> (ice cream with topping for dessert)
  (define subst
    (lambda (new old lat)
      (cond
        ((null? lat) '())
        ((eq? (car lat) old)
         (cons new (cdr lat)))
        (else
          (cons (car lat) (subst new old (cdr lat)))))))

; (subst2 vanilla chocolate banana (banana ice cream with chocolate topping)) ->
; (vanilla ice cream with chocolate topping)
  (define subst2
    (lambda (new o1 o2 lat)
      (cond
        ((null? lat) '())
        ((or (eq? (car lat) o1) (eq? (car lat) o2))
         (cons new (cdr lat)))
        (else
          (cons (car lat) (subst new o1 o2 (cdr lat)))))))
{{< /highlight >}}

`(multirember a lat)`: removes all the occurrences of a in lat(list).
`(multiinsertR new old lat)` and `(multiinsertL new old lat)`: insert new atom at the
RIGHT/LEFT side of old atom for EVERY occurrence of old in lat(list).

{{< highlight scheme >}}
; (multirember cup (coffee cup tea cup and hick cup)) -> (coffee tea and hick)
(define multirember
  (lambda (a lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) a)
       (multirember a (cdr lat)))
      (else
        (cons (car lat) (multirember a (cdr lat)))))))

; (multiinsertR x a (a b c d e a a b)) -> (a x b c d e a x a x b)
(define multiinsertR
  (lambda (new old lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) old)
       (cons old (cons new (multiinsertR new old (cdr lat)))))
      (else
        (cons (car lat) (multiinsertR new old (cdr lat)))))))

; (multiinsertL x a (a b c d e a a b)) -> (x a b c d e x a x a b)
(define multiinsertL
  (lambda (new old lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) old)
       (cons new (cons old (multiinsertL new old (cdr lat)))))
      (else
        (cons (car lat) (multiinsertL new old (cdr lat)))))))
{{< /highlight >}}

`(multirsubst new old lat)`: replaces old atom with new atom for EVERY occurrence
of old atom in lat(list).

{{< highlight scheme >}}
; (multisubst x a (a b c d e a a b))  ; (x b c d e x x b)
define multisubst
  (lambda (new old lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) old)
       (cons new (multisubst new old (cdr lat))))
      (else
        (cons (car lat) (multisubst new old (cdr lat)))))))
{{< /highlight >}}

The multi operation is genenally better designed. It can work with both single
and multiple occurrences of old atoms, and terminate at until it's got null
list. But the single operations terminate right when the first `(eq?)` returns
T. Generally applicable termination should be at finishing the last element.


## Chapter 4 Number Games {#chapter-4-number-games}

In this section, we only consider _whole_ and _positive_ number. We are going to
use addition 1 `(add1 argument)`, and subtraction 1 `(sub1 argument)` as building blocks to construct
various number calculation algorithms.

{{< highlight scheme >}}
;(add1 67) -> 68
(define add1
  (lambda (n) (+ n 1)))

;(sub1 5) -> 4
(define sub1
  (lambda (n) (- n 1)))

;(o+ 46 12) -> 58
(define o+
  (lambda (n m)
    (cond
      ((zero? m) n)
      (else (add1 (o+ n (sub1 m)))))))

;(o- 14 3) -> 11
(define o-
  (lambda (n m)
    (cond
      ((zero? m) n)
      (else (sub1 (o- n (sub1 m)))))))
{{< /highlight >}}

**tup** is either an empty list, or it contains a number and a rest that is also a **tup**.

To enable natural termination on a list we use `(null? list)`; the natural termination on a
tup we use `(null? tup)`; the natural recursion on a number we use `(zero? 0)`.

To enable natural recursion on a list we use `(cdr argument)`; the natural recursion on a
tup we use `(cdr argument)`; the natural recursion on a number we use `(sub1
argument)`. These condition is reused as new argument in inner recursion as
stated in The Fourth Commandment.
