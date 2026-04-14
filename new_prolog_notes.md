**Word Puzzle**

```prolog
word(hot).
word(cold).
word(bus).
word(hat).

opposite(hot, cold).
opposite(cold, hot).

soundslike(hot, hat).
soundslike(hat, hot).
soundslike(A, A).

% Challenge: Find three words such that:
% A and C are the same word.
% A is the opposite of B.
% B sounds like "hat".

puzzle(A, B, A) :-
    word(A),
    word(B),
    opposite(A, B),
    soundslike(hat, B).
```

**Map Coloring**
```prolog
different(A, B) :- color(A), color(B), A \= B.

% When using something like the above, make sure A and B are resolved and 
% not floating free variables

% Lists
color(A) :- member(A, [red, yellow, blue, green]).

% We can use lists with member instead of like color(red). color(yellow). color(blue). color(green)

% Pattern Matching on lists
% alldifferent([]).
% alldifferent([H | T]) :-
% Can call recursively on Tail T

alldifferent([]).
alldifferent([ (A, B) | T ]) :-
    different(A, B), alldifferent(T).
```

**Sodoku**
```prolog
% 4x4 sodoku

isdigit(A) :- member(A, [1, 2, 3, 4]).

notin(_, []).
notin(X, [ H | T ]) :- X \= H, notin(X, T).

unique([]).
unique([ H | T ]) :- notin(H, T), unique(T).

alldigits([]).
alldigits([ H | T ]) :- isdigit(H), alldigits(T).

sodoku(List) :-
    List = [A, B, C, D, E, F, G, H, I, J, K, L, M, N, O, P], % Unify, pattern match list with each variable. Will fail if not exactly 16 members
    alldigits([A, B, C, D, E, F, G, H, I, J, K, L, M, N, O, P]),
    
    unique([A, B, C, D]),
    unique([E, F, G, H]),
    unique([I, J, K, L]),
    unique([M, N, O, P]).
	% on and on for each
```


**Logic Puzzle**
```prolog
rightof(A, B, [B, A, _]).
rightof(A, B, [_, B, A]).

houses(HouseList) :-
    member((english, red,  _), HouseList),
    member((spanish, _, jaguar), HouseList),
    rightof((japanese, _, _), (_, _, snail), HouseList),
    rightof((_, blue, _), (_, _, snail), HouseList),
    member((_, _, zebra), HouseList),
    member((_, green, _), HouseList).
```
