# Scheme-Notes

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
## Other built-in expression `min`, `max`
  ```scheme
  > (min 1 2)
  1
  > (max 1 2)
  2
  ```

## `Let` Syntax

  ```scheme
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

  

