# Question 1

### Q1.1

As wrriten in meni's lecture note's No such program exists. Because L1 lacks function definitions and recursion, define serves mainly as syntactic convenience. Therefore, any L1 program can be transformed into an equivalent L11 program via direct substitution of the evaluated expression for every variable reference.

### Q1.2

No, every L2 program can be transformed into an equivalent L21 program. Global definitions can be replaced by let expressions, and recursive procedures can be implemented without names using self-application, specifically by utilizing the Y-Combinator pattern.

### Q1.3

No, there is no such program. We can transform any L2 program into an equivalent L22 program by handling both multiple parameters and multiple expressions.
First, we use Currying to break down functions with multiple variables into a chain of lambda functions that take exactly one variable.
Second, if a function body has multiple expressions, we simulate the sequence by nesting them inside another lambda application.

### Q1.4

Yes, such a program exists. We cannot always evaluate a function before passing it because the function might need to be applied to local arguments that only exist inside the scope of the receiving function. For example, in a higher-order function application like :

```scheme
((lambda (f) (f 5)) (lambda (x) (* x 2)))
```

the passed function cannot be evaluated beforehand since it relies on the argument 5 provided inside the body. Therefore, programs relying on this behavior cannot be transformed.

### Q1.5

Special forms are required because primitive operators strictly follow applicative-order evaluation, meaning all arguments are unconditionally evaluated before the operator is applied. We need special forms like if or and to control the evaluation flow (lazy evaluation); for example, if if were a primitive operator, an expression like:

``` scheme
(if (= x 0) 1 (/ 1 x))
```

would crash due to division by zero when x=0, because all arguments would be evaluated upfront.

### Q1.6

No, multiple expressions are not required in pure functional programming. Since there are no side effects, evaluating preceding expressions without using their results is meaningless. It is useful in languages that support side effects (such as imperative languages with state mutation or I/O). In L3, which is purely functional and lacks side effects, multiple expressions in a body are technically unnecessary as they do not affect the final returned value.

### Q1.7

Instead of using a variable's name, a lexical address uses coordinates [depth, index] to tell the interpreter exactly where the variable is defined. 'Depth' is how many lambda scopes we need to step outward to find the declaration, and 'index' is its position in the parameter list.

For example, in :

``` scheme
(lambda (y x)
    ( (lambda (x) (+ [x:0 0] [y:1 0])) 
      (+ [x:0 1] [z:free])
    )
)
```

the inner x is found in the current scope (depth 0, index 0), y is one scope outward (depth 1, index 0), the outer x is evaluated in the outer scope (depth 0, index 1), and z is global (free).

### Q1.8

*<u>Primop Advantages</u>* : It keeps the interpreter simpler and independent of the environment, avoiding the overhead of initializing and searching a global environment for basic operations since they are hard-coded in the evaluation logic.

*<u>Closure/PrimProc Advantage</u>* : It provides high extensibility and uniformity. Adding a new primitive only requires binding it in the global environment upon initialization without altering the AST or evaluation rules, and it allows primitives to be passed as arguments (higher-order functions) just like any other user-defined function.

### Q1.9

a. The main reason to switch is to avoid evaluating arguments that are ultimately unused in the function body, which prevents runtime errors (like division by zero) or infinite loops.

Example:

``` scheme
(define ignore-y (lambda (x y) x))
```

with the call :

```scheme
(ignore-y 5 (/ 1 0))
```

will crash in applicative order due to division by zero, but in normal order, it will safely evaluate to 5 because the second argument is never evaluated.

b. The main reason to switch is to avoid redundant, duplicate evaluations of the same argument if it is used multiple times within the function body, which significantly improves performance.

Example:

```scheme
(define square (lambda (x) (* x x)))
```

with the call :

```scheme
(square (+ 2 3))
```

in normal order will evaluate (+ 2 3) twice (once for each x), whereas applicative order evaluates it only once before passing the result to the function.

### Q1.10

a. In the substitution model, arguments are evaluated into runtime Values before the function is applied. Because substitution is a syntactic operation that modifies the AST (replacing variables with expressions), valueToLitExp is required to convert these computed Values back into Literal Expressions (LitExp) so they can be legally inserted into the function body's AST.

b. Normal order uses lazy evaluation, meaning arguments are not evaluated before application. Instead, they are passed as un-evaluated Expressions. Since we are directly substituting raw AST expressions into the function body, there are no Values that need to be converted back into expressions.

c. The environment model does not perform textual substitution on the AST at all. Instead, arguments are evaluated into Values and bound directly to variable names within a memory structure (the Environment). Since the original AST remains unmodified and we only look up values in the environment, there is no need to wrap Values back into Literal Expressions.

### Q1.11

a. Renaming is not required because variable capture is naturally prevented by the hierarchy of environment frames. When the interpreter looks up a variable, it searches starting from the most recent frame and moves outward. If multiple variables share the same name, the interpreter finds the value in the recent frame first. This naturally hides the outer variables, meaning there is never a conflict that would require modifying variable names in the code.

b.No, renaming is not required in this case. The sole purpose of renaming in the substitution model is to prevent "variable capture", which happens when a free variable in the substituted term is accidentally bound by a local definition (like a lambda) in the target expression. If the term being substituted is closed (contains no free variables), there are no variables that can be captured by surrounding scopes. Therefore, its meaning remains strictly independent of where it is substituted, making renaming entirely unnecessary.
