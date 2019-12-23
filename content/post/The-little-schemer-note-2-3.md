+++
title = "The Little Schemer speedy referring note (2/3)"
date = 2019-12-23T01:35:00+00:00
lastmod = 2019-12-23T02:38:55+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


## Chapter 5 It's full of stars {#chapter-5-it-s-full-of-stars}

The starred function means that compared with the premature function we
defined above, we need to further extend it to achieve higher level of
mission, or to do the work more thoroughly. For example, `(rember a l)`
removes multiple occurrences `a` as the first level s-expression of list `l`, whileas `(rember* a l)`
removes `a` as any level s-expressions.

{{< highlight scheme >}}
; (rember*
;   'cup
;   '((coffee) cup ((tea) cup) (and (hick)) cup))
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

;(insertR*
;  'roast
;  'chuck
;  '((how much (wood)) could ((a (wood) chuck)) (((chuck)))
;    (if (a) ((wood chuck))) could chuck wood))
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

;(insertL*
;  'pecker
;  'chuck
;  '((how much (wood)) could ((a (wood) chuck)) (((chuck)))
;    (if (a) ((wood chuck))) could chuck wood))
; -> ((how much (wood)) could ((a (wood) chuck pecker)) (((chuck pecker)))
;      (if (a) ((wood chuck pecker))) could chuck pecker wood)
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

{{< highlight scheme >}}
;(occur*
;  'banana
;  '((banana)
;    (split ((((banana ice)))
;            (cream (banana))
;            sherbet))
;    (banana)
;    (bread)
;    (banana brandy)))
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
;(subst*
;  'orange
;  'banana
;  '((banana)
;    (split ((((banana ice)))
;            (cream (banana))
;            sherbet))
;    (banana)
;    (bread)
;    (banana brandy)))
; -> '((orange)
;      (split ((((orange ice)))
;              (cream (orange))
;              sherbet))
;      (orange)
;      (bread)
;      (orange brandy))
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

;(member
;  'chips
;  '((potato) (chips ((with) fish) (chips)))) -> #t
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
