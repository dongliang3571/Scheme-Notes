# Scheme-Notes

scheme cheatsheet http://www.nada.kth.se/kurser/su/DA2001/sudata16/examination/schemeCheatsheet.pdf

## NOTE: Applicative order evaluation(aka eager evaluation) and Normal order evaluation(lazy evaluation)
  ``` scheme
  (define (try a b)
    (if (= a 0) 1 b))
    
  ;Evaluating (try 0 (/ 1 0)) generates an error in Scheme. With lazy evaluation, 
  there would be no error. Evaluating the  expression would return 1, because the 
  argument (/ 1 0) would never be evaluated.
  
  ; Another example
  ; consider
  (define (p) (p))
  (define (test x y)
    (if (= x 0)
      0
      y))
  
  > (test 0 (p))
  ; give infinite loop, () will call p
  
  > (test 0 p)
  0
  ;give 0
  
  ; remark
  > (if #t 0 (p))
  0
  ; returns 0 -- because if is a special form, the infinite loop (p) is never evaluated
  ```

## Other built-in Functions
  ```scheme
  > (min 1 2 3)
  1
  > (max 1 2 3)
  3
  
  > (remainder 13 4)
  1
  > (remiander -13 4)
  -1
  
  > (modulo 13 4)
  1
  > (modulo -13 4)
  3
  ;To get some number mod 4, you add whatever integer multiple of 4 it takes to get a number between 0 and 3. For -13, that integer is 4 because -13 + 4 x 4 is between 0 and 3
  
  > (quotient -35 7)
  -5
  
  > (quotient 36 7)
  5
  ; quotient takes integers only
  
  > (gcd 32 4)
  4
  ; greatest common divisor
  
  > (magnitude -7)
  7
  > (magnitude 7)
  7
  ; absolute value
  
  > (= 2.0 2)
  #t
  ; compare only numbers, if want to compare string, use (eqv? string1 string2)
  
  > (eqv? "ab" "ab")
  #t
  ; compare
  
  > (zero? 0)
  #f
  ; test 0 value
  
  > (integer? 2)
  #t
  > (integer? 2.0)
  #t
  > (integer? 2.1)
  #f
  ; test integers
  
  > (or 1 0)
  1
  > (and 1 1)
  1
  > (not #f)
  #t
  
  > (negative? -1)
  #t
  > (positive? 1)
  #t
  
  ;;;;;;;;;;;;;;;;;;;;;;;;; List Functions ;;;;;;;;;;;;;;;;;;;
  
  > (quote (2 3))
  (2 3)
  > '(2 3)
  (2 3)
  ; both quote suspends evaluation of the list

  > (list 1 7 3)
  (1 7 3)
  > (length '(1 2 3))
  3
  > (list (list 7 8 2))
  3
  ; length of the list
  ```

## Newton's method for getting square root
1. want to compute square root of 2
2. guess a number, eg. 1
3. get Quotient of 2/1, which equals to 2
4. take the average of Quotient and the number guessed, (2 + 1)/2, which equals to 1.5
5. now repeat from step 2, results from step 4 will be the next number to be guessed
6. get Quotient of 2/1.5, which equals to 1.3333
7. take the average of Quotient and the number guessed, (1.3333 + 1.5)/2, which equals to 1.4167
8. now repeat from step 2..................


## 1.1.1 Expressions
- expressions are evaluated
- some expressions are primitive
- numbers are primitive expressions
  ```scheme
  > 2345
  2345
  > 12.01
  12.01
  ```
  
- another kind of expression is a primitive procedure
  ```scheme
  > +
  #<procedure:+>
  > (+ 2 3)
  5
  > (+ 2 3 4 5)
  14
  ```
  
- there are many other kinds of expressions, which we will mostly ignore for now, for example: `string-append`.

  ```scheme
  > (string-append "Hello" " world")
  "Hello world"
  ```
  
## 1.1.2 Naming
- we can name values, and then use them

  ```scheme
  > (define size 2)
  > (+ 5 size)
  7
  ```
  
## 1.1.3 Evaluating Combinations
1. evaluate the subexpressions of the combination
2. apply the procedure that is the value of the leftmost subexpression (the operator) to the arguments that are the values of the other subexpressions (the operands)

  ```scheme
  > (+ (* 3 (+ (* 2 4) (+ 3 5))) (+ (- 10 7) 6))
  57
  ```

## 1.1.4  Compound Procedures
- functions are values, too

  ```scheme
  > (lambda (x) (* x x))
  #<procedure>
  > ((lambda (x) (* x x)) 2)
  4
  
  > (define square
      (lambda (x) (* x x)))
  > (square 2)
  4
  ```
  
- perhaps more conveniently, but with some loss of clarity, we can omit 'lambda'
  ```scheme
  > (define (square x) 
      (* x x))
  > (square 2)
  ```
  
- some names are local, while others are global
  ```scheme
  ;here, x is global and y is local
  > (define x 3)
  > (define (add-x y)
      (+ x y))
  ```
  
- function names can occur in other functions' bodies, too
  ```scheme
  ; here, square is global
  > (define (cube x)
      (* x (square x)))
  ```
## 1.1.6 Conditional Expressions and Predicates
  ```scheme
  ; cond experssion
  > (define (myabs x)
      (cond ((> x 0) x)
        ((= x 0) 0)
        (else (- x))))
    ; format:
        (cond ((expression1) value1)
             ((expression2) value2)
             ((expression3) value3)
              .                .
              .                .
              .                .
             ((expressionN) valueN)
             (else (value)))
             
    ; once one of experssion is true, value is return and comparision won't continue
             
  ; if expression
  > (define (myabs x)
      (if (< x 0)
          (- x)
          x))
          
  ; format:
      (if (expression)
          (value)
          (else value))
  ```

- Scheme also supplies logical connectives such as and, or, not, `#t` is true, `#f` is false
 
  ```scheme
  > (define x 6)
  > (and (> x 5) (< x 10))
  #t
  > (not (> x 4))
  #f
  ```

- if statement can be used to evaluate procedures too
  ```scheme
  > (if (> 4 0) + -)
  #<procedure:+>
  
  > (define (a-plus-abs-b a b)
      ((if (> b 0) + -) a b))
  ```


## `Let` Syntax

  ```scheme
  (define (test num)
     (let ((x num))
       (if #t
           x
	   (+ x 1))))
  
  (test 2)
  ; ==> 2
  
  ; let is Syntactic sugar for lambda followed by it's arguments that are first evaluted and then passed to the lambda which is then evaluated.

  ((let ((x 2))
    (+ x 1)))
  ; is equivlent to  
  ((lambda(x)
    (+ x 1))
    2)
  ; but `let` can run itself alone
  
  
  (define (square x)
    (let ((m 3)
          (n 3))
    (* x m n))
  )
  > (square 2)
  18
  
  ; it's a little different than `define`
  
  (define (square x)
    (define m 3)
    (define n 3)
    (* x m n)
  )
  
  > (square 2)
  18
  
  ; results are the same, `let` can bind multiple value at one expression while `define` has 
  to call multiple times in order to do the same thing.
  ; Note: (let ((m 3)) ) it has extra parentheses surrounded around binding expression
  ```


## Factorial example
  ```scheme
  (define (fact x)
  (cond ((= x 0) 1)
    (else (* x (fact (- x 1))))))
    
  ; (fact 6)
  ; (* 6 (fact 5))
  ; (* 6 (* 5 (fact 4)))
  ; (* 6 (* 5 (* 4 (fact 3))))
  ; (* 6 (* 5 (* 4 (* 3 (fact 2)))))
  ; (* 6 (* 5 (* 4 (* 3 (* 2 (fact 1))))))
  ; (* 6 (* 5 (* 4 (* 3 (* 2 (* 1 (fact 0)))))))
  ; (* 6 (* 5 (* 4 (* 3 (* 2 (* 1 1))))))
  ; (* 6 (* 5 (* 4 (* 3 (* 2 1)))))
  ; (* 6 (* 5 (* 4 (* 3 2))))
  ; (* 6 (* 5 (* 4 6)))
  ; (* 6 (* 5 24))
  ; (* 6 120)
  ; 720
  
  ; as you might expect, one certifies the correctness of fact using
  ; induction on the non-negative integer input, x
  
  ; basis step: start with the smallest legal input, that is, x = 0
  ;             and observe that (fact 0) = 1, which is correct.  
  
  ; induction hypothesis:  assume (fact k) works correctly, that is, 
  ;             that the value returned by the call (fact k) is exactly
  ;             the factorial of k (written k!, as you know)
  
  ; induction step:  we show, using the induction hypothesis, that (fact (+ k 1))
  ;             works correctly.  According to the code, (fact (+ k 1)) returns
  ;             (* (+ k 1) (fact k)).  If (fact k) = k!, as it must by the 
  ;             induction hypothesis, then multiplying it by (+ k 1) certainly
  ;             returns the factorial of (+ k 1).
  
  ; we would want to check in addition that the program terminates - that is, that it
  ; does eventually return an answer.  we do this without using induction,
  ; as follows: as n >= 0 is an integer,
  ; and as each call to fact reduces the value of this parameter by 1, and as the procedure
  ; halts when n = 0, we see that any call (fact n) will terminate.  
  
  ; another design for a factoral procedure does not create a chain of
  ; deferred operations
  
  (define (new-fact x)
    (fact-iter x 0 1))

  (define (fact-iter x count result)
    (if (= count x)
        result
        (fact-iter x (+ count 1) (* (+ count 1) result))))
  
  ; here we see that the work is done by updating the values of count and result

  ;; (new-fact 6)
  ;; (fact-iter 6 0 1)
  ;; (fact-iter 6 1 1)
  ;; (fact-iter 6 2 2)
  ;; (fact-iter 6 3 6)
  ;; (fact-iter 6 4 24)
  ;; (fact-iter 6 5 120)
  ;; (fact-iter 6 6 720)
  
  ; such processes are said to be linear recursive, or iterative


  ; an alternate organization of the iterative version  casts fact-iter as a local function:
  
  (define (new-fact n)
    (define (fact-iter count result)
      (if (= count n)
      result
      (fact-iter (+ count 1) (* (+ count 1) result))))
    (fact-iter 0 1))
  ```

## 1.2 Procedures and the Processes they Generate
- Recursive process(proper recursion)
  - Linear recursive process
  - Tree recursive process
- Iterative process(tail recursion, eg. for, while)

   key to certifying iteration procedures is 'invariant relation'
  - Linear iterative process



## List and Tree

  ```scheme
  (quote (a b)) ==> (a b) ; quote suspends evaluation of the list
  ’(a b) ==> (a b) ; quote suspends evaluation of the list
  (car ’(a b)) ==> a ; get head of list (contents of address reg)
  (car ’()) ==> ERROR: car: Wrong type in arg1 () ; expects a list!
  (car ’((a) b)) ==> (a) ; get head of list, could be a list!
  (cdr ’(a b)) ==> (b) ; get rest of list (contents of address reg)
  (cdr ’()) ==> ERROR: car: Wrong type in arg1 () ; expects a list!
  (car ’((a) a (b))) ==> (a (b)) ; get rest of list
  (cons 3 ’()) ==> (3) ; put element on head of list
  (cons ’(a) ’(b c d)) ==> ((a) b c d)
  (cons "a" ’(b c)) ==> ("a" b c)
  (list a 7 c) ==> (a 7 c) ; build a list from the operands
  (list) ==> () ; build a list from the operands
  (length ’(a b c)) ==> 3 ; what is the length of a list?
  (length ’(a (b) (c d e))) ==> 3 ; what is the length of a list?
  (length ’()) ==> 0 ; what is the length of a list?
  (append ’(x) ’(y)) ==> (x y) ; append one list to another
  (append ’(a) ’(b c d)) ==> (a b c d) ; append one list to another
  (append ’(a (b)) ’((c))) ==> (a (b) (c)) ; append one list to another
  (reverse ’(a b c)) ==> (c b a) ; reverse a list
  (reverse ’(a (b c) d (e (f)))) ==> ((e (f)) d (b c) a) ; reverse a list
  (null? ’()) ==> #t ; is the list empty?
  (null? ’(2)) ==> #f ; is the list empty?
  
  (list-ref '(a b c d) 2) ==> c ; 0 based indexing, find nth element in the list
  ```

### Atom

An atom is a string of letters, digits, or characters other than ( or ) - no spaces, either

  ```scheme
  'atom
  'turkey
  '1492
  'abc$2
  
  ; anything which is not a list is an atom
  ; implementation of atom?
  (define atom?
    (lambda (x)
      (and (not (pair? x)) (not (null? x)))))
      
      
  ; A predicate check whether lst is a lat(a list of atoms)
  ; definition lat ::= () | (cons atom lat)
  (define lat?
    (lambda (lst)
     (cond ((null? lst) #t)
           (else 
            (and
             (atom? (car lst)) 
             (lat? (cdr lst)))))))
             
     
  ; an alternate design, which also corresponds (perhaps not as closely) to the definition
  (define alt-lat?
    (lambda (lst)
      (cond ((null? lst) #t)
            ((atom? (car lst)) (alt-lat? (cdr lst)))
            (else #f))))
            
            
            
  ; Check if a is in lat
  (define member?
    (lambda (a lat)
     (cond 
        ((null? lat) #f)
        (else (or (eq? (car lat) a)
                  (member? a (cdr lat)))))))
                  
                  
  ; removing a from lat
  (define rember2
    (lambda (a lat)
      (cond 
        ((null? lat) (quote ()))
        (else (cond ((eq? (car lat) a) (cdr lat))
                    (else (cons (car lat) (rember2 a (cdr lat)))))))))
                    
  (rember2 'and '(bacon lettuce and tomato))
  ; ==> (bacon lettuce tomato)
                
  
  ; insert new on the right-hand side of occ in the lat, insertR, R is right
  (define (insertR lat occ new)
    (cond ((null? lat) '())
	        ((eq? (car lat) occ) (cons (car lat) (cons new (cdr lat))))
  	      (else (cons (car lat) (insertR (cdr lat) occ new)))))
          
  (insertR '(a b c d c e f) 'c 'new)
  ; ==> (a b c new d c e f)
  
  
  ; Multiple insert new to occ's right hand side
  (define (multi-insertR lat occ new)
    (cond ((null? lat) '())
          ((eq? (car lat) occ) (cons (car lat) (cons new (multi-insertR (cdr lat) occ new))))
          (else (cons (car lat) (multi-insertR (cdr lat) occ new)))))

  (multi-insertR '(a b b) 'b 'new)
  ; ==> (a b new b new)
  
  
  
  (define (insertL lat occ new)
    (cond ((null? lat) '())
	        ((eq? (car lat) occ) (cons new (cons (car lat) (cdr lat))))
  	      (else (cons (car lat) (insertL (cdr lat) occ new)))))
  
    (insertL '(a b c d c e f) 'c 'new)
    ; ==> (a b new c d c e f)
  
  
  (define (multi-insertL lat occ new)
    (cond ((null? lat) '())
          ((eq? (car lat) occ) (cons new (cons (car lat) (multi-insertL (cdr lat) occ new))))
          (else (cons (car lat) (multi-insertL (cdr lat) occ new)))))

  (multi-insertL '(a b b) 'b 'new)
  ; ==> (a new b new b)
  
  
  
  ; substitution
  (define (subst lat occ new)
    (cond ((null? lat) '())
          ((eq? (car lat) occ) (cons new (cdr lat)))
          (else (cons (car lat) (subst (cdr lat) occ new)))))
  
  (subst '(a b c) 'b 'new)
  ; ==> (a new c)
  
  
  ; Multiple substitution
  (define (multisubst lat occ new)
    (cond ((null? lat) '())
          ((eq? (car lat) occ) (cons new (multisubst (cdr lat) occ new)))
          (else (cons (car lat) (multisubst (cdr lat) occ new)))))

  (multisubst '(a b b) 'b 'new)
  ```
  
 
Lists are collections of atoms or lists enclosed by parentheses

  ```scheme
  '(atom turkey or)
  '((atom turkey) or)
  '()
  ```

### Map, Reduce(accumulate), Filter

   ```scheme
   ;;;;;;;;;; MAP ;;;;;;;;;;
   (define (mymap f seq)
     (cond ((null? seq) seq)
       	   (else (cons (f (car seq))
                       (mymap f (cdr seq))))))

   (define (square a)
     (* a a))

   (mymap square '(1 2 3))
   
   
   ;;;;;;;;; ACCUMULATE(Reduce) ;;;;;;;;;;
   (define (accumulate op init seq)
     (cond ((null? seq) init)
       (else (op (car seq) (accumulate op init (cdr seq))))))

   (accumulate + 3 '(1 2 3))
   
   
   ;;;;;;;;; Filter ;;;;;;;;;
   (define (filter pred seq)
     (cond ((null? seq) seq)
       	   ((pred (car seq)) (cons (car seq) (filter pred (cdr seq))))
           (else (filter pred (cdr seq)))))

   (define (greaterThanThree a)
     (> a 3))

   (filter greaterThanThree '(5 8 2))
   ```

### S-expression

An s-exp ("s-expression") is either an atom, the empty list, or a list of s-exps

  ```scheme
  ()        ;is an s-exp

  xyz       ;is an s-exp

  (x y z)   ;is a list of s-exps, and hence an s-exp

  ((x y) z) ;is a list of two s-exps, (x y) and z, and hence is itself an s-exp
  
  ; s-exp ::=  atom | () | (s-exp ... s-exp)
  ; s-exp definition equal atom OR () OR (s-exp ... s-exp) 
  ```
  
  
## Problem from class

```scheme
; Problem 4 (40 points)

; In this problem, you are asked to implement and prove correct a procedure bfs for breadth-first
; search of a binary tree.

; Binary trees have nodes and branches, and leaves.  Leaves can be represented as atoms; binary trees
; which are not just leaves can be represented as lists of the form

;             (node left-subtree right-subtree)

; For example, (13 (5 6 1) (45 7 18)) is a binary tree with root node 13, and left and right subtrees.
; (5 6 1) is a binary tree with root node 5, left subtree 6 (which is a leaf), and right subtree 1 (also a leaf).

; Given a binary tree t, and an element e, your bfs program is to return #t if e occurs as a node or leaf element of t,
; and #f otherwise.  Thus, referring to the example just given as sample-tree, (bfs sample-tree 0) is #f, while
; (bfs sample-tree 13), (bfs sample-tree 5) and (bfs sample-tree 18) are all #t.

; You may assume that nodes and leaves are atoms.

; You will want to write selectors appropriate for this data structure and then to make use of these in your
; breadth-first search program. A constructor, say make-tree, could also be put to good use in setting up test data
; for your function.


; Recall from your algorithms course that breadth-first search proceeds by maintaining a list of
; the subtrees that must be returned to.  When a node is reached, it is examined - if it is the
; element searched for, return #t; otherwise, add its subtrees to the end of this
; search list, and then continue the search.  If the search list is empty, the search has failed.
; I leave it to you to work out what to do when a leaf is reached.

; (13 (5 6 1) (45 7 18))
;         13
;       /   \
;      5     45
;     / \   / \
;    6  1  7   18
;
; 13, 5, 45, 6, 1, 7, 18

(define (make-tree node left-subtree right-subtree) (list node left-subtree right-subtree))
(define (atom? node) (and (not (pair? node)) (not (null? node))))
(define (has-left-branch? t) (not (atom? (cadr t))))
(define (has-right-branch? t) (not (atom? (caddr t))))
(define (left-subtree-node t) (caadr t))
(define (right-subtree-node t) (caaddr t))
(define (left-subtree t) (cadr t))
(define (right-subtree t) (caddr t))
(define (node subtree) (car subtree))

(define (bfs t e)
  (cond ((null? t) #f)
        ((has-left-branch? t) (cond ((or (eq? e (node t)) (eq? e (left-subtree-node t))) #t)
                                     ((has-right-branch? t) (cond ((or (eq? e (node t)) (eq? e (right-subtree-node t))) #t)
                                                                   (else (or (bfs (left-subtree t) e) (bfs (right-subtree t) e)))))
                                     (else (bfs (left-subtree t) e))))
        ((has-right-branch? t) (cond ((or (eq? e (node t)) (eq? e (right-subtree-node t))) #t)
                                      (else (bfs (right-subtree t) e))))
        (else (or (eq? e (left-subtree t)) (eq? e (right-subtree t)) (eq? e (node t))))))

(make-tree 13 '(5 (6 2 3) 1) '(45 7 18))

(bfs (make-tree 13 '(5 (6 2 3) 1) '(45 7 18)) 7)


```
