# Dynamic Programming (DP)

> Dynamic Programming solves a problem by breaking it into smaller, overlapping subproblems and reusing their answers instead of recalculating them.

## 1. When should you think about DP?

DP is useful when a problem has both:

- **Overlapping subproblems:** the same smaller problem is solved repeatedly.
- **Optimal substructure:** an optimal answer can be built from answers to smaller subproblems.

Common signals in problem statements:

- Count the number of ways to do something.
- Find the minimum cost, minimum number, maximum profit, or longest sequence.
- Decide whether a target can be formed or a choice is possible.
- Each step depends on a small set of earlier states.

DP is not automatically the right choice for every problem involving recursion. First identify repeated work and a recurrence.

## 2. The core DP recipe

For a new problem, work through these steps in order.

1. **Define the state:** What does `dp[i]` or `dp[i][j]` mean? Write the meaning in plain English.
2. **Write the transition:** How can the current state be calculated from smaller states?
3. **Set base cases:** What are the smallest states whose answers are known directly?
4. **Choose an evaluation method:** Top-down memoization or bottom-up tabulation.
5. **Determine the answer location:** Is the answer `dp[n]`, `dp[n - 1]`, a maximum over states, or something else?
6. **Analyze complexity:** Count the number of states and the work done per state.
7. **Test edge cases:** Empty input, smallest valid input, repeated values, impossible targets, and boundary indices.

A useful rule:

> **DP complexity ≈ number of distinct states × work per state.**

## 3. Recursion, memoization, and tabulation

### Plain recursion

A recursive solution expresses the recurrence directly, but may solve the same subproblem many times.

For Fibonacci:

```py
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

This is easy to understand but has exponential time complexity, approximately **O(2ⁿ)**, because the recursion tree repeats work.

### Top-down DP: memoization

Memoization stores the result of each subproblem after computing it. Later calls reuse the stored answer.

```py
from functools import lru_cache

@lru_cache(maxsize=None)
def fib(n):
    if n <= 1:
        return n
    return fib(n - 1) + fib(n - 2)
```

- **Time:** O(n), because each value from 0 to n is computed once.
- **Space:** O(n) for cached results, plus the recursion stack.

Memoization is often easiest when the recurrence is naturally recursive or only a subset of possible states is reached.

### Bottom-up DP: tabulation

Tabulation computes states in an order that guarantees their dependencies are already known.

```py
def fib(n):
    if n <= 1:
        return n

    dp = [0] * (n + 1)
    dp[1] = 1

    for i in range(2, n + 1):
        dp[i] = dp[i - 1] + dp[i - 2]

    return dp[n]
```

- **Time:** O(n).
- **Space:** O(n).

Tabulation avoids recursion overhead and makes the evaluation order explicit.

### Space-optimized tabulation

If each state depends only on the previous two states, the whole array is unnecessary.

```py
def fib(n):
    if n <= 1:
        return n

    prev2, prev1 = 0, 1

    for _ in range(2, n + 1):
        current = prev1 + prev2
        prev2, prev1 = prev1, current

    return prev1
```

- **Time:** O(n).
- **Extra space:** O(1).

Only optimize space after you understand the state dependencies and can prove older values are no longer needed.

## 4. Example: Climbing Stairs

**Problem:** You can climb either 1 or 2 steps at a time. How many distinct ways can you reach step `n`?

### State and transition

Let `dp[i]` be the number of ways to reach step `i`.

The last move must come from step `i - 1` or step `i - 2`:

```text
dp[i] = dp[i - 1] + dp[i - 2]
```

Base cases:

- `dp[0] = 1`: one way to remain at the starting position (take no steps).
- `dp[1] = 1`: one way to reach the first step.

```py
def climb_stairs(n):
    if n <= 1:
        return 1

    two_steps_back = 1
    one_step_back = 1

    for _ in range(2, n + 1):
        current = one_step_back + two_steps_back
        two_steps_back = one_step_back
        one_step_back = current

    return one_step_back
```

For `n = 4`, the values are 1, 1, 2, 3, 5, so there are **5 ways**.

- **Time:** O(n).
- **Extra space:** O(1).

The recurrence resembles Fibonacci, but the meaning and base cases come from the stairs problem. Always derive them from the problem rather than blindly copying Fibonacci.

## 5. Example: Minimum Coins (Coin Change)

**Problem:** Given coin denominations and a target amount, find the minimum number of coins needed. Each coin may be used unlimited times. Return `-1` if the amount cannot be formed.

Let `dp[a]` mean the minimum coins needed to make amount `a`.

For each coin `c` that does not exceed `a`:

```text
dp[a] = min(dp[a], dp[a - c] + 1)
```

Initialize `dp[0] = 0` and all other amounts to an unreachable sentinel, such as `amount + 1`.

```py
def coin_change(coins, amount):
    dp = [amount + 1] * (amount + 1)
    dp[0] = 0

    for a in range(1, amount + 1):
        for coin in coins:
            if coin <= a:
                dp[a] = min(dp[a], dp[a - coin] + 1)

    return dp[amount] if dp[amount] != amount + 1 else -1
```

- **Time:** O(amount × number of coin denominations).
- **Space:** O(amount).

**Why it works:** To make amount `a` using coin `c` last, first make amount `a - c`, then add one coin. The minimum over all valid last coins gives the optimal answer.

## 6. Example: House Robber

**Problem:** Each house has some money, but adjacent houses cannot both be robbed. Find the maximum amount.

Let `dp[i]` be the maximum money obtainable from the first `i` houses.

For each house, choose between:

- **Skip it:** keep the best answer for the previous house.
- **Rob it:** take its money plus the best answer before the previous house.

The recurrence is:

```text
dp[i] = max(dp[i - 1], dp[i - 2] + nums[i - 1])
```

```py
def rob(nums):
    prev2 = 0
    prev1 = 0

    for money in nums:
        current = max(prev1, prev2 + money)
        prev2, prev1 = prev1, current

    return prev1
```

- **Time:** O(n).
- **Extra space:** O(1).

The state is defined using the first `i` houses, which is why the current house is `nums[i - 1]` in the array-based recurrence. This indexing distinction is a common source of bugs.

## 7. Common DP patterns

### One-dimensional DP

A state is usually indexed by one position, amount, or item count.

Examples:

- Fibonacci and Climbing Stairs
- House Robber
- Minimum Cost Climbing Stairs
- Coin Change

### Grid / two-dimensional DP

A state often describes reaching cell `(r, c)` or solving a problem for prefixes of two sequences.

For paths moving only right and down, if every cell is allowed:

```text
dp[r][c] = dp[r - 1][c] + dp[r][c - 1]
```

The first row and first column need boundary handling. If obstacles exist, blocked cells must contribute zero paths.

### Knapsack / choose-or-skip DP

For each item, decide whether to include it or exclude it.

In **0/1 knapsack**, an item can be used at most once. A common state is `dp[i][capacity]`, the best value using the first `i` items with the given capacity.

- **Skip:** `dp[i - 1][capacity]`
- **Take, if it fits:** item value + `dp[i - 1][capacity - weight]`

A one-dimensional 0/1 knapsack optimization usually iterates capacity **backwards** so an item cannot be reused during the same pass. For unlimited-use variants, forward iteration may be appropriate. Loop direction changes the problem's meaning.

### Subsequence / two-string DP

For **Longest Common Subsequence (LCS)**, `dp[i][j]` can mean the LCS length for the first `i` characters of one string and first `j` characters of another.

- If the last characters match: `1 + dp[i - 1][j - 1]`
- Otherwise: `max(dp[i - 1][j], dp[i][j - 1])`

This pattern also appears in edit distance and related sequence problems, though their transitions differ.

### Interval DP

A state represents a range, such as `dp[left][right]`. Try splitting the interval at every possible middle point and combine the subproblem results. This often appears in matrix-chain multiplication and some palindrome problems.

### State-machine DP

Track a small state for each position, such as whether you are holding a stock, not holding a stock, or are in a cooldown period. Transitions model allowed changes between states.

## 8. How to recognize and build a state

A state must contain enough information to make future decisions correctly, but not unnecessary history.

Ask:

- What information about the past affects the remaining answer?
- If two paths reach the same state, will the remaining problem be identical?
- Can I express the answer using smaller states?
- What is the smallest state that fully describes the subproblem?

For example, in House Robber, the whole list of earlier decisions is unnecessary. The best results for the last two prefixes are enough to compute the next answer.

If the state forgets information that affects future choices, the recurrence will be incorrect. If the state stores too much irrelevant information, the solution may become unnecessarily large.

## 9. Memoization vs. tabulation

| Feature | Memoization | Tabulation |
| --- | --- | --- |
| Direction | Top-down | Bottom-up |
| Evaluation | Recursion + cache | Loops + table |
| States computed | Usually only reached states | Usually all states in the chosen order |
| Main overhead | Recursion stack | Table initialization |
| Best fit | Natural recursive structure, sparse states | Clear dependency order, avoiding recursion |

Both are DP. Neither is universally better.

## 10. Complexity analysis

Suppose there are `n` possible states and each state examines `k` choices:

- **Time:** often O(nk).
- **Space:** often O(n) for a one-dimensional table or O(nm) for an `n`-by-`m` table.
- Add recursion-stack space when using recursive memoization.
- Space optimization may reduce storage if only a few earlier states are required.

Do not confuse the number of recursive calls in an uncached solution with the number of distinct states in a DP solution.

## 11. Common mistakes

- Defining `dp[i]` without explaining what it represents.
- Guessing a recurrence instead of deriving it from the final decision or move.
- Incorrect base cases, especially for empty input and amount zero.
- Reading a table entry before it has been initialized or computed.
- Off-by-one errors when a state counts elements but an array uses zero-based indexes.
- Using the wrong loop direction for 0/1 versus unlimited-use choices.
- Returning the wrong state (for example, `dp[n - 1]` instead of `dp[n]`).
- Using an arbitrary sentinel that is not safe for the problem's constraints.
- Optimizing space before the recurrence is correct.
- Assuming every optimization problem is DP; some are better solved with greedy algorithms, graph algorithms, or other techniques.

## 12. A practical problem-solving template

Write these items in your notes before coding:

1. **State:** `dp[...]` means ...
2. **Transition:** `dp[state] = ...`
3. **Base cases:** ...
4. **Evaluation order:** ...
5. **Answer:** ...
6. **Time complexity:** ...
7. **Space complexity:** ...
8. **Edge cases:** ...

Start with a simple recursive definition if that helps reveal the recurrence. Add memoization to remove repeated work, then consider tabulation and space optimization.

## 13. Practice ladder

Try these in order. For each, define the state and recurrence before writing code.

1. **Fibonacci** — compare plain recursion, memoization, and tabulation.
2. **Climbing Stairs** — count ways using one- or two-step moves.
3. **Min Cost Climbing Stairs** — minimize total cost.
4. **House Robber** — maximize value with no adjacent selections.
5. **Coin Change** — minimum number of coins.
6. **Coin Change II** — count combinations rather than minimize coins.
7. **Unique Paths** — count paths through a grid.
8. **0/1 Knapsack** — maximize value under a capacity.
9. **Longest Common Subsequence** — compare two strings.
10. **Longest Increasing Subsequence** — optimize a sequence under increasing-order constraints.

## Quick revision

- **DP = states + transitions + base cases + an evaluation order.**
- Memoization caches recursive answers; tabulation builds answers iteratively.
- First define what each state means in plain English.
- Count distinct states and work per state to analyze time.
- Optimize space only when dependencies allow it.
- The hardest part is usually finding the right state and transition—not writing the loop.
