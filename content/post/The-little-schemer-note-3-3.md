+++
title = "The Little Schemer speedy referring note (3/3)"
lastmod = 2020-05-11T02:29:22+01:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

The former chapters can be easily understood from reading the code without
counting parenthesis. **However from this chapter, it is highly recommended to
download Drracket and use a stepper to run all the recurions.** For example, the
stepper make the answer of `soegaard` very straightforward:

[scheme - Y combinator discussion in "The Little Schemer" - Stack Overflow](https://stackoverflow.com/questions/10499514/y-combinator-discussion-in-the-little-schemer?noredirect=1&lq=1)

This chapter introduces the idea of Y combinator based on recursion. We've seen
that recursion is a function calling itself during defining itself, but when the
function is just an lambda expression without name, what do we do?

The Y combinator provides a solution by designing an high order function, which
is a function that takes a function as an argument and returns a function.
Taking factorial as an example, we deduce a function G where G(factorial)=factorial.
Let's learn how to deduce step by step.


## Chapter 9 and Again and Again and Again {#chapter-9-and-again-and-again-and-again}

The key of writing recursion is making sure there is `termination condition`.
That's the basic requirement for function in both computation and mathmatics
area: a function is a mapping procedure which takes in an argument and produces
an output accordingly. We need to aviod any algorithm leading to infinite loop.

Here is an example of failed design: the `(keep-looking)` calls `(pick)` to see if `a` is equal to the random atom in lat (assuming the numbers in lat is random).

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

In the first test case we can find that, the `'caviar` is the 4th element in the
first example, and the list contains 4. So running it is very likely to hit the
termination condition. But in second example the `'caviar` is the 4th element whereas no 4
is contained in the list, so the recursion will run forever, meaning the
function won't always return a value for an input. This illed function is called
**partial function** as opposed to **total function** defined previously.

Let's see another example. We've defined **pair** is a list containing two
s-expressions (s-expression: a binary tree). The `shift` takes a pair whose first component is a pair and builds a pair by shifting the second part of the first component into
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
       (align (shift pora))) ;******alarming
      (else (build (first pora)
              (align (second pora)))))))
{{< /highlight >}}

Based on `(shift)` we further creat `(align)`. Don't rush to run the function. Remember the seventh commandment emphasizes "**recursion should happen on the subparts that are of the same nature:
 either on the sublists of a list; or on the subexpressions of an arithmetic
 expression**". We  notice something alarming in the starred line in `(align)`:
 the `(align)` as well as `(keep-looking)` both creat new argument in recursion
 that is **not part** of the original argument. It's an indicator of ill, but
 `(align)` will still generate output for every input, so it's not  partial function.

We will continue and define a very similar function `(shuffle)` below, which is partial. It won't
produce value for some cases, since the `a-pair` predicate will always swap the items of pair, which
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
start to think if possible to develop a diagnose function to detect the partial function. Let's make
up a function `(will-stop?)` without getting into detail. We want it to **return
\#t if the argument stops when applied to null input, and return #f it does not
stop when inputting null input**. And
itself has to be a total function. Then we try compounding it with `(length)` and `(eternity)` respectively.

{{< highlight scheme >}}
(define will-stop?
 (lambda (f))
...)

(define length
  (lambda (lat)
    (cond
      ((null? lat) 0)
      (else (add1 (length (cdr lat)))))

(define will-stop?
  (lambda (x)
    (length x)))

(define eternity
  (lambda (x)
    (eternity x)))

(define will-stop?
  (lambda (x)
    (eternity x)))
{{< /highlight >}}

The `(length)` stops when the input is `'()`, so the `(will-stop?)` returns #t.
But the `(eternity)` is partial and won't stop for any input, which makes the
`(will-stop?)` returns #f whatsoever. This gives us a gist of what function we want
to build. Let's see another tool function:

{{< highlight scheme >}}
(define last-try
 (lambda (x)
  (and (will-stop? last-try)
    (eternity x))))
{{< /highlight >}}

In order to test if ths is a right function, we input `()` and that requires
evaluate `(will-stop? last-try)`. Provided it returns #f (aka the last-try will
not stop with null input), then the whole function will stop and return #f.
Clearly this mother function `(last-try (quote()))` stops for null input, which
goes against the aka part in parenthesis. So we try the opposite hypothesis: it
returns #t (aka the last-try will stop with null input), and then we get to
evaluate `(eternity (quote()))`, which never stops. And this time, it logically
goes against the hypothesis again. This makes the `(will-stop?)` a
function we can describe but can not define. So be careful when using the recursion.

---

From now on, we start to learn to remove `(define function-name)` and use lambda
expression to directly refer to a function. This allows us to save time when we
want to quickly define something without permanently store it in official
environment.

To start, try understand the below two functions are identical, and try writing a
named funtion in lambda expression yourself.

{{< highlight scheme >}}
(define length
 (lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1 (length (cdr l)))))))

(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1 (length (cdr l))))))
{{< /highlight >}}

It's absolutely normal to get confused when there are more layers of lambda expressions involved in a
function/recursion. It helps to think whether the lambda expressions is being
merely defined or being defined and called, i.e. counting the parenthesis very
carefully. The difference between defining and calling is that calling a function has arguments involved:

{{< highlight scheme >}}
;defining
(lambda (f)
  (lambda (g)...)
)

;calling(f) with defining(g)
((lambda (f)
  (lambda (g)...))
    arguments-for-f)
{{< /highlight >}}

A more general case of calling with defining in lambda expression is called the
omega combinator. It has shape in the below picture and more information can be
found at [Lambda calculus - Wikipedia](https://en.wikipedia.org/wiki/Lambda%5Fcalculus#Standard%5Fterms)
![](/img/little2.png)

As you read more, you will understand
the below example is carefully selected: it achieves a simple job at a time; applying
it on itself is achieving a simple job on a achieved job; when repeating calling itself,
it can achieve a almost infinite loop.

In addition, the function change `(length)` a little by calling `(eternity)`
instead of `(length)` in its recursion. This makes the function only works on **null input**, since it will terminate in the `(null?)` predicate without calling the partial
`(eternity)`, otherwise any non-null input will trigger the infinite
recursion and cause failure by giving no answer. This is essentially how the `(length<=0)` can **only** determine the length of the empty list. With it, we can further develop an `(length<=1)` which measures the length of list
with less than one element:

{{< highlight scheme >}}
;length<=0
(lambda (l)
    (cond
      ((null? l) 0)
      (else
        (add1 (eternity (cdr l))))))

;length<=1
(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1 (length0 (cdr l))))))

;length<=1
(lambda (l)  ;read more details below if you don't understand here
  (cond
    ((null? l) 0)
    (else
      (add1
        ((lambda(l)
           (cond
             ((null? l) 0)
             (else
               (add1 (eternity (cdr l))))))
         (cdr l))))))
{{< /highlight >}}

{{< figure src="/img/length1.png" >}}

Recursively we can develop `(length<=2)` below. Notice these
three functions are identical. (here we start dumping `(define ...)`, referring the
length0 and length1 are there only for demonstration purpose).

{{< highlight scheme >}}
;length<=2
(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1 (length1 (cdr l))))))

(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1
        ((lambda(l)
           (cond
             ((null? l) 0)
             (else
               (add1 (length0 (cdr l))))))
         (cdr l))))))

(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1
        ((lambda(l)
           (cond
             ((null? l) 0)
             (else
               (add1
                ((lambda(l)
                 (cond
                   ((null? l) 0)
                     (else
                       (add1 (eternity (cdr l))))))
               (cdr l))))))
      (cdr l))))))

;let's give distinguished names to arguments in every layer
(lambda (l2)  ;assume l2 = '(b c)
  (cond
    ((null? l2) 0)
    (else
      (add1
        ((lambda(l1)  ;then l1 <- cdr(l2) = '(c)
           (cond
             ((null? l1) 0)
             (else
               (add1
                ((lambda(l0)  ;then l0 <- cdr(l1) = '( )
                 (cond
                   ((null? l0) 0)  ;so here returns 0, and terminates
                     (else
                       (add1 (eternity (cdr l0))))))
               (cdr l1))))))
      (cdr l2))))))
{{< /highlight >}}

The above functions show repetitive content, aka the `(length)` part is being
called over and over, working on a shorter and shorter argument. Normally, we would write and save as a named
function for calling in the future. **But, if we don't save it, instead we want to directly
address it within other function, or even address itself. How do we do that?**
You may have realized the motivation of this question, addressing itself withing itself
is exactly the nature of recursion.

If we can define length abstractly, we can call it to simplify the reptitive procedure.
This need is particularly necessary when there is going to be many algorithms
having similar repetitions as `(length<=n)`.

First we deal with the first question, separating `(eternity)` by calling it
from argument, see in length<=0. Then we deal the second question, separating
length0 as "g calling eternity", addressing it through f.

{{< highlight scheme >}}
;length<=0
((lambda (length)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else (add1 (length (cdr l)))))))
 eternity)

;length<=1
((lambda (f)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else (add1 (f (cdr l)))))))
 ((lambda (g)
    (lambda (l)
      (cond
        ((null? l) 0)
        (else (add1 (g (cdr l)))))))
  eternity))

;length<=2
((lambda (length)
  (lambda(l)
   (cond)
       ((null? l) 0)
       (else (add1 (length (cdr l)))))))
 ((lambda (length)
    (lambda (l)
      (cond
        ((null? l) 0)
        (else (add1 (length (cdr l)))))))
 ((lambda (length)
    (lambda (l)
      (cond
        ((null? l) 0)
        (else (add1 (length (cdr l)))))))
  eternity)))
{{< /highlight >}}

The repetitions decrease but not pithy enough, and we can further simplify it.
Since we observe that the length testing part is highly similar, therefore we
call it `(mk-length)`, the length functions can be therefore written as:

{{< highlight scheme >}}
;length<=0
((lambda (mk-length)
  (mk-length eternity))
 (lambda (length)
  (lambda(l)
   (cond
 ((null? l) 0)
  (else (add1 (length (cdr l))))))))

;length<=1
((lambda (mk-length)
 (mk-length
 (mk-length eternity)))
 (lambda (length)
  (lambda(l)
   (cond
 ((null? l) 0)
  (else (add1 (length (cdr l))))))))

;length<=2
((lambda (mk-length)
  (mk-length
   (mk-length
    (mk-length eternity))))
 (lambda (length)
  (lambda(l)
   (cond
 ((null? l) 0)
  (else (add1 (length (cdr l))))))))
{{< /highlight >}}

In the length 0, the actual working part is `((null? l) 0)` and the `(else)` predicate
was never really got invoked. So in that predicate it doesn't really matter if we call `(eternity)`, or
even itself `(mk-length)`. Therefore we try further abstract it with this thoughts:

{{< highlight scheme >}}
; length<=0
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (length)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else
         (add1 (length (cdr l))))))))

; since it doesn't really matter what to name the inner argument
; we rewrite length<=0
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else
         (add1 (mk-length (cdr l))))))))

;use length<=0 to achieve length<=1
(((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else
         (add1 ((mk-length eternity) (cdr l)))))))))
{{< /highlight >}}

The last part in above shows: since the `(eternity)` is now substituted by the
`(mk-length)`, we can add one more workable layer on `(length0)`. This is achievable by calling the inner most
function `(mk-length)` with another argument of `(eternity)`.

The exercise in page 166 will help you on how it works. The instruction can be
found in the answer of `soegaard` :
[scheme - Y combinator discussion in "The Little Schemer" - Stack Overflow](https://stackoverflow.com/questions/10499514/y-combinator-discussion-in-the-little-schemer?noredirect=1&lq=1)

When running it with stepper in DrRacket, there are 27 steps for a case `(l is
(' a b c))`, I only demonstrate 4 steps here (press ctrl and + to see the enlarged image)

![](/img/little.png)
You can try to play with longer list, such as this:

{{< highlight scheme >}}
(((lambda (mk-length)
    (mk-length mk-length))
  (lambda (mk-length)
    (lambda (l)
      (cond
        ((null? l) 0 )
        (else (add1
               ((mk-length mk-length)
                (cdr l))))))))
 '(a b c))
{{< /highlight >}}

You would realize that this is just more recurrences of that "bear in mind"
picture, aka calling `(mk-length mk-length)` one more time before applying a
shorter candidate list, until the `(cdr l)` runs out of atom. In the end, the null
list becomes the termination condition, without triggering it, we will go stack overflow by calling
`(mk-length mk-length)` infinitely.

After undertanding the above code, the author finanlly gets to reform this
procedure in to Y combinator, a function without defining name and so pithy that
can be called repetitively by itself as many times as we want.

If you find it confusing, read this preview of omega combinator in the first
answer of this post:[scheme - Y combinator discussion in "The Little Schemer" -
Stack Overflow](https://stackoverflow.com/questions/10499514/y-combinator-discussion-in-the-little-schemer/11864862#11864862)
