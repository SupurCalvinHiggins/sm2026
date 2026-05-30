# Reduce, Map and Filter

## Learning Objectives

By the end of this tutorial, you will be able to:
1. Design programs to solve problems with the reduce pattern.
2. Design programs to solve problems with the map pattern.
3. Design programs to solve problems with the filter pattern.

## Introduction to Reduce

The **reduce** pattern describes computations where a list is aggregated into single value. Many common problems, including the `sum`, `max`, and `find` functions, follow the reduce pattern.

The `sum` function transforms a list into a single sum:
```py
def sum(arr: list[int]) -> int:
    """
    `sum(arr)` is the sum of `arr`.
    """
    n = len(arr)
    # `acc` is the sum of `arr[:i]`.
    acc = 0
    for i in range(n):
        acc = acc + arr[i]
    # `acc` is the sum of `arr`.
    return acc
```

The `max` function transforms a list into a single maximum:
```py
def max(arr: list[int]) -> int:
    """
    `max(arr)` is the maximum of `arr`.
    """
    n = len(arr)
    # `acc` is the maximum of `arr[:i]`.
    acc = float("-inf")
    for i in range(n):
        acc = arr[i] if arr[i] > acc else acc
    # `acc` is the maximum of `arr`.
    return acc
```

The `find` function transforms a list into a single index:
```py
def find(arr: list[int], target: int) -> int:
    """
    `find(arr, target)` is the leftmost occurence of `target` in `arr`.
    """
    n = len(arr)
    # `acc` is the leftmost occurence of `target` in `arr[:i]`.
    acc = n 
    for i in range(n):
        acc = i if arr[i] == target and i < acc else acc
    # `acc` is the leftmost occurence of `target` in `arr`.
    return acc
```

In these programs, an accumulator variable `acc` is initialized, and a new value for `acc` is computed on each iteration from the current value of `acc` and the array value `arr[i]`. After the loop terminates, the `acc` variable contains the desired result. This is because the loop invariant on `acc` is stated in terms of the prefix `arr[:i]`, so on termination, `acc` is the aggregation of `arr[:n]` which is `arr`.

In general, the reduce pattern has two key parts: the initialization of `acc` and the transition operation `f` which computes the new value of `acc` from the current value of `acc` and `arr[i]`. To implement a new reduce algorithm, simply fill in `acc`'s initialization and the transition operation `f`. For example, the `sum` function can be derived by substituting `acc = 0` for the initialization and `acc = acc + arr[i]` for the transition.
```py
def reduce(arr: list[int]) -> int:
    n = len(arr)
    # `acc` is f(f(f(..., arr[i-3]), arr[i-2]), arr[i-1]).
    acc = ...
    for i in range(n):
        acc = f(acc, arr[i])
    # `acc` is f(f(f(..., arr[n-3]), arr[n-2]), arr[n-1]).
    return acc
```

## Introduction to Map 

TODO

## Introduction to Filter 

TODO

## Program Design with Reduce, Map and Filter

TODO

