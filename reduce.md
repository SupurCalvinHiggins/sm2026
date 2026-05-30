# Reduce

## Learning Objectives

By the end of this tutorial, you will be able to:
1. Design programs to solve problems with the reduce pattern.

## Introduction to Reduce

The **reduce** pattern describes computations where a list is aggregated into single value. Many common problems, including the `sum`, `max`, and `find` functions, follow the reduce pattern. The reduce pattern (and the map pattern) are so powerful and general that entire programming paradigms like Google's `MapReduce` are structured around them.

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
    `find(arr, target)` is the leftmost occurrence of `target` in `arr`.
    """
    n = len(arr)
    # `acc` is the leftmost occurrence of `target` in `arr[:i]`.
    acc = n 
    for i in range(n):
        acc = i if arr[i] == target and i < acc else acc
    # `acc` is the leftmost occurrence of `target` in `arr`.
    return acc
```

In these programs, an accumulator variable `acc` is initialized, and a new value for `acc` is computed on each iteration from the current value of `acc` and the array value `arr[i]`. After the loop terminates, the `acc` variable contains the desired result. This is because the loop invariant on `acc` is stated in terms of the prefix `arr[:i]`, so on termination, `acc` is the aggregation of `arr[:n]` which is `arr`.

In general, the reduce pattern has two key parts: the initialization of `acc` and the transition operation `f` which computes the new value of `acc` from the current value of `acc` and `arr[i]`. To implement a new reduce algorithm, fill in `acc`'s initialization and the transition operation `f`. For example, the `sum` function can be derived by substituting `acc = 0` for the initialization and `acc = acc + arr[i]` for the transition.
```py
def reduce(arr: list[int]) -> int:
    n = len(arr)
    # `acc` is `f(acc, arr[:i])`.
    acc = ...
    for i in range(n):
        acc = f(acc, arr[i])
    # `acc` is `f(acc, arr)`.
    return acc
```

The initialization of `acc` requires some care. For the `sum` function, the value of `acc` on `arr[:0] == []` is well-defined as `0`. However, for the `max` and `find` functions, the value of `acc` on `arr[:0] == []` is not defined (What is the max of nothing? Where is the leftmost occurrence if the target does not exist?). For these, `acc` should be set so that the loop invariant is meaningful as soon as possible. For `max`, this means `acc` is set to `-inf` so that the first element `arr[0]` is always greater than `acc`, and so the loop invariant is true after the first iteration. For `find`, this means `acc` is set to `n` so that the first occurrence of `target` is always at a lower index than `acc` (since `n` is higher than all valid indices).

Although the examples use `acc` for consistency, real code should use more meaningful names instead. For example, `max_so_far`, `sum_so_far` or `leftmost_so_far`.

The reduce pattern applies when the problem or subproblem requires computing a single value from a list.

### Problem

Complete the following `min` function:
```py
def min(arr: list[int]) -> int:
    """
    `min(arr)` is the minimum of `arr`.
    """
    n = len(arr)
    # `acc` is the minimum of `arr[:i]`.
    acc = ...
    for i in range(n):
        acc = ...
    # `acc` is the minimum of `arr`.
    return acc
```

<details>
    <summary>Show solution</summary>

The minimum value of an empty list is not defined, so we must choose an initial value for `acc` such that `acc` is at least as large as any element in the list. This ensures that the invariant is true after the first iteration, since `acc` will be `arr[0]`. One such option is `inf`. Other values are possible: `acc` can also be initialized to `arr[0]`. The transition operation selects the smaller of `acc` and `arr[i]`. This maintains the loop invariant since `acc` is the smallest value in `arr[:i-1]`, so the smaller of this and `arr[i]` is the smallest value in `arr[:i]` as desired.
```py
def min(arr: list[int]) -> int:
    """
    `min(arr)` is the minimum of `arr`.
    """
    n = len(arr)
    # `acc` is the minimum of `arr[:i]`.
    acc = float("inf")
    for i in range(n):
        acc = arr[i] if arr[i] < acc else acc
    # `acc` is the minimum of `arr`.
    return acc
```
</details>

### Problem

Complete this `contains` function, following the reduce pattern:
```py
def contains(arr: list[int], target: int) -> int:
    """
    `contains(arr)` is true if and only if `target` is in `arr`.
    """
    ...
```

<details>
    <summary>Show solution</summary>

First, state the invariants. The loop invariant follows directly from the termination invariant. 
```py
def contains(arr: list[int], target: int) -> bool:
    """
    `contains(arr)` is true if and only if `target` is in `arr`.
    """
    n = len(arr)
    # `acc` is true if and only if `target` is in `arr[:i]`.
    acc = ...
    for i in range(n):
        ...
    # `acc` is true if and only if `target` is in `arr`.
    return acc
```

Second, fill in the initialization of `acc` and the transition operation. Since `target` is not in `arr[:0] == []`, `acc` should start as `False`. Then, `target` is in `arr[:i]` if and only if `target` is in the previous prefix or `arr[i] == target`. The old value of `acc` is `True` if and only if `target` is in previous prefix, so the transition operation is `acc = acc or arr[i] == target`.
```py
def contains(arr: list[int], target: int) -> bool:
    """
    `contains(arr)` is true if and only if `target` is in `arr`.
    """
    n = len(arr)
    # `acc` is true if and only if `target` is in `arr[:i]`.
    acc = False
    for i in range(n):
        acc = acc or arr[i] == target
    # `acc` is true if and only if `target` is in `arr`.
    return acc
```

</details>

