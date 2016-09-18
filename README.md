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



  

