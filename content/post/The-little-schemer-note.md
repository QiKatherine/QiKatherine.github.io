+++
title = "The Little Schemer speedy referring note (1/3)"
date = 2019-12-10T23:20:00+00:00
lastmod = 2020-05-14T21:09:20+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

The Little Schemer uses a very easy way, introducing the nature of recursion,
continuation and Y combinator with only several simple building blocks. You might
have been so used to writing and calling functions with formal name, this book
will show you how complex procedure can be reformed by lambda expressions.

This is a quick reference note that I pull from the book The Little Schemer. The
full detailed code can be found in this repo:

[the-little-schemer/02-do-it-again.ss at master · pkrumins/the-little-schemer](https://github.com/pkrumins/the-little-schemer/blob/master/02-do-it-again.ss)

My recommendation of reading this book is that don't skip too much questions
after the fifth chapter, as the functions getting more complex, you will find those
questions from the dialogue are carefully asked, in order to guide us to gradually write a better program. Meanwhile try to write functions with pens and papers before checking the
answer, and often review them with quick scan of this article. I really enjoy this book, and hope you have much fun as I do :).

From some point, you probably need to read "Fibonacci times" in every chapter to fully
understand but keep going, you won't regret it.


## Chapter 1 Toy {#chapter-1-toy}

In this chapter, we list primitive conceptions and functions which will be
used through the entire book:

---

**atom** is the smallest element, something NOT enclosed by pair of
parenthesis: a string of digits/characters/numbers.

**list** is something enclosed by pair of parenthesis: something can be nothing, can be
atom, can be another list.

**s-expression** can be an atom, or an expression of the form (x y) where x and
y are s-expressions. It's essentially a binary tree.

`(car argument)`: returns first s-expression among all the first-level
 s-expression within a list. It can NOT work on empty list.

`(cdr argument)`: returns the complement set of `(car argument)`, including
the parenthesis. It can return empty list but can NOT work on non-list input.

`car/cdr` both take and output non-empty list. `cdr` cannot work on null list or
atom. For `(han), han, ()` cdr can only work on the first.

`(cons argument1 argument2)`: add arg1 (s-exp) onto arg2 (list), the output is
 list.

`(null? argument)`: returns T when the argument(list) is `(quote()), '() and ()` returns
 error when argument is `atom`; returns F for others.

`(atom? argument)`: checks if the argument (any s-expression) is atom.

`(eq? argument1 argument2)`: returns T when arguments (atoms) are equal; returns
 error when arguments are numerical or list; returns F for others.

`(or argument1 argument2 ...)` checks predicate arguments one by one. It terminates
whenever the first T is found and returns T, otherwise it returns F.

`(and argument1 argument2 ...)` checks predicate arguments one by one. It terminates
whenever the first F is found and returns F, otherwise it returns T.


## Chapter 2 Do It Again and Again {#chapter-2-do-it-again-and-again}

From chapter 2, we begin to build functions with primitive building blocks.

---

`(lat? argument)`: checks if every s-expression in a list is atom. Since the
 s-expression is either atom or list/pair, `(lat?)` use `(atom?)` as core function.

{{< highlight scheme >}}
;define atom? as primitive function first:
  ;define atom? as primitive function first:
  (define atom?
   (lambda (x)
      (and (not (pair? x)) (not (null? x)))))

  ; (lat? (Jack Sprat could eat no chicken fat)) -> #t, for every element is atom.
  (define lat?
    (lambda (l)
      (cond
        ((null? l) #t)
        ((atom? (car l)) (lat? (cdr l)))
        (else #f))))
{{< /highlight >}}

 `(member? argument1 argument2)`: checks if the argument1 (an atom) is in
the argument2 (a non-empty list).

{{< highlight scheme >}}
; (member? meat (mashed potatoes and meat gravy)) -> #t, for meat is in the list.
  ; (member? meat (mashed potatoes and meat gravy)) -> #t, for meat is in the list.
  (define member?
    (lambda (a lat)
      (cond
        ((null? lat) #f)
        (else (or (eq? (car lat) a)
                  (member? a (cdr lat)))))))
{{< /highlight >}}


## Chapter 3 Cons the Magnificent {#chapter-3-cons-the-magnificent}

---

 `(rember argument1 argument2)`: removes the first occurrence of the argument1 (an atom) from
the argument2 (a non-empty list).

{{< highlight scheme >}}
; (rember cup (coffee cup tea cup and hick cup)) -> (coffee tea cup and hick cup)
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
lists. The function returns a list consists of all the first s-expressions within
the first level argument lists.

{{< highlight scheme >}}
; (firsts ((five plums) (four) (eleven green oranges))) -> (five four eleven)
  ; (firsts ((five plums) (four) (eleven green oranges))) -> (five four eleven)
  (define firsts
    (lambda (l)
      (cond
        ((null? l) '())
        (else
          (cons (car (car l)) (firsts (cdr l)))))))
{{< /highlight >}}

`(insetR new old lat)` and `(insetL new old lat)`: insert the _new_ atom at the
RIGHT/LEFT side of the _old_ atom in lat (a list).

{{< highlight scheme >}}
; (insertR topping fudge (ice cream with fudge for dessert)) -> (ice cream with fudge topping for dessert)
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

`(subst new old lat)`: in lat (a list), this function replaces old atom with new
atom.
`(subst2 new o1 o2 lat)`: in lat (a list), this function checks o1 o2, whichever
occurs firstly is replaced by new atom.

{{< highlight scheme >}}
; (subst topping fudge (ice cream with fudge for dessert)) -> (ice cream with topping for dessert)
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

From now on, we use recursion more than once, in different predicates to achieve
multiple assignments, or more complicated assignments. For example, putting a
recursion in `(eq?)` predicate, in `(rember)` function enables us to go deeper,
removing multiple occurred atoms.
`(multirember a lat)`: removes all the occurrences of a in lat (a list).
`(multiinsertR new old lat)` and `(multiinsertL new old lat)`: insert new atom at the
RIGHT/LEFT side of old atom for EVERY occurrence of old in lat (a list).

{{< highlight scheme >}}
; (multirember cup (coffee cup tea cup and hick cup)) -> (coffee tea and hick)
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
of old atom in lat (a list).

{{< highlight scheme >}}
; (multisubst x a (a b c d e a a b)) -> (x b c d e x x b)
  ; (multisubst x a (a b c d e a a b)) -> (x b c d e x x b)
  define multisubst
    (lambda (new old lat)
      (cond
        ((null? lat) '())
        ((eq? (car lat) old)
         (cons new (multisubst new old (cdr lat))))
        (else
          (cons (car lat) (multisubst new old (cdr lat)))))))
{{< /highlight >}}

The multi operation is generally better designed. It can work with both single
and multiple occurrences of old atoms, and terminate at until it's got null
list. But the single operations terminate right when the first `(eq?)` returns
T. Generally applicable termination should be at finishing the last element.


## Chapter 4 Number Games {#chapter-4-number-games}

To start, we only consider _whole_ and _positive_ number. We are going to
firstly build increment function `(add1 argument)`, and decrement function
`(sub1 argument)`; then using them as fundamental blocks, we build addition
`(o+)` and subtraction `(o-)`; again using addtion as building block, we build multiplication.
It can be sensed that these recursive paradigm is exactly how we establish all more
calculation algorithms.

{{< highlight scheme >}}
;(add1 67) -> 68
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

  ;(o* 5 3) -> 15
  (define o*
    (lambda (n m)
      (cond
        ((zero? m) 0)
        (else (o+ n (o* n (sub1 m)))))))
{{< /highlight >}}

Next we introduce new class tuple (tup). **tup** is either an empty list, or it contains a number and a rest that is also a **tup**.

Using tup as building block to create extent function is just as using list before.
To enable natural termination on a list we use `(null? list)` and on a number we
use `(zero? 0)`. To enable the natural termination on a tup we use `(null? tup)`.

To enable natural recursion on a list we use `(cdr argument)`; the natural recursion on a
tup we use `(cdr argument)`; the natural recursion on a number we use `(sub1
argument)`. These condition is reused as new argument in inner recursion as
stated in The Fourth Commandment.

For tup, we develop two functions for fun:

{{< highlight scheme >}}
;(addtup (3 5 2 8)) -> 18
  ;(addtup (3 5 2 8)) -> 18
  (define addtup
    (lambda (tup)
      (cond
        ((null? tup) 0)
        (else (o+ (car tup) (addtup (cdr tup)))))))

  ;(tup+ (3 6 9 11 4) (8 5 2 0 7)) -> (11 11 11 11 11)
  (define tup+
    (lambda (tup1 tup2)
      (cond
        ((null? tup1) tup2)
        ((null? tup2) tup1)
        (else
          (cons (o+ (car tup1) (car tup2))
                (tup+ (cdr tup1) (cdr tup2)))))))
{{< /highlight >}}

The second case shows case need more than one terminal conditions. Using `(and
(null? tup1) (null? tup2))` is wrong when tup1 and tup2 have different length.
In such case, we use multiple terminal conditions and it works not only to finish the recursion,
but also to delivery the key result.

Back to the number game, we continuous use the foundamental blocks to creat:

{{< highlight scheme >}}
;(o> 12 133) -> #f
  ;(o> 12 133) -> #f
  (define o>
    (lambda (n m)
      (cond
        ((zero? n) #f)
        ((zero? m) #t)
        (else
          (o> (sub1 n) (sub1 m))))))

  ;(o< 4 6) -> #t
  (define o<
    (lambda (n m)
      (cond
        ((zero? m) #f)
        ((zero? n) #t)
        (else
          (o< (sub1 n) (sub1 m))))))

  ;(o= 5 5) -> #t
  (define o=
    (lambda (n m)
      (cond
        ((o> n m) #f)
        ((o< n m) #f)
        (else #t))))

  ;(o^ 2 3) -> 8
  (define o^
    (lambda (n m)
      (cond
        ((zero? m) 1)
        (else (o* n (o^ n (sub1 m)))))))

  ;(o/ 15 4) -> 3
  (define o/
    (lambda (n m)
      (cond
        ((o< n m) 0)
        (else (add1 (o/ (o- n m) m))))))
{{< /highlight >}}

In the `o> and o<` we can see again that the terminating conditions not only
terminate recursions,
but also work in a carefully arranged order, to deliver the right results for
designated function.

{{< highlight scheme >}}
;(olength (hotdogs with mustard sauerkraut and pickles)) -> 6
  ;(olength (hotdogs with mustard sauerkraut and pickles)) -> 6
  (define olength
    (lambda (lat)
      (cond
        ((null? lat) 0)
        (else (add1 (olength (cdr lat)))))))

  ;(pick 4 (lasagna spaghetti ravioli macaroni meatball)) -> macaroni
  (define pick
    (lambda (n lat)
      (cond
        ((zero? (sub1 n)) (car lat))
        (else
          (pick (sub1 n) (cdr lat))))))

  ;(rempick 3 (hotdogs with hot mustard)) -> (hotdogs with mustard)
  (define rempick
    (lambda (n lat)
      (cond
        ((zero? (sub1 n)) (cdr lat))
        (else
          (cons (car lat) (rempick (sub1 n) (cdr lat)))))))
{{< /highlight >}}

and number

{{< highlight scheme >}}
;(no-nums '(5 pears 6 prunes 9 dates)) -> (pears prunes dates)
  ;(no-nums '(5 pears 6 prunes 9 dates)) -> (pears prunes dates)
  (define no-nums
      (lambda (lat)
        (cond
          ((null? lat) '())
          ((number? (car lat)) (no-nums (cdr lat)))
          (else
            (cons (car lat) (no-nums (cdr lat)))))))

  ;(all-nums '(5 pears 6 prunes 9 dates)) -> (5 6 9)
    (define all-nums
      (lambda (lat)
        (cond
          ((null? lat) '())
          ((number? (car lat)) (cons (car lat) (all-nums (cdr lat))))
          (else
            (all-nums (cdr lat))))))

  ;(eqan? 'a 'a) -> #t
    (define eqan?
      (lambda (a1 a2)
        (cond
          ((and (number? a1) (number? a2)) (= a1 a2))
          ((or  (number? a1) (number? a2)) #f)
          (else
            (eq? a1 a2)))))

  ;(occur 'x '(a b x x c d x)) -> 3
    (define occur
      (lambda (a lat)
        (cond
          ((null? lat) 0)
          ((eq? (car lat) a)
           (add1 (occur a (cdr lat))))
          (else
            (occur a (cdr lat))))))

  ;  (one? 5) -> #f
  (define one?
      (lambda (n) (= n 1)))

  ;(rempick-one 4 '(hotdogs with hot mustard)) -> '(hotdogs with mustard)
  (define rempick-one
      (lambda (n lat)
        (cond
          ((one? n) (cdr lat))
          (else
            (cons (car lat) (rempick-one (sub1 n) (cdr lat)))))))
{{< /highlight >}}


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
  ;(rember '(foo (bar (baz))) '(apples (foo (bar (baz))) oranges)) -> '(apples oranges)
  (define rember
    (lambda (s l)
      (cond
        ((null? l) '())
        ((equal2?? (car l) s) (cdr l))
        (else (cons (car l) (rember s (cdr l)))))))
{{< /highlight >}}

This chapter establish rules to write a good functions `(1) the first thing is to write ALL the operations
for every predicate condition; (2) make sure the algorithm is correct; (3) then to simplify it.`


## Chapter 6 Shadows {#chapter-6-shadows}

---

An arithmetic expression (aexp) is either an atom or two arithmetic
expression combined by o+, o\* , ^. `(numbered? argument)` return #f if the
argument contains anything other than numbers and o+, o\* , ^.

{{< highlight scheme >}}
;(numbered? '(5 ox (3 'foo 2))) -> #f
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
  ;(1 + 1) is written as:
   ((eq? (car (cdr nexp)) 'o+)

  ;(+ 1 1) is written as:
   ((eq? (car nexp) 'o+)
{{< /highlight >}}

So the `(value)` can be written in two forms:

{{< highlight scheme >}}
;(value '(1 o+ (3 o^ 4))) ->82
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
setting functions**, we know this work will not be limited to `(1 + 1) and (+1
1)`. Actually, the syntactic/symbolic expression can be in any form, such as: if
0 is written as `quote()`, then 4 can be written as `(() () () ()) or
((((()))))`. Accordingly, the numberic operations can be written as:

{{< highlight scheme >}}
; sero? just like zero?
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
