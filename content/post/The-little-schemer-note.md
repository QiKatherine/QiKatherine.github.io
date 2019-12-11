+++
title = "The Little Schemer note"
date = 2019-12-10T23:20:00+00:00
lastmod = 2019-12-11T02:08:32+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


## Chapter 2 Do It Again {#chapter-2-do-it-again}

**atom** is the smallest element, something NOT enclosed by pair of
parenthesis: a string of digits/characters/numbers.

**list** is something enclosed by pair of parenthesis: something can be nothing, can be
atom, can be another list.

**s-expression** describe both atom and list/pair. A series of s-expression
enclosed by parenthesis is **list**.

`(car argument)`: returns first s-expression among all the first-level
 s-expression within a list.

`(cdr argument)`: returns the complement set of `(car argument)`, including
the parenthesis.

`car/cdr` both take and output non-empty list. `cdr` cannot work on null list or
atom. For `(han), han, ()` cdr can only work on the first.

`(cons argument1 argument2)` add arg1(s-exp) onto arg2(list), the output is
 list.

`(null? argument)` returns T when the argument(list) is `(quote()), '() and ()` returns
 error when argument is `atom`; returns F for others.

`(atom? argument)` checks if the argument(any s-expression) is atom.

`(eq? argument1 argument2)` returns T when arguments(atoms) are equal; returns
 error when arguments are numerical or list; returns F for others.

`(lat? argument)` checks if every s-expression in a list is atom. Since the
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

`(member? argument1 argument2)` checks if argument1(atom) is in
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

`(rember argument1 argument2)` removes the first occurrence of argument1(atom) from
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

`(first argument)` : the argument is a non-empty list, possibly consists of more
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

`(insetR new old lat)` and `(insetL new old lat)` insert the new atom at the
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

`(multirember a lat)` removes all the occurrences of a in lat(list).
`(multiinsertR new old lat)` and `(multiinsertL new old lat)` insert new atom at the
RIGHT/LEFT side of old atom for every occurrence of old in lat(list).

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

`(multirsubst new old lat)` replace old atom with new atom for every occurrence
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
