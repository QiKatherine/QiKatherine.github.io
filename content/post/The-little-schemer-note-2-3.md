+++
title = "The Little Schemer speedy referring note (2/3)"
date = 2019-12-23T01:35:00+00:00
lastmod = 2019-12-31T02:07:27+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


## Chapter 5 It's Full Of Stars {#chapter-5-it-s-full-of-stars}

The starred function family re-write the functions in previous chapters with a
bit more recursions. The purpose is to achieve higher level of
mission, or to make the work more thoroughly.

---

For example, `(rember a l)` removes multiple occurrences `a` as the _first level_ s-expression of list `l`, whereas `(rember* a l)`
removes `a` as _any level_ s-expressions. This is done by changing one more
condition in the outer `(else)` by recusion. Similar works can be also achieved
as insertR\*, insertL\*.

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

Similarly, we re-write more other functions as:

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

We notice that: in all the starred functions above, we always asked three questions: **(1) Is the list null?
(2) If not, is the `(car argument)` an atom? (3) If yes, is the predicate `(eq?)` true?** Those questions enable a
function to work on any cases with: empty list; atom _consed_ to a list; list
_consed_ to a list.

As comparison, the below function shows a case where only ONE question is asked, and
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
      ((null? l1) #f); the above predicates have ruled out l2 is empty or list
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

Notice that the deduction in comments happens because `(cond)` excutes the
predicate one by one, meaning the second predicate gets to run only when the
first predicate is #f, which gives us information for inference.

It's not quite pithy, we can acutally merge some of the #f predicates together
with `(or)`.

{{< highlight scheme >}}
;(equal2?? '(a (b c)) '(a (b c))) -> #t
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
{{< /highlight >}}

{{< highlight scheme >}}
;(eqlist3?
;  '(beef ((sausage)) (and (soda)))
;  '(beef ((salami)) (and (soda)))) -> #f
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

{{< highlight scheme >}}
;(rember '(foo (bar (baz))) '(apples (foo (bar (baz))) oranges)) -> '(apples oranges)
(define rember
  (lambda (s l)
    (cond
      ((null? l) '())
      ((equal2?? (car l) s) (cdr l))
      (else (cons (car l) (rember s (cdr l)))))))
{{< /highlight >}}

So far, the content tells rules that `the first thing is to write all the operations
for every predicate condition and make sure the program is right; and then to simplify it.`


## Chapter 6 Shadows {#chapter-6-shadows}

---

Then we introduce some more of the number operations. `(numbered? argument)`
return if the argument contains anything other than numbers and o+, o\* , ^. The
function shows if aexp is not an atom, meaning the aexp consists of two
sub-expression. It will check if both of the sub-expressions, i.e whether the
element on the left of operator (o+ or o\* or o^) and the right element are both
numbered?, and this process gets to run recursively again and again.

{{< highlight scheme >}}
;(numbered? '(5 ox (3 'foo 2))) -> #f
(define numbered?
  (lambda (aexp)
    (cond
      ((atom? aexp) (number? aexp))
      ((eq? (car (cdr aexp)) 'o+)
       (and (numbered? (car aexp))
            (numbered? (car (cdr (cdr aexp))))))
      ((eq? (car (cdr aexp)) 'ox)
       (and (numbered? (car aexp))
            (numbered? (car (cdr (cdr aexp))))))
      ((eq? (car (cdr aexp)) 'o^)
       (and (numbered? (car aexp))
            (numbered? (car (cdr (cdr aexp))))))
      (else #f))))

;if we only input numeric expression, we can simplify as:
(define numbered?
  (lambda (aexp)
    (cond
      ((atom? aexp) (number? aexp))
      (else
        (and (numbered? (car aexp))
             (numbered? (car (cdr (cdr aexp)))))))))
{{< /highlight >}}

The function `(value argument)` print the calculated value of a numberic
expression: when the argument is a single atom, it prints itself; when the
argument is conpound, meaning more sub-expressions joint by operator, it
recursively runs until hits a single atom, and then the aggregate calculation is
done from inside to outside. However, notice that the predicate changes according to the syntax
setting of numeric expressions, taking addition as example:

{{< highlight scheme >}}
;(1 + 1) is written as:
 ((eq? (car (cdr nexp)) 'o+)

;(+ 1 1) is written as:
 ((eq? (car nexp) 'o+)
{{< /highlight >}}

So the `(value)` can be written in two forms:

{{< highlight scheme >}}
;(value '(1 o+ (3 o^ 4))) ->82
  (define value
    (lambda exp)
      (cond
        ((atom? nexp) nexp)
        ((eq? (car (cdr nexp)) 'o+)
         (+ (value (car nexp))
            (value (car (cdr (cdr nexp))))))
        ((eq? (car (cdr nexp)) 'o*)
         (* (value (car nexp))
            (value (car (cdr (cdr nexp))))))
        ((eq? (car (cdr nexp)) 'o^)
         (expt (value (car nexp))
               (value (car (cdr (cdr nexp))))))
        (else #f))))

;(value-prefix '(o+ 1 (o^ 3 4))) -> 82
(define value-prefix
    (lambda (nexp)
      (cond
        ((atom? nexp) nexp)
        ((eq? (car nexp) 'o+)
         (+ (value-prefix (car (cdr nexp)))
            (value-prefix (car (cdr (cdr nexp))))))
        ((eq? (car nexp) 'o*)
         (* (value-prefix (car (cdr nexp)))
            (value-prefix (car (cdr (cdr nexp))))))
        ((eq? (car nexp) 'o^)
         (expt (value-prefix (car (cdr nexp)))
               (value-prefix (car (cdr (cdr nexp))))))
        (else #f))))
{{< /highlight >}}

For this type of prefixed operator syntax, we can rewrite it with pre-defined
1st and 2nd sub-expressions:

{{< highlight scheme >}}
(define 1st-sub-exp
  (lambda (aexp)
    (car aexp)))

(define 2nd-sub-exp
  (lambda (aexp)
    (car (cdr (cdr aexp)))))

(define operator
  (lambda (aexp)
    (car (cdr aexp))))

(define value-prefix-helper
  (lambda (nexp)
    (cond
      ((atom? nexp) nexp)
      ((eq? (operator nexp) 'o+)
       (+ (value-prefix (1st-sub-exp nexp))
          (value-prefix (2nd-sub-exp nexp))))
      ((eq? (car nexp) 'o*)
       (* (value-prefix (1st-sub-exp nexp))
          (value-prefix (2nd-sub-exp nexp))))
      ((eq? (car nexp) 'o^)
       (expt (value-prefix (1st-sub-exp nexp))
             (value-prefix (2nd-sub-exp nexp))))
      (else #f))))
{{< /highlight >}}

From this chapter we begin to **design different functions for different syntax
setting functions**, we we know this work will not be limited to `(1 + 1) and (+1
1)`. Actually, the syntactic/symbolic expression can be in any form, such as: if
0 is written as `quote()`, then 4 can be written as `(() () () ()) or
((((()))))`. Accordingly, the numberic operations can be written as:

{{< highlight scheme >}}
; sero? just like zero?
(define sero?
  (lambda (n)
    (null? n)))

; edd1 just like add1
(define edd1
  (lambda (n)
    (cons '() n)))

; zub1 just like sub1
(define zub1
  (lambda (n)
    (cdr n)))

; .+ just like o+
;(.+ '(()) '(() ())) -> '(() () ())
(define .+
  (lambda (n m)
    (cond
      ((sero? m) n)
      (else
        (edd1 (.+ n (zub1 m)))))))

; tat? just like lat?
;(tat? '((()) (()()) (()()()))) -> #f
(define tat?
  (lambda (l)
    (cond
      ((null? l) #t)
      ((atom? (car l))
       (tat? (cdr l)))
      (else #f))))
{{< /highlight >}}


## Chapter 7 Friend and Relations {#chapter-7-friend-and-relations}

In this chapter, we see more examples of using the previously defined functions
to develop more functions.

**set** is a list consists of **non-repeated** atoms. The function `(set? argument)`
 checks whether a list is a set. It can be written with `(member?)`:

{{< highlight scheme >}}
  (define member?
    (lambda (a lat)
      (cond
        ((null? lat) #f)
        (else (or (eq? (car lat) a)
                  (member? a (cdr lat)))))))

;(set? '(apple 3 pear 4 9 apple 3 4)) -> #f
  (define set?
    (lambda (lat)
      (cond
        ((null? lat) #t)
        ((member? (car lat) (cdr lat)) #f)
        (else
          (set? (cdr lat))))))
{{< /highlight >}}

`(makeset argument)` make a new list by removing duplicated atoms in argument
list. For a repeated atom in list, in order to retain the first occurrence while remove others, we use `(multirember)`:

{{< highlight scheme >}}
(define multirember
  (lambda (a lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) a)
       (multirember a (cdr lat)))
      (else
        (cons (car lat) (multirember a (cdr lat)))))))

;(makeset '(apple 3 pear 4 9 apple 3 4))
(define makeset
  (lambda (lat)
    (cond
      ((null? lat) '())
      (else
        (cons (car lat)
              (makeset (multirember (car lat) (cdr lat))))))))
{{< /highlight >}}

`(subset? argument1 argument2)` checks whether argument1 is a subset of
argument2. The other two check for equality and intersection respectively.
Comparing the three algorithms, we can find how the different mathmatical
functions can be achieved by manipulating the logical tools.

{{< highlight scheme >}}
; (subset? '(4 pounds of horseradish)
; '(four pounds of chicken and 5 ounces of horseradish)) -> #f
(define subset?
    (lambda (set1 set2)
      (cond
        ((null? set1) #t)
        (else (and (member? (car set1) set2)
                   (subset? (cdr set1) set2))))))

;(eqset? '(a b c) '(a b)) -> #f
  (define eqset?
    (lambda (set1 set2)
      (and (subset? set1 set2)
           (subset? set2 set1))))

;(intersect? '(stewed tomatoes and macaroni) '(macaroni and cheese)) -> #t
  (define intersect?
    (lambda (set1 set2)
      (cond
        ((null? set1) #f)
        (else (or (member? (car set1) set2)
                  (intersect? (cdr set1) set2))))))
{{< /highlight >}}

The below functions do a bit more, they return intersection and union results as
 **set** out of the arguments sets.

{{< highlight scheme >}}
;(intersect '(stewed tomatoes and macaroni) '(macaroni and cheese)) -> '(and macaroni)
(define intersect
  (lambda (set1 set2)
    (cond
      ((null? set1) '())
      ((member? (car set1) set2)
       (cons (car set1) (intersect (cdr set1) set2)))
      (else
        (intersect (cdr set1) set2)))))

;(union '(stewed tomatoes and macaroni casserole) '(macaroni and cheese))
; -> '(stewed tomatoes casserole macaroni and cheese)
(define union
  (lambda (set1 set2)
    (cond
      ((null? set1) set2)
      ((member? (car set1) set2)
       (union (cdr set1) set2))
      (else (cons (car set1) (union (cdr set1) set2))))))
{{< /highlight >}}

{{< highlight scheme >}}
;(xxx '(a b c) '(a b d e f)) -> '(c)
(define xxx
  (lambda (set1 set2)
    (cond
      ((null? set1) '())
      ((member? (car set1) set2)
       (xxx (cdr set1) set2))
      (else
        (cons (car set1) (xxx (cdr set1) set2))))))
{{< /highlight >}}