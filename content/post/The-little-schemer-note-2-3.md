+++
title = "The Little Schemer speedy referring note (2/3)"
date = 2019-12-23T01:35:00+00:00
lastmod = 2019-12-26T02:25:30+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


## Chapter 5 It's Full Of Stars {#chapter-5-it-s-full-of-stars}

The starred function means that compared with the premature function we
defined above, we need to further extend it to achieve higher level of
mission, or to do the work more thoroughly.

---

For example, `(rember a l)` removes multiple occurrences `a` as the first level s-expression of list `l`, whileas `(rember* a l)`
removes `a` as any level s-expressions. This is improved by changing one more condition with recusion and works
similarly in the blow algorithms.

{{< highlight scheme >}}
; (rember* 'cup '((coffee) cup ((tea) cup) (and (hick)) cup))
; -> '((coffee) ((tea)) (and (hick)))
(define rember*
  (lambda (a l)
    (cond
      ((null? l) '())
      ((atom? (car l))
       (cond
         ((eq? (car l) a)
          (rember* a (cdr l)))
         (else
           (cons (car l) (rember* a (cdr l))))))
      (else
        (cons (rember* a (car l)) (rember* a (cdr l)))))))

;(insertR* 'roast 'chuck
;  '((how much (wood)) could ((a (wood) chuck)) (((chuck))) (if (a) ((wood chuck))) could chuck wood))
; -> ((how much (wood)) could ((a (wood) chuck roast)) (((chuck roast)))
(define insertR*
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((atom? (car l))
       (cond
         ((eq? (car l) old)
          (cons old (cons new (insertR* new old (cdr l)))))
         (else
           (cons (car l) (insertR* new old (cdr l))))))
      (else
        (cons (insertR* new old (car l)) (insertR* new old (cdr l)))))))

;(insertL* 'pecker 'chuck
;  '((how much (wood)) could ((a (wood) chuck)) (((chuck))) (if (a) ((wood chuck))) could chuck wood))
; -> ((how much (wood)) could ((a (wood) chuck pecker)) (((chuck pecker))) (if (a) ((wood chuck pecker))) could chuck pecker wood)
(define insertL*
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((atom? (car l))
       (cond
         ((eq? (car l) old)
          (cons new (cons old (insertL* new old (cdr l)))))
         (else
           (cons (car l) (insertL* new old (cdr l))))))
      (else
        (cons (insertL* new old (car l)) (insertL* new old (cdr l)))))))
{{< /highlight >}}

Similarly, we re-write other functions mentioned before:

{{< highlight scheme >}}
;(occur* 'banana '((banana) (split ((((banana ice))) (cream (banana)) sherbet)) (banana) (bread) (banana brandy)))
; -> 5
(define occur*
  (lambda (a l)
    (cond
      ((null? l) 0)
      ((atom? (car l))
       (cond
         ((eq? (car l) a)
          (add1 (occur* a (cdr l))))
         (else
           (occur* a (cdr l)))))
      (else
        (+ (occur* a (car l))
           (occur* a (cdr l)))))))

;(subst* 'orange 'banana
;  '((banana) (split ((((banana ice))) (cream (banana)) sherbet)) (banana) (bread) (banana brandy)))
; -> '((orange) (split ((((orange ice))) (cream (orange)) sherbet)) (orange) (bread) (orange brandy))
(define subst*
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((atom? (car l))
       (cond
         ((eq? (car l) old)
          (cons new (subst* new old (cdr l))))
         (else
           (cons (car l) (subst* new old (cdr l))))))
      (else
        (cons (subst* new old (car l)) (subst* new old (cdr l)))))))

;(member 'chips '((potato) (chips ((with) fish) (chips)))) -> #t
(define member*
  (lambda (a l)
    (cond
      ((null? l) #f)
      ((atom? (car l))
       (or (eq? (car l) a)
           (member* a (cdr l))))
      (else
        (or (member* a (car l))
            (member* a (cdr l)))))))
{{< /highlight >}}

In the above starred functions, we asked three questions: **(1) Is the list null?
(2) Is
the `(car argument)` an atom? (3) Is the predicate `(eq?)` true?** Those questions enable a
function to work on any cases with: empty list; atom _consed_ to a list; list
_consed_ to a list.

The below function shows the a case where only ONE question is asked, and
therefore it works only on limited types argument:

{{< highlight scheme >}}
;(leftmost '((potato) (chips ((with) fish) (chips)))) -> 'potato
(define leftmost
  (lambda (l)
    (cond
      ((atom? (car l)) (car l))
      (else (leftmost (car l))))))
{{< /highlight >}}

Theoritically, there were two questions checked above, but the null? and atom?
is achieved in the same predicate: if the list is null, the `(atom? car(lat))`
would also return F and the recursion in `(else)` will be excuted. This type of
simplification will help us to improve functions into a pithy fashion.

For example, we use this function to check the equality of two lists. Based on
the 3 questions we asked for operations in one list, two lists will generate
3\*3 = 9 predicates in permutation, and the `(eq?)` is happening when both of the
arguments are atom:

{{< highlight scheme >}}
;(eqlist? '(strawberry ice cream) '(strawberry ice cream)) -> #t
(define eqlist?
  (lambda (l1 l2)
    (cond
      ; case 1: l1 is empty, l2 is empty, atom, list
      ((and (null? l1) (null? l2)) #t)
      ((and (null? l1) (atom? (car l2))) #f)
      ((null? l1) #f)
      ; case 2: l1 is atom, l2 is empty, atom, list
      ((and (atom? (car l1)) (null? l2)) #f)
      ((and (atom? (car l1)) (atom? (car l2)))
       (and (eq? (car l1) (car l2))
            (eqlist? (cdr l1) (cdr l2))))
      ((atom? (car l1)) #f)
      ; case 3: l1 is a list, l2 is empty, atom, list
      ((null? l2) #f)
      ((atom? (car l2)) #f)
      (else
        (and (eqlist? (car l1) (car l2))
             (eqlist? (cdr l1) (cdr l2)))))))
{{< /highlight >}}

It's not quite pithy but there is room for improvement:

{{< highlight scheme >}}

;(eqlist2?  '(strawberry ice cream)  '(strawberry ice cream)) -> #t
(define eqlist2?
  (lambda (l1 l2)
    (cond
      ; case 1: l1 is empty, l2 is empty, atom, list
      ((and (null? l1) (null? l2)) #t)
      ((or (null? l1) (null? l2)) #f)
      ; case 2: l1 is atom, l2 is empty, atom, list
      ((and (atom? (car l1)) (atom? (car l2)))
       (and (eq? (car l1) (car l2))
            (eqlist2? (cdr l1) (cdr l2))))
      ((or (atom? (car l1)) (atom? (car l2)))
       #f)
      ; case 3: l1 is a list, l2 is empty, atom, list
      (else
        (and (eqlist2? (car l1) (car l2))
             (eqlist2? (cdr l1) (cdr l2)))))))

;(eqlist3?  '(strawberry ice cream)  '(strawberry ice cream)) -> #t
(define eqlist3?
  (lambda (l1 l2)
    (cond
      ((and (null? l1) (null? l2)) #t)
      ((or (null? l1) (null? l2)) #f)
      (else
        (and (equal2?? (car l1) (car l2))
             (equal2?? (cdr l1) (cdr l2)))))))
{{< /highlight >}}

After defining `(eqlist?)` to compare the equality of two lists, we can further
improve `(rember)` to remove lists as argument, not just removing atoms like in the
previous chapter.
