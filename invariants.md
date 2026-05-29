# Invariants

An **invariant** is a condition that must always true hold true at a particular point in a program. Correct invariants are always true when they are supposed to be, and are said to be **maintained** by the program. Incorrect invariants are sometimes false, and are said to be **violated**. Invariants allow programmers to formally prove their programs are bug-free. Invariants are also an incredibly powerful tool for program design. Expert programmers often state invariants first, then write code to maintain them. For them, the program is the proof of correcntess, not the code.

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
* Invariant D states what the `max_so_far` variable represents after the loop exits. Such invariants are known as **termination invariants** since they describe the program state _after_ iteration. Termination invariants are a direct consequence of loop invariants. For the `max` funciton, the termination invariant is simply the loop invariant with `i = n`. In general, loops build towards desirable termination invariants.

These invariants work together to ensure the `max` function works correctly. First, the precondition (Invariant B) guarantees `arr` is non-empty so the line `max_so_far = arr[0]` will execute successfully. Second, the loop invariant (Invariant C) ensures `max_so_far` is the maximum value in an expanding prefix of `arr`, so that by the end of the loop, `max_so_far` is the maximum in the entire array `arr` and Invariant D is maintained. Since `max_so_far` is returned and it is the maximum value in `arr`, Invariant A is maintained and the function works correctly.

At first, invariants may feel abstract, pointless, and/or difficult. Like anything, learning to use invariants requires intense, sustained practice. Invariants are deep topic and can take years to mastery. However, I assure you, learning invariants will radically transform your programming ability. 

## Example

Consider [1672. Richest Customer Wealth](https://leetcode.com/problems/richest-customer-wealth/description/). Read the problem description and work through the provided examples by hand. We will work backwards from desirable invariants to build a solution.

State preconditions and postconditions. These come directly from the problem statement.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty grid `accounts`.
        """
        # `accounts` is a non-empty grid.
```

Define a desirable termination invariant. This comes directly from the postcondition.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty grid `accounts`.
        """
        # `accounts` is a non-empty grid.
        ...
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

Define a loop invariant that will eventually transform into the termination invariant. A **prefix** is a contiguous portion of an array that starts from the first element. Loop invariants are often stated in terms of an array prefix. In this case, the prefix `accounts[:i]` (i.e. all values in `accounts` up to but not including `accounts[i]`). After the loop terminates, the prefix will contain all values (i.e. `accounts[:m] == accounts`), and so the termination invariant will be maintained. Initializing `max_wealth_so_far` to `0` is correct since the precondition states all accounts contain positive wealth and there will always be at least one account. This means `max_wealth_so_far` will always be replaced with the wealth of `accounts[0]` after the first iteration.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        m = len(accounts)
        n = len(accounts[0])
        # `accounts` is a non-empty positive grid.
        # `max_wealth_so_far` is the maximum wealth in `accounts[:i]`.
        max_wealth_so_far = 0
        for i in range(m):
            ...
        # `max_wealth_so_far` is the maximum wealth in non-empty grid `accounts`.
        return max_wealth_so_far
```

Fill in the loop body to maintain the loop invariant. Again, start with a desirable termination invariant. 
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        m = len(accounts)
        n = len(accounts[0])
        # `accounts` is a non-empty positive grid.
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

Define a loop invariant that will eventually transform into the termination invariant. Again, 
the invariant can be stated in terms of an array prefix. After the loop terminates, `wealth_so_far` will be the total wealth in `accounts[i][:n] == accounts[i]`, which is exactly what we want. Initially, the total wealth in `accounts[i][:0] == []` is `0`, so `wealth_so_far` must start at `0`.
```py
class Solution:
    def maximumWealth(self, accounts: List[List[int]]) -> int:
        """
        `maximumWealth(accounts)` is the maximum wealth in non-empty positive grid `accounts`.
        """
        m = len(accounts)
        n = len(accounts[0])
        # `accounts` is a non-empty positive grid.
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
