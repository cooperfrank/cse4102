```smalltalk
(3 = 4) iftrue: ['Yes']. "returns nil, which is like null pointer"
```

The square brackets are the branches, we can also put statements in like below:

```smalltalk
a := 0.
(4 = 4) ifTrue: [ a := 1 ].
a "a is equal to 1"
```

In the following example, 1 + 1 gets evaluated first, then the boolean is run
```smalltalk
(4 = 4) ifFalse: 1 + 1.
```

This is bad for the following case
```smalltalk
(a = 0) ifFalse: 42 / a.
```

The square brackets prevent execution until this branch is actually reached
```smalltalk
(a = 0) ifFalse: [42 / a] ifTrue: 0.
```

Square brackets are called **blocks**, which package up pieces of code
- Note: Blocks are of class FullBlockClosure

```smalltalk
[1 + 3] value "3"
"value is necessary here even though it takes no arguments to prompt the block to evaluate"
```

Blocks can take parameters

value should take as many parameters as the block takes
```smalltalk
[:x :y | x + y] value: 1 value: 2 "3"
```

While loops
```smalltalk
n := 1.
[n < 1000] whileTrue: [n := n * 2].
Transcript show: n; cr.
```

timesRepeat
```smalltalk
n := 1.
10 timesRepeat: [n := n * 2].
```

intervals
```smalltalk
result := String new.
1 to: 10 do: [:n | result := result, n printString, ' '].

"1 to: 10 returns an Interval class"
"This syntax with to:do: is basically a for loop, the argument to our lambda block is like i
```

collect
```smalltalk
(1 to: 10) collect: [:each | each * each].
"returns 1 to 10 squared"
"this is just map"
```

strings are just collections of characters, so we can call collect on a string
```smalltalk
'hello' collect: [:c | c asUpperCase] "HELLO"
```

select, which is basically filter
```smalltalk
'hello there' select: [c: | c isVowel not]. "hll thr"
```

reject, which is the opposite of filter
```smalltalk
'hello there' reject: [c: | c isVowel]. "hll thr"
```

detect, which answers with the first element for which the block evaluates to true
```smalltalk
'hello there' detect: [c: | c isVowel]. "e"
```

inject, which is basically fold
```smalltalk
(1 to: 10) inject: 0 into: [:acc :i | acc + i]. "55"

(1 to: 10) inject: '' into: [:acc :i | acc, i printString, ' '].
```

Inject in a more object oriented way, the above was sort of functionally

Ths is like a for loop
```smalltalk
sum := 0.
(1 to: 100) do: [:i | sum := sum + i].
```

here, we'll use 'do it' on the loop rather than print it because we arent interested in what it prints, but the effect it has on sum

We can make this look more like a while loop:
```smalltalk
sum := 0.
i := 1.
[i <= 100] whileTrue: [sum := sum + i. i := i + 1]
sum
i
```

We can use the these operations with any collection
```smalltalk
#(1 25 825 324) inject: 0 into: [:s :i | s + 1]

sum := 0.
#(1 25 825 324) do: [:i | sum := sum + i].

"indexing over the collection"
sum := 0.
arr := #(1 25 825 324)
i := 0.
[i < (arr size)] whileTrue: [sum := sum + (arr atWrap: i). i := i + 1]
sum
i

"note: atWrap gets the element at the index passed, if the index is out of range then it wraps around"
```

Practice Problem
```smalltalk
zeros := 0.
nzprod := 1.
#(1 6 0 32 3 0 1 0) do: [:i | (i = 0) ifTrue: [zeros := zeros + 1] ifFalse: [nzprod := nzprod * i]].

"its very important that the ifTrue and ifFalse blocks are in square brackets, or else they will run no matter what"
"also, the do should be a block because its actually a lambda function, it needs to be"
```

The object is the only one that can change its instance varaibles, so we need to define messages that let us change/set instance variables of an object

Higher order functions
```smalltalk
"Higher order functions"
epsilon := 0.000001.
derive := [:f | [: x | (f value: x + epsilon) - (f value: x) / epsilon]].

f := [:x | x * x].
fprime := derive value:f.
fprime value: 2.5. 5.00000100078779
```
