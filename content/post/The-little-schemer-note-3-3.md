+++
title = "The Little Schemer speedy referring note (3/3)"
lastmod = 2020-01-10T23:45:17+00:00
categories = ["TECH"]
draft = false
image = "img/111.jpg"
+++

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
      (else (add1 (length (cdr lat)))))))

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

From now on, we start to get used to removing `define function-name)` and use lambda
expression to directly refer to a function. This allows us to save time when we
want to quickly define something without permanently store it in official environment. The below two functions are identical:

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

It's easy to get confused when there are more layers of lambda expressions involved in a
function/recursion.It's helpful to think whether the lambda expressions is being
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

The below function change `(length)` a little by calling `(eternity)`
instead of `(length)` in its recursion. This makes the function only works
on **null input**, since it will terminate in the `(null?)` predicate without calling the partial
`(eternity)`, otherwise any non-null input will trigger the infinite
recursion and cause failure by giving no answer. This is essentially how the `(length<=0)` can only determine the length of the empty list. With it, we can further develop an `(length<=1)` which measures the length of list
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
      (add1 (length0 (cdr l))))

;length<=1
(lambda (l)
  (cond
    ((null? l) 0)
    (else
      (add1
        ((lambda(l)
           (cond
             ((null? l) 0)
             (else
               (add1 (eternity (cdr l)))))); l=cdr(l) below
         (cdr l))))));the cdr(l) is for l in the above line.
{{< /highlight >}}

{{< figure src="/img/length1.png" >}}

Recursively we can develop `(length<=2)` below. Notice these
three functions are identical. (We've actually dumped `(define ...)`, the
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
{{< /highlight >}}

The above functions show repetitive content along with the length of input list,
mainly in calling `(length)` part, so we can write a function to capture the
pattern. If we can define length abstractly, we can call it to simplify the reptitive procedure.
This need is particularly necessary because one can imagine there is going to be many algorithms having similar repetitions as `(length<=n)`.

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
{{< /highlight >}}

The repetitions decrease by reducing the  but not pithy enough, we further simplify it:

{{< highlight scheme >}}
;define make length
(lambda (mk-length)
  (mk-length eternity))

; rewrite length<=1
((lambda (mk-length)
   (mk-length mk-length))
 (lambda (mk-length)
   (lambda (l)
     (cond
       ((null? l) 0)
       (else
         (add1
           ((mk-length eternity) (cdr l))))))))
{{< /highlight >}}
