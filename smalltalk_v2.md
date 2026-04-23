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

