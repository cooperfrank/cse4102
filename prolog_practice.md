Sum List
```prolog
sum_list([], Sum) :- Sum = 0.

sum_list(L, Sum) :- 
    L = [H | T],
    sum_list(T, Rest),
    Sum is H + Rest.
```

Unique List
```prolog
all_different([]) :- true. % all_different([]). is functionally equivalent
all_different(Ls) :- 
    Ls = [H | T],
    not_member(H, T),
    all_different(T).
    
not_member(_, []) :- true. % same here
not_member(X, Ls) :- 
    Ls = [H | T],
    X \= H,
    not_member(X, T).
```

Prefix
```prolog
prefix([], _) :- true.
prefix(_, []) :- false.
prefix(Ps, Ls) :-
    Ps = [PH | PT],
    Ls = [LH | LT],
    PH = LH,
    prefix(PT, LT).

% Better version:
% (writing false is generally bad; this will evaluate to false without that case because the step Ls = [LH | LT] on an empty list will fail)
prefix([], _).
prefix(Ps, Ls) :-
    Ps = [PH | PT],
    Ls = [LH | LT],
    PH = LH,
    prefix(PT, LT).
```
