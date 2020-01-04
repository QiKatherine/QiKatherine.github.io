+++
title = "The Little Schemer speedy referring note (2/3)"
date = 2019-12-23T01:35:00+00:00
lastmod = 2020-01-04T01:45:05+00:00
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

For example, `(rember a l)` removes multiple occurrences of `a` as the _first level_ s-expression of list `l`, whereas `(rember* a l)`
removes `a` as _any level_ s-expressions. This is done by changing one more
condition in the outer `(else)` by recusion. We can also improve insertR\*,
insertL\*, occur\*, subst\* and member\* in the same way.

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

The starred functions require more predicates to consider all possibile
situations. After observing the design pattern in the above functions, we
would see that they all asked three fundamental questions: **(1) Is the list null?
(2) If not, is the `(car argument)` an atom? (3) If yes, is the predicate
`(eq?)` true?** It is these questions that enable a
function to work on any cases with: empty list; atom _consed_ to a list; list
_consed_ to a list.

Let's see another function:

{{< highlight scheme >}}
;(leftmost '((potato) (chips ((with) fish) (chips)))) -> 'potato
(define leftmost
  (lambda (l)
    (cond
      ((atom? (car l)) (car l))
      (else (leftmost (car l))))))
{{< /highlight >}}

As comparison, this function shows a case where only ONE question is
asked, therefore it works on less types of argument than the starred functions. But
there is more: you may noticed that there were TWO questions checked above. The `(null?)` and `(atom?)`
are achieved by one predicate with some self deducted logic: if the list is null, the `(atom? car(lat))`
would also return F and the recursion in `(else)` will still be called. This type of
simplification will help us to improve functions into a pithy fashion.

For example, we write `(eqlist?)` to check the equality of two lists. Based on
the 3 golden questions we asked for operating ONE list, designing two lists operation
will require asking 3\*3 = 9 predicates (in permutation), and the `(eq?)` is happening when both of the
arguments are atoms:

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

;(eqlist2? '(a (b c)) '(a (b c))) -> #t
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

Notice that the third predicate can be written as `(null? l1) #f)` because `(cond)` excutes the
predicate one by one. Means that the second predicate gets to run **only when** the
first predicate returns #f which gives us information for inference.  It's not
quite pithy, so in the `(eqlist2?)` we merge some of the redundant #f predicates together
with `(or)`.

This can be further simplified by introducing an S-expression comparison
function `(equal?)`, which itself can be also written in the simplified way as `(eqlist2?)`.

{{< highlight scheme >}}
;(equal? '(a) '(a)) -> #t
(define equal?
  (lambda (s1 s2)
    (cond
      ((and (atom? s1) (atom? s2))
       (eq? s1 s2))
      ((or (atom? s1) (atom? s2)) #f)
      (else (equal? s1 s2)))))

;(eqlist3?
;  '(beef ((sausage)) (and (soda)))
;  '(beef ((salami)) (and (soda)))) -> #f
(define eqlist3?
  (lambda (l1 l2)
    (cond
      ((and (null? l1) (null? l2)) #t)
      ((or (null? l1) (null? l2)) #f)
      (else
        (and (equal? (car l1) (car l2))
             (equal? (cdr l1) (cdr l2)))))))
{{< /highlight >}}

After defining `(eqlist?)` to compare the equality of two lists, we can further
improve `(rember)` to remove **lists** as argument, not just removing atoms like in the
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

This chapter establish rules to write a good functions `(1) the first thing is to write ALL the operations
for every predicate condition; (2) make sure the algorith is correct; (3) then to simplify it.`


## Chapter 6 Shadows {#chapter-6-shadows}

---

An arithmetic expression (aexp) is is either an atom or two arithmetic
expression combined by o+, o\* , ^. `(numbered? argument)` return #f if the
argument contains anything other than numbers and o+, o\* , ^.

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

;if we are only allowed to input numeric expression, we can simplify as:
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

---

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

For sets, we can define some primary functions: `(subset? argument1 argument2)`
checks whether argument1 is a subset of argument2. `(eqset?` and `(intersect?)` check for
equality and intersection respectively. Comparing the three algorithms, we can
find how the different mathmatical functions can be achieved by manipulating the logical tools.

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

The below functions do a bit more, they return intersection or union or difference results as
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

;(xxx '(a b c) '(a b d e f)) -> '(c)
(define xxx
  (lambda (set1 set2)
    (cond
      ((null? set1) '())
      ((member? (car set1) set2)
       (xxx (cdr set1) set2))
      (else
        (cons (car set1) (xxx (cdr set1) set2))))))

;(intersectall '((a b c) (c a d e) (e f g h a b))) -> '(a)
(define intersectall
  (lambda (l-set)
    (cond
      ((null? (cdr l-set)) (car l-set))
      (else
        (intersect (car l-set) (intersectall (cdr l-set)))))))
{{< /highlight >}}

The **pair** is a list with only TWO s-expressions and the **rel** is a set of
pairs. We use `(set?)` and `(firsts)` to define `(fun?)`, which is used to test
whether `(firsts argument)` is a set.(i.e. whether a list consisting of all first
element of first-level sub-expressions contains duplicated atom).

{{< highlight scheme >}}
(define set?
      (lambda (lat)
        (cond
          ((null? lat) #t)
          ((member? (car lat) (cdr lat)) #f)
          (else
            (set? (cdr lat)))))

(define firsts
  (lambda (l)
    (cond
      ((null? l) '())
      (else
        (cons (car (car l)) (firsts (cdr l))))))

;(fun? '((4 3) (4 2) (7 6) (6 2) (3 4))) -> #f
;(fun? '((8 3) (4 2) (7 6) (6 2) (3 4))) -> #t
(define fun?
  (lambda (rel)
    (set? (firsts rel))))
{{< /highlight >}}

A list of pairs in which no **first** element of any pair is the same as any other
**first** element, is call a **finite function**; a list of pairs in which no **second**
element of any pair is the same as any other **second** element, is call a
**fullfun**.

Think about how these two conceptions are connected. For example, we introduce
`(revrel)` to switch each atoms for every pair within a list.

{{< highlight scheme >}}
;(revrel '((8 a) (pumpkin pie) (got sick))) -> '((a 8) (pie pumpkin) (sick got))
(define revrel
  (lambda (rel)
    (cond
      ((null? rel) '())
      (else (cons (build (second (car rel))
                         (first (car rel)))
                  (revrel (cdr rel)))))))

;introducing revpair to simplify revrel
(define revpair
  (lambda (p)
    (build (second p) (first p))))

(define revrel
  (lambda (rel)
    (cond
      ((null? rel) '())
      (else (cons (revpair (car rel)) (revrel (cdr rel)))))))
{{< /highlight >}}

Naturally, we could define `(seconds)` as we define `(firsts)` to develop
`(fullfun?)` for detecting duplicated second atom.

{{< highlight scheme >}}
(define seconds
  (lambda (l)
    (cond
      ((null? l) '())
      (else
        (cons (second (car l)) (seconds (cdr l)))))))

;(fullfun? '((8 3) (4 2) (7 6) (6 2) (3 4))) -> #f
;(fullfun? '((8 3) (4 8) (7 6) (6 2) (3 4))) -> #t
(define fullfun?
  (lambda (fun)
    (set? (seconds fun))))
{{< /highlight >}}

But, with the help of `(revrel)`, we can define `(fullfun?)` in a better way.
Let's call it `(one-to-one?)`:

{{< highlight scheme >}}
;(one-to-one? '((chocolate chip) (doughy cookie))) -> #t
(define one-to-one?
  (lambda (fun)
    (fun? (revrel fun))))
{{< /highlight >}}


## Chapter 8 Lambda and the Ultimate {#chapter-8-lambda-and-the-ultimate}

In the previous chapters, we've seen over and over that a function takes **list
or atom** as input, and produces **list or atom** as output.In this chapter, we
will see a function takes input and returns **functions**.

Function 1 is a function which takes **a** as argument and returns the function 2 (an
equality checking function) as output.

{{< highlight scheme >}}
;function 1:
(lambda (a)
 lambda (x)
  (eq? x a)))
;function 2:
(lambda (x)
 (eq? x a))
{{< /highlight >}}

It's called currying. Let's apply it with a familiar function:

{{< highlight scheme >}}
;((rember-f eq?) 2 '(1 2 3 4 5)) -> '(1 3 4 5)
(define rember-f
  (lambda (test?)
    (lambda (a l)
      (cond
        ((null? l) '())
        ((test? (car l) a) (cdr l))
        (else
          (cons (car l) ((rember-f test?) a (cdr l))))))))

(define test?
 (lambda (equal? eqan? eqlist? eqpair?)))
{{< /highlight >}}

The test? is an equivalent of a in the first example, i.e. a function working as
an argument input in function rember-f. It returns a member-removing function
as result. We want to do this, because we find that in rember functions, whether
we want to remove atom or list or pair or list, the only different part is the
equality checking. It's natural that we want to isolate the variant part and
write an invariant part.

The compound function = invariant functions (contains an abstract place
holder) + variant function. This allows us to extend more functions efficiently.
There are lots of commonly used building blocks in developing algorithms and with currying, we get to write much less repetitive code.

For example, when defining `(insertL)` and `(insertR)`, we notice that the only
difference is the order we `cons` the _new_ and _old_ argument, which can be
isolated as another two small variant functions, and then combined to a main,
invariant function:

{{< highlight scheme >}}
;variant functions
(define seqL
  (lambda (new old l)
      (cons new (cons old l))))

(define seqR
  (lambda (new old l)
      (cons old (cons new l))))

;invariant function containing seq as an abstract place holder
(define insert-g
  (lambda (seq)
      (lambda (new old l)
        (cond
          ((null? l) '())
          ((eq? (car l) old)
           (seq new old (cdr l)))
          (else
            (cons (car l) ((insert-g seq) new old (cdr l))))))))

;compound function
(define insertL (insert-g seqL))

(define insertR (insert-g seqR))
{{< /highlight >}}

The substitution function only differs in the same position, so it can be
rewritten as:

{{< highlight scheme >}}
;recap subst
(define subst
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((eq? (car l) old)
       (cons new (cdr l)))
      (else
        (cons (car l) (subst new old (cdr l)))))))

;rewrite invariant function
(define subst-f
 (lambda (seq)
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((eq? (car l) old)
       (seq new old cdr(l)))
      (else
        (cons (car l) (subst-f new old (cdr l)))))))

(define seqS
  (lambda (new old l)
    (cons new l)))

;of course the subst-f is identical to insert-g, so we write as:
(define subst (insert-g seqS))
{{< /highlight >}}

The `(rember)` function can be abstracted by `(insert-g)` too, but using variant
function requires extra tweak, since the `(rember)` doesn't use arguments _new_:

{{< highlight scheme >}}
;invariant function with seqrem as place holder
(define yyy
  (lambda (a l)
    ((insert-g seqrem) #f a l)))

;variant function
(define seqrem
  (lambda (new old l)
    l))

;(yyy 'sausage '(pizza with sausage and bacon)) -> '(pizza with and bacon)
{{< /highlight >}}

Let's see a function with more and more isolated parts to decrease repetitive
work in writting functions. In the `(value)`, we've used 1st-sub-ex and 2nd-sub-exp to write
less _car_ and _cdr_,

{{< highlight scheme >}}
;value uses 1st-sub-exp
(define 1st-sub-exp
  (lambda (aexp)
    (car (cdr aexp))))

;value uses 2nd-sub-exp
(define 2nd-sub-exp
  (lambda (aexp)
    (car (cdr (cdr aexp)))))

;half abstracted function
(define value
  (lambda (nexp)
    (cond
      ((atom? nexp) nexp)
      ((eq? (operator nexp) 'o+)
       (+ (value-prefix (1st-sub-exp nexp))
          (value (2nd-sub-exp nexp))))
      ((eq? (car nexp) 'o*)
       (* (value (1st-sub-exp nexp))
          (value (2nd-sub-exp nexp))))
      ((eq? (car nexp) 'o^)
       (expt (value (1st-sub-exp nexp))
             (value (2nd-sub-exp nexp))))
      (else #f))))

;keep abstract it with what we learned in this chapter
(define atom-to-function
  (lambda (atom)
    (cond
      ((eq? atom 'o+) +)
      ((eq? atom 'o*) *)
      ((eq? atom 'o^) expt)
      (else #f))))

;atom-to-function uses operator
(define operator
  (lambda (aexp)
    (car aexp)))

;(value '(o+ 1 (o^ 3 4))) -> 82
(define value
  (lambda (nexp)
    (cond
      ((atom? nexp) nexp)
      (else
        ((atom-to-function (operator nexp))
         (value (1st-sub-exp nexp))
         (value (2nd-sub-exp nexp)))))))
{{< /highlight >}}
