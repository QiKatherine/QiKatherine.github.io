+++
title = "The Little Schemer speedy referring note (2/3)"
date = 2019-12-23T01:35:00+00:00
lastmod = 2020-05-11T02:03:24+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)


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
or atom** as input and returns **list or atom** as output. In this chapter, we
will be learning how to write a function that takes input and returns
**functions**. Technically, a digit/atom is a function too, which takes itself as
argument and returns itself. Taking an naive example, the `(eq?-f)` is a
functional projection which takes `a` (a constant function) as argument and
returns `(eq?-a)` (equivelent in concept) as output. It's called currying.

{{< highlight scheme >}}
(define eq?-f)
 (lambda (a)
   (lambda (x)
    (eq? x a))))

(define eq?-a
 (lambda (x)
  (eq? x a))
{{< /highlight >}}

But the functional projection is sometimes more confusing when there are
recursions involved in multiple stages, especially we will no longer spercify
whether every input is an argument or an function. Therefore it will always be beneficial to ask yourself, which functions has been
previously defined, and how its default arguments are tweaked in developing the
current function.
Let's improve it a little with a familiar function:

{{< highlight scheme >}}
(define rember-f
  (lambda (test?)
    (lambda (a l)
      (cond
        ((null? l) '())
        ((test? (car l) a) (cdr l))
        (else
          (cons (car l) ((rember-f test?) a (cdr l))))))))

; the test? can be eq? equal? eqan? eqlist? eqpair?
; depending on which type of member you plan to remove.
;e.g. remove number: ((rember-f eq?) 2 '(1 2 3 4 5)) -> '(1 3 4 5)
{{< /highlight >}}

Notice that this is not a well defined function yet, since we have not
specify the mapping relations. But it can lead us think that `test?` is an equivalent of `a` in the first
example, i.e. a function `(test?)` works as an argument input in function
`(rember-f)`, which together returns a member-removing function as result. This
came because we can find that in `(rember)` function family, whether we want to remove atom
or number or pair or list, the only different part is the equality checking.

It's therefore natural that we want to deconstruct a compound function as
invariant functions + variant function, which allows us to extend more functions efficiently.
Because there are lots of commonly used building blocks in developing algorithms and with currying, we get to write much less repetitive code.

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

;firstly we define variant function
(define seqS
  (lambda (new old l)
    (cons new l)))

;rewrite invariant function accordingly
(define subst-f
 (lambda (seq)
  (lambda (new old l)
    (cond
      ((null? l) '())
      ((eq? (car l) old)
       (seq new old cdr(l)))
      (else
        (cons (car l) (subst-f new old (cdr l)))))))

;huh! the subst-f is identical to insert-g, so we can write as:
(define subst (insert-g seqS))
{{< /highlight >}}

The `(rember)` function can be achieved by `(insert-g)` too, but it requires
extra tweak, since the `(rember)` doesn't use arguments _new_. The `(seqrem)`
function replaces the old seq's job: it neither cons on left nor right, but only
retains the third argument, aka `(cdr l)`.

{{< highlight scheme >}}
;invariant function with seqrem as place holder
(define yyy
  (lambda (a l)
    ((insert-g seqrem) #f a l)))

(define insert-g
  (lambda (seq)
      (lambda (new old l)
        (cond
          ((null? l) '())
          ((eq? (car l) old)
           (seq new old (cdr l)))
          (else
            (cons (car l) ((insert-g seq) new old (cdr l))))))))

(define seqrem
  (lambda (new old l)
    l))
;(yyy 'sausage '(pizza with sausage and bacon)) -> '(pizza with and bacon)
{{< /highlight >}}

Let's see a function with more and more isolated parts to decrease repetitive
work in writting functions. In the `(value)`, we've used 1st-sub-ex and 2nd-sub-exp to write
less _car_ and _cdr_. Here we isolate two more parts `operator` to **locate** the
calculation operator and `atom-to-function` to **match and export** the
calculation operator. Notice that genetically the arguments are named as `aexp`
and `atom` inside the functions, but when they are called in `(value-f)`, the
arguments are tweaked with the arguments of `(value-f)`.

{{< highlight scheme >}}
;value uses 1st-sub-exp
(define 1st-sub-exp
  (lambda (aexp)
    (car (cdr aexp))))

;value uses 2nd-sub-exp
(define 2nd-sub-exp
  (lambda (aexp)
    (car (cdr (cdr aexp)))))

;atom-to-function uses operator
(define operator
  (lambda (aexp)
    (car aexp)))

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

;(value-f '(o+ 1 (o^ 3 4))) -> 82
(define value-f
  (lambda (nexp)
    (cond
      ((atom? nexp) nexp)
      (else
        ((atom-to-function (operator nexp))
         (value-f (1st-sub-exp nexp))
         (value-f (2nd-sub-exp nexp)))))))
{{< /highlight >}}

Here is another compound function containing more layers of abstractions, which
doesn't only call other functions, but also contains recursions on multiple conditions:

{{< highlight scheme >}}
(define multiremember&co
 (lambda (a lat col)
    (cond
      ((null? lat)
       (col '() '()))
      ((eq? (car lat) a)
       (multiremember&co a (cdr lat)
       (lambda (newlat seen)
         (col newlat (cons (car lat) seen)))))
      (else
        (multiremember&co a (cdr lat)
                          (lambda (newlat seen)
                            (col (cons (car lat) newlat) seen)))))))

(define a-friend
 (lambda (x y)
  (null? y)))
;(multiremember&co 'tuna '() a-friend) -> #t;
;(multiremember&co 'tuna '(tuna) a-friend) -> #f

;(multiremember&co 'tuna '(and tuna) a-friend) -> #f
;in the final recursion, it gets us:
;a=tuna
;lat='()
;col=
;((lambda (newlat1 seen1)
;   ((lambda (newlat2 seen2)
;      (list newlat2 (cons 'foo seen2)))
;    (cons 'bar newlat1)
;    seen1))
; '() '())

;define a different continuation
(define last-friend
 (lambda (x y)
  (length? x)))
;(multiremember&co 'tuna (strawberries tuna and swordfish) last-friend) -> 3
{{< /highlight >}}

The question has been also discussed on SO: [recursion - Explain the
continuation example on p.137 of The Little Schemer - Stack Overflow](https://stackoverflow.com/questions/7004636/explain-the-continuation-example-on-p-137-of-the-little-schemer)

{{< highlight scheme >}}
(define multiinsertLR
  (lambda (new oldL oldR lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) oldL)
       (cons new
             (cons oldL
                   (multiinsertLR new oldL oldR (cdr lat)))))
      ((eq? (car lat) oldR)
       (cons oldR
             (cons new
                   (multiinsertLR new oldL oldR (cdr lat)))))
      (else
        (cons
          (car lat)
          (multiinsertLR new oldL oldR (cdr lat)))))))

(define multiinsertLR&co
  (lambda (new oldL oldR lat col)
    (cond
      ((null? lat)
       (col '() 0 0))
      ((eq? (car lat) oldL)
       (multiinsertLR&co new oldL oldR (cdr lat)
                         (lambda (newlat L R)
                           (col (cons new (cons oldL newlat))
                                (+ 1 L) R))))
      ((eq? (car lat) oldR)
       (multiinsertLR&co new oldL oldR (cdr lat)
                         (lambda (newlat L R)
                           (col (cons oldR (cons new newlat))
                                L (+ 1 R)))))
      (else
        (multiinsertLR&co new oldL oldR (cdr lat)
                          (lambda (newlat L R)
                            (col (cons (car lat) newlat)
                                 L R)))))))
;some collectors
(define col1
  (lambda (lat L R)
    lat))
(define col2
  (lambda (lat L R)
    L))
(define col3
  (lambda (lat L R)
    R))

; Examples of multiinsertLR&co
(multiinsertLR&co 'salty 'fish 'chips '(chips and fish or fish and chips)  col1)
;-> '(chips salty and salty fish or salty fish and chips salty)
(multiinsertLR&co  'salty  'fish  'chips  '(chips and fish or fish and chips)  col2)
;-> 2
(multiinsertLR&co  'salty 'fish 'chips '(chips and fish or fish and chips) col3)
;-> 2
{{< /highlight >}}

{{< highlight scheme >}}
;(evens-only* '((9 1 2 8) 3 10 ((9 9) 7 6) 2)) -> '((2 8) 10 (() 6) 2)
(define evens-only*
  (lambda (l)
    (cond
      ((null? l) '())
      ((atom? (car l))
       (cond
         ((even? (car l))
          (cons (car l)
                (evens-only* (cdr l))))
         (else
           (evens-only* (cdr l)))))
      (else
        (cons (evens-only* (car l))
              (evens-only* (cdr l)))))))

;define *&co function
(define evens-only*&co
  (lambda (l col)
    (cond
      ((null? l)
       (col '() 1 0))
      ((atom? (car l))
       (cond
         ((even? (car l))
          (evens-only*&co (cdr l)
                          (lambda (newl p s)
                            (col (cons (car l) newl) (* (car l) p) s))))
         (else
           (evens-only*&co (cdr l)
                           (lambda (newl p s)
                             (col newl p (+ (car l) s)))))))
      (else
        (evens-only*&co (car l)
                        (lambda (al ap as)
                          (evens-only*&co (cdr l)
                                          (lambda (dl dp ds)
                                            (col (cons al dl)
                                                 (* ap dp)
                                                 (+ as ds))))))))))
(define evens-friend
  (lambda (e p s)
    e))

;(evens-only*&co '((9 1 2 8) 3 10 ((9 9) 7 6) 2) evens-friend)
; -> '((2 8) 10 (() 6) 2)
{{< /highlight >}}
