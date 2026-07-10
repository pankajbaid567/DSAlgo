# Digit DP

## Introduction
Digit DP is a technique used to count the number of integers in a given range `[L, R]` that satisfy a specific property (e.g., sum of digits is prime, no repeating digits, specific digits allowed). 
Because `R` can be up to $10^{18}$ (or string length up to 100), checking every number is $O(R)$, which will time out. Digit DP solves this in $O(\log R)$ or $O(\text{length of string})$.

## Core Concepts
Instead of iterating numbers, we construct numbers digit by digit from left to right.
We use a function like `solve(R)` which calculates the valid numbers in `[0, R]`.
The answer for `[L, R]` is usually `solve(R) - solve(L - 1)`.

**State Representation:**
1. `idx`: Current digit position we are placing.
2. `tight`: Boolean. If `tight == True`, the prefix we built so far matches the prefix of `R`. We are restricted and can only place digits up to `R[idx]`. If `tight == False`, we can place any digit from `0` to `9`.
3. `leading_zero`: Boolean. Have we only placed zeros so far? (Important for properties like non-repeating digits).
4. `Property-specific state`: E.g., `sum_so_far`, `previous_digit`, `remainder`.

## Example Standard Template
```python
# Counting numbers up to a string upper_bound where digits sum to exactly target_sum
memo = {}
def dp(idx, tight, current_sum, num_str):
    if idx == len(num_str):
        return 1 if current_sum == target_sum else 0
        
    state = (idx, tight, current_sum)
    if state in memo:
        return memo[state]
        
    limit = int(num_str[idx]) if tight else 9
    ans = 0
    
    for digit in range(limit + 1):
        is_tight_now = tight and (digit == limit)
        ans += dp(idx + 1, is_tight_now, current_sum + digit, num_str)
        
    memo[state] = ans
    return ans
```

## Top LeetCode Questions
- [233. Number of Digit One](https://leetcode.com/problems/number-of-digit-one/) (Hard)
- [902. Numbers At Most N Given Digit Set](https://leetcode.com/problems/numbers-at-most-n-given-digit-set/) (Hard)
- [1012. Numbers With Repeated Digits](https://leetcode.com/problems/numbers-with-repeated-digits/) (Hard)
- [2827. Number of Beautiful Integers in the Range](https://leetcode.com/problems/number-of-beautiful-integers-in-the-range/) (Hard)
