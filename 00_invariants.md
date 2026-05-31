# Invariants

## Learning Objectives

By the end of this tutorial, you will be able to:
1. Define common invariant types.
2. Identify common invariant types, and whether invariants are useful and/or correct.
3. Infer useful, correct invariants from existing programs.
4. Design programs to solve problems with useful correct invariants.

## Introduction to Invariants

An **invariant** is a condition that must always hold true at a particular point in a program. Correct invariants are always true when they are supposed to be, and are said to be **maintained** by the program. Incorrect invariants are sometimes false, and are said to be **violated**. Invariants allow programmers to formally prove their programs are bug-free. Invariants are also an incredibly powerful tool for program design. Expert programmers often state invariants first, then write code to maintain them. For them, the program is the proof of correctness, not the code.

Consider the following `max` function that computes the maximum value in a non-empty input list `arr`:
```py
def max(arr: list[int]) -> int:
    """
    Invariant A:
    `max(arr)` is the maximum value in non-empty input list `arr`.
    """
    # Invariant B:
    # `arr` is a non-empty list.
    n = len(arr)
    # Invariant C:
    # `max_so_far` is the maximum value in `arr[:i]`.
    max_so_far = arr[0]
    for i in range(1, n):
        if arr[i] > max_so_far:
            max_so_far = arr[i]
    # Invariant D:
    # `max_so_far` is the maximum value in `arr[:n]` which is `arr`.
    return max_so_far
```

There are four key invariants in this program: 
* Invariant A states what the `max` function returns. Such invariants are known as **postconditions** since they describe the program state _after_ the function returns. The function body is responsible for maintaining all postconditions.
* Invariant B states what the `max` function takes as an argument. Such invariants are known as **preconditions** since they describe the program state _before_ the function is called. If any precondition is violated, the function may behave incorrectly (i.e. the postconditions may be violated). For the `max` function, if the input list is empty, violating the precondition, the function will crash with an `IndexError` since the line `max_so_far = arr[0]` indexes `arr` out-of-bounds. The caller of the function is responsible for maintaining all preconditions.
* Invariant C states what the `max_so_far` variable represents at the start of each iteration of the loop. Such invariants are known as **loop invariants** since they describe the program state _before_ and _during_ iteration. Variable initialization and the loop body are responsible for maintaining all loop invariants.
* Invariant D states what the `max_so_far` variable represents after the loop exits. Such invariants are known as **termination invariants** since they describe the program state _after_ iteration. Termination invariants are a direct consequence of loop invariants. For the `max` function, the termination invariant is simply the loop invariant with `i = n`. In general, loops build towards desirable termination invariants.

These invariants work together to ensure the `max` function works correctly. First, the precondition (Invariant B) guarantees `arr` is non-empty so the line `max_so_far = arr[0]` will execute successfully. Second, the loop invariant (Invariant C) ensures `max_so_far` is the maximum value in an expanding prefix of `arr`, so that by the end of the loop, `max_so_far` is the maximum in the entire array `arr` and Invariant D is maintained. Since `max_so_far` is returned and it is the maximum value in `arr`, Invariant A is maintained and the function works correctly.

These invariants are all useful invariants. A **useful** invariant describes what a variable or function call represents, not just a property it happens to have. Moreover, that meaning is required for reasoning about the correctness of the program. For example, the invariant `max_so_far >= arr[0]` is true, but not useful. It provides no information about `max_so_far`'s purpose.


At first, invariants may feel abstract, pointless, and/or difficult. Like anything, learning to use invariants requires intense, sustained practice. Invariants are a deep topic and take years to mastery. However, I assure you, learning invariants will radically transform your programming ability. 

### Problem

Consider the following `min` function that computes the minimum value in a non-empty input list `arr`:
```py
def min(arr: list[int]) -> int:
    """
    Invariant 1:
    `min(arr)` is the minimum value in non-empty input list `arr`.
    """
    # Invariant 2:
    # `arr` is a non-empty list.
    n = len(arr)
    # Invariant 3:
    # `min_so_far` is the minimum value in `arr[i:]`.
    min_so_far = arr[n - 1]
    # Invariant 4:
    # `i` is largest index not yet processed.
    for i in range(n - 2, 0, -1):
        if arr[i] < min_so_far:
            min_so_far = arr[i]
    # Invariant 5:
    # `min_so_far` is the minimum value in `arr[0:]` which is `arr`.
    return min_so_far
```

Identify the kind of each invariant annotated in the `min` function, and whether it is maintained, useful, both, or neither. 
<details>
    <summary>Show solution</summary>

* Invariant 1 is a postcondition. It is useful since it describes exactly what the function should compute. However, it is not maintained since Invariant 5 is not maintained.
* Invariant 2 is a precondition. It is useful since it describes exactly what `arr` represents, beyond what is stated in the type hint (e.g. non-empty). It may or may not be maintained since the caller is responsible for maintaining preconditions.
* Invariant 3 is a loop invariant. It is useful since it describes exactly what `min_so_far`  represents. It is maintained at the _end_ of each iteration of the loop. 
* Invariant 4 is a loop invariant. It is not useful since the order in which the elements of `arr` are processed does not impact the end result. It is maintained since the indices are processed largest to smallest.
* Invariant 5 is a termination invariant. It is useful since it describes exactly what `min_so_far` represents on termination. However, it is not maintained since `arr[0]` is never processed, and so the loop terminates with `min_so_far` as the minimum value in `arr[1:]`.

</details>

### Problem

Consider the following `sum` function that computes the sum of an input list `arr`:
```py
def sum(arr: list[int]) -> int:
    """
    ???
    """
    # ???
    n = len(arr)
    # ???
    sum_so_far = 0
    for i in range(n):
        sum_so_far += arr[i]
    # ???
    return sum_so_far 
```

Annotate the `sum` function with _useful_ invariants like the `max` function. 
<details>
    <summary>Show solution</summary>

There are a few differences between the `sum` and `max` functions. First, the `sum` function does not have a useful precondition like the `max` function. The list need not be non-empty. A precondition adds no additional information beyond the type hint, and so should not be stated. Second, the postcondition, loop invariant, and termination invariant are stated in terms of sums, not maxes. 
```py
def sum(arr: list[int]) -> int:
    """
    `sum(arr)` is the sum of all values in list `arr`.
    """
    n = len(arr)
    # `sum_so_far` is the sum of all values in `arr[:i]`.
    sum_so_far = 0
    for i in range(n):
        sum_so_far += arr[i]
    # `sum_so_far` is the sum of all values in `arr[:n]`, which is `arr`.
    return sum_so_far 
```
</details>

## Program Design with Invariants 

At a high-level, invariant-driven program design follows three steps:
1. State preconditions and postconditions on the original function. Think of this as understanding the overall problem statement.
2. State a desirable invariant. Think of this as what the current subproblem is but not necessarily how to solve it. This usually means stating a postcondition or a termination invariant. Write as much code as possible to directly maintain this invariant.
3. State an invariant that helps maintain the original invariant. Think of this as how to solve the current subproblem. This is usually a loop invariant. Write as much code as possible to directly maintain this invariant.
4. Repeat from Step 2.

Loop invariants are typically the most difficult to state. These invariants can often be framed in terms of prefixes, suffixes or subarrays. A **prefix** is a contiguous portion of an array that starts with the first element (e.g. `arr[:i]`). The loop invariant in the `max` function above is defined in terms of a prefix of `arr`. A **suffix** is a contiguous portion of an array that ends with the last element (e.g. `arr[i:]`). The loop invariant in the `min` function above is defined in terms of a suffix of `arr`. Usually, prefix and suffix invariants are interchangable. For example, both the `min` and `max` functions can be written using a prefix (scan left to right) or suffix (scan right to left) invariant. The choice is down to personal preference. A **subarray** is any contiguous portion of an array (e.g. `arr[i:j]`). Subarray invariants can sometimes, but not always, be replaced with prefix or suffix invariants. Any prefix or suffix invariant is a subarray invariant since prefixes and suffixes are subarrays.

Read [1672. Richest Customer Wealth](https://leetcode.com/problems/richest-customer-wealth/description/) and work through the provided input/output examples by hand. We will solve this problem with invariants.

Starting with Step 1, state preconditions and postconditions from the problem statement.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty grid `accounts`.
        """
        # `accounts` is a non-empty positive grid.
```

For Step 2, define a desirable termination invariant. In this case, the termination invariant comes directly from the postcondition.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty grid `accounts`.
        """
        # `accounts` is a non-empty positive grid.
        ...
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

For Step 3, define a loop invariant that will eventually transform into the termination invariant. If we compute the maximum wealth of all prefixes of `accounts`, we eventually compute the maximum wealth over all accounts. More formally, we maintain the maximum wealth in the prefix `accounts[:i]` (i.e. all values in `accounts` up to but not including `accounts[i]`). After the loop terminates, the prefix will contain all accounts (i.e. `accounts[:m] == accounts`), and so we will have the maximum wealth over all accounts. A suffix invariant also works here but iterating in reverse order might be less natural for this problem. 

Correct variable initialization for loop invariants requires care. In this case, initializing `max_wealth_so_far` to `0` is correct since the precondition states all accounts contain positive wealth and there will always be at least one account. This means `max_wealth_so_far` will always be replaced with the wealth of `accounts[0]` after the first iteration. However, if the accounts could contain negative wealth, zero initialization would be incorrect since the maximum wealth might be negative.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        # `accounts` is a non-empty positive grid.
        m = len(accounts)
        # `max_wealth_so_far` is the maximum wealth in `accounts[:i]`.
        max_wealth_so_far = 0
        for i in range(m):
            ...
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

As Step 3 is complete, repeat from Step 2, focusing on maintaining the loop invariant. Start with a desirable termination invariant. In this case, we must know the wealth of the current account to maintain the invariant on `max_wealth_so_far`. 
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        # `accounts` is a non-empty positive grid.
        m = len(accounts)
        # `max_wealth_so_far` is the maximum wealth in `accounts[:i]`.
        max_wealth_so_far = 0
        for i in range(m):
            ...
            # `wealth_so_far` is the total wealth of `accounts[i]`.
            if wealth_so_far > max_wealth_so_far:
                max_wealth_so_far = wealth_so_far
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

For Step 3, define a loop invariant that will eventually transform into the termination invariant. If we compute the total wealth of all prefixes of `accounts[i]`, we eventually compute the wealth in `accounts[i]`. More formally, we maintain the total wealth in the prefix `accounts[i][:j]` (i.e. all values in `accounts[i]` up to but not including `accounts[i][j]`). After the loop terminates, the prefix will contain the entire list (i.e. `accounts[i][:n] == accounts[i]`), and so we will have the total wealth. Again, although the invariant can be stated in terms of a prefix or a suffix, we opt for a prefix. 

Again, variable initialization requires care. In this case, the total wealth in `accounts[i][:0] == []` is `0`, so `wealth_so_far` must start at `0`. Unlike before, this is always correct regardless of negative wealth. The sum of nothing is defined as zero while the maximum of nothing is not defined. This is why initializing `max_wealth_so_far` requires a careful argument relying on the positive wealth precondition while `wealth_so_far` follows directly from the invariant.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        # `accounts` is a non-empty positive grid.
        m = len(accounts)
        n = len(accounts[0])
        # `max_wealth_so_far` is the maximum wealth in `accounts[:i]`.
        max_wealth_so_far = 0
        for i in range(m):
            # `wealth_so_far` is the total wealth in `accounts[i][:j]`.
            wealth_so_far = 0
            for j in range(n):
                wealth_so_far += accounts[i][j]
            # `wealth_so_far` is the total wealth of `accounts[i]`.
            if wealth_so_far > max_wealth_so_far:
                max_wealth_so_far = wealth_so_far
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

### Problem

Consider the following two `rfind` functions that find the rightmost occurence of `target` in an input list `arr`: 
```py
def rfind(arr: list[int], target: int) -> int:
    """
    `rfind(arr)` is rightmost occurence of `target` in `arr`. `target` must occur at least in `arr`.
    """
    n = len(arr)
    # `target` occurs at least once in `arr`.
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr[:i]` (prefix).
    ...
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr`.
    return rightmost_so_far


def rfind(arr: list[int], target: int) -> int:
    """
    `rfind(arr)` is rightmost occurence of `target` in `arr`. `target` must occur at least in `arr`.
    """
    n = len(arr)
    # `target` occurs at least once in `arr`.
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr[i:]` (suffix).
    ...
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr`.
    return rightmost_so_far
```

Complete both functions so that the invariants are maintained.
<details>
    <summary>Show solution</summary>

For both functions, initialization is tricky. Since `target` occurs at least once in `arr`, we can set `rightmost_so_far` to `-1` since this will be further left than all valid indices. For the first function, iterate in left to right order to compute prefixes. For the second function, iterate in right to left order to compute suffixes.
```py
def rfind(arr: list[int], target: int) -> int:
    """
    `rfind(arr)` is rightmost occurence of `target` in `arr`. `target` must occur at least in `arr`.
    """
    n = len(arr)
    # `target` occurs at least once in `arr`.
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr[:i]` (prefix).
    rightmost_so_far = -1
    for i in range(n):
        # Note:
        # Just `arr[i] == target` also works as the condition. Since `i` is increasing,
        # whenever we find a new occurence of `target`, it is always the rightmost.
        if arr[i] == target and i > rightmost_so_far:
            rightmost_so_far = i
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr`.
    return rightmost_so_far


def rfind(arr: list[int], target: int) -> int:
    """
    `rfind(arr)` is rightmost occurence of `target` in `arr`. `target` must occur at least in `arr`.
    """
    n = len(arr)
    # `target` occurs at least once in `arr`.
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr[i:]` (suffix).
    rightmost_so_far = -1
    for i in range(n - 1, -1, -1):
        # Note:
        # Just `arr[i] == target and rightmost_so_far == -1` also works as the condition. 
        # Since `i` is decreasing, the first occurence of `target` is always the rightmost.
        if arr[i] == target and i > rightmost_so_far:
            rightmost_so_far = i
    # `rightmost_so_far` is the rightmost occurence of `target` in `arr`.
    return rightmost_so_far
```

</details>

### Problem

Consider the following `product` function that computes the product of an input list `arr`:
```py
def product(arr: list[int]) -> int:
    """
    `product(arr)` is the product of all values in list `arr`.
    """
    n = len(arr)
    ...
    # `product_so_far` is the product of all values in `arr`.
    return product_so_far
```

Complete Step 3 for the `product` function. 
<details>
    <summary>Show solution</summary>

If we compute the product of all prefixes of `arr`, then eventually we compute the product of `arr`. Therefore, if we maintain that `product_so_far` is the product of `arr[:i]`, we maintain the termination invariant. The product of zero values is `1`, and so, since `product_so_far` is the product of `arr[:0] == []` on the first iteration, `product_so_far` must start at `1`.
```py
def product(arr: list[int]) -> int:
    """
    `product(arr)` is the product of all values in list `arr`.
    """
    n = len(arr)
    # `product_so_far` is the product of all values in `arr[:i]`.
    product_so_far = 1
    for i in range(n):
        product_so_far *= arr[i]
    # `product_so_far` is the product of all values in `arr`.
    return product_so_far
```
</details>
