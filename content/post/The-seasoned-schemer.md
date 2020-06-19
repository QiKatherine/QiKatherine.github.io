+++
title = "The Seasoned Schemer learning note (1/3)"
date = 2020-06-14T01:13:00+01:00
lastmod = 2020-06-19T01:57:18+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

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


## Chapter 12 Take Cover {#chapter-12-take-cover}

In this chapter we reform previous function to seperate values.

Let's firstly
review Y combinator:

{{< highlight scheme >}}
;deducted in chapter 9 in The Little Schemer
(define Y
  (lambda (le)
    ((lambda (f) (f f))
     (lambda (f)
       (le (lambda (x) ((f f) x)))))))

; An example of using Y-combinator to measure length and applied to '(a b c)
((Y (lambda (length)
     (lambda (l)
       (cond
         ((null? l) 0)
         (else (add1 (length (cdr l)))))))) '(a b c))  ; produces output 3
{{< /highlight >}}

We can use Y combinator to rewrite `(multirember)` that removes multi-members from a list.

{{< highlight scheme >}}
;old friend multirember
(define multirember
  (lambda (a lat)
    (cond
      ((null? lat) '())
      ((eq? (car lat) a)
       (multirember a (cdr lat)))
      (else
        (cons (car lat) (multirember a (cdr lat)))))))

; No need to pass 'a' around in multirember
; Use Y-combinator not to pass it around
(define multirember
  (lambda (a lat)
    ((Y (lambda (mr)
          (lambda (lat)
            (cond
              ((null? lat) '())
              ((eq? a (car lat)) (mr (cdr lat)))
              (else
               (cons (car lat) (mr (cdr lat))))))))
     lat)))

(multirember 'a '(a b c a a a x)) ;-> '(b c x)
{{< /highlight >}}

We can see from the above that `(multirember)` has two arguments - _a_: the word we
want to remove; _lat_: the candidate list. _a_ is contant all the time but
_lat_ becomes shorter and shorter in every recursion. We re-write `(multirember)` in the second
definition by adding another layer to just passing the arguments. It is defining
with calling, which we called currying in The Little Schemer.

What is the benefit of doing this? It seperates the procedures of applying _a_
and _lat_, you would naturally ask, what if I use currying learned from The
Little Schemer to write it, maybe like this?

{{< highlight scheme >}}
(define mr
  (lambda (lat)
     (cond
       ((null? lat) '())
            ((eq? a (car lat)) (mr (cdr lat)))
              (else
               (cons (car lat) (mr (cdr lat)))))))

(define multirember
  (lambda (a lat)
    (mr lat)))
{{< /highlight >}}

When we apply it, we would notice problems, with step two result, instead of
finding `(mr)` function, it will substitute _a_ with _'c_ and _lat_ with _'(c d)_, which will report error
because we did not specify _a_ in `(multirember)`. The third part is procedure we actually want.

{{< highlight scheme >}}
;step 1
(multirember 'c '(c d))

;step 2
((lambda (a lat)
    (mr lat)) 'c '(c d)) ;-> error variable 'a is not defined

;what we actually want it
((lambda (a lat)
    ((lambda (lat)
     (cond
       ((null? lat) '())
            ((eq? a (car lat)) (mr (cdr lat)))
              (else
               (cons (car lat) (mr (cdr lat)))))))
 lat))
'c '(c d))
{{< /highlight >}}

How do we write function achieve the code we want? The difference between the
right and wrong function is holding the argument substitution until after applying
the `(mr)` function. Apart from the traditional way, we can use `(letre)` to define:

{{< highlight scheme >}}
(define multirember-letrec
  (lambda (a lat)
    ((letrec
       ((mr (lambda (lat)
              (cond
                ((null? lat) '())
                ((eq? a (car lat)) (mr (cdr lat)))
                (else
                  (cons (car lat) (mr (cdr lat))))))))
       mr)
     lat)))

(multirember-letrec 'apple '(apple bbb)) ;-> '(bbb)
{{< /highlight >}}

As the it says in the book, the implementation of `(lambda (a lat))` is
implementing `((letrec ((mr..)) mr) lat)`. It is the same as the implementation of old traditional `(multirember lat)`. We can put the
above code in DrRacket see exactly how `(letrec)` part is called:
![](/img/seasoned11.png)

Here are more information about the concept of `(letrec)`:

[3.9 Local Binding: let, let\*, letrec, ...](https://docs.racket-lang.org/reference/let.html)

[Teach Yourself Scheme in Fixnum Days](https://ds26gte.github.io/tyscheme/index-Z-H-8.html)

We slightly adding a bit syntax sugar, changing the `((letrec ((mr..)) mr) lat)` by moving the _lat_ inside as
`(letrec ((mr..)) (mr lat))`:

{{< highlight scheme >}}
(define multirember-letrec
  (lambda (a lat)
    (letrec
       ((mr (lambda (lat)
              (cond
                ((null? lat) '())
                ((eq? a (car lat)) (mr (cdr lat)))
                (else
                  (cons (car lat) (mr (cdr lat))))))))
       (mr lat))))

(multirember-letrec 'apple '(apple bbb))
{{< /highlight >}}

By this function we successfully achieve the function we want without error as
above. With the syntax sugar, the function is very easy to read: we are just
defining a local recursive function that knows the definition of _a_, then we implement the function
to list _lat_.
