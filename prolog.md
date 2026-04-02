# Prolog

## Prolog Overview
- Logic programming language
- Stands for **Pro**grammation en **log**ique ("programming in logic")
- Invented by Alain Colmerauer in 1972
- Natural fit for searching problems

### SWI Prolog
- Modern-ish implementation
- Largely-compliant with ISO, but adds a lot of SWI-specific features
- Free and open-source
- Online version: https://swish.swi-prolog.org/
  - This will be used for homework
 
## Prolog's Core
- Specify a set of **facts** 
- Specify **rules** for building facts from other facts
- Pose **queries** that ask whether some fact is derivable

**Example**
Fact: fib(0) = 1
Fact: fib(1) = 1
Rule: if n > 1 and fib(n - 2) = a and fib(n - 2) = b, then fib(n) = a + b
Query: What is fib(5)?

Note: A knowledge base is a set of facts and rules

## Facts
Written as:
```prolog
woman(fudd).
man(karaban).
dog(jonathan).
husky(fudd).
husky(karaban).
husky(jonathan).
```

Facts are predicates applied to atoms

`predicate(atom)`

The same predicates must be contiguous in the file (ex. you can't define a woman, then a man, then back to another woman)

## Query
- Perform computation by making a query
- Has the same syntax as a fact
- ex. ?- dog(jonathan)

## Predicates
- Predicates have an arity (number of arguments)
  - ex. coach/1(auriemma)
  - ex. coach/2(hurley, mullins)
- Prolog considers these different predicates

## Rules
- Rules are like inference rules
- coach(geno, X) :- woman(X)

woman(X)
---.
coach(geno, X)

Reads like coach(geno, X) if woman(X)

We can expand this to add multiple premises

coach(geno, X) :- woman(X), husky(X)

