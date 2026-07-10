# Probability and Expected Value DP

## Introduction
This pattern is overwhelmingly popular in Quant firm interviews (Jane Street, Optiver, Citadel, Tower Research) and occasionally in standard FAANG interviews.
Instead of finding the minimum, maximum, or total number of ways, you are tasked with finding the **expected value** (e.g., expected number of steps, expected payoff) or the **probability** of reaching a specific state.

## Mathematical Core
1. **Probability**:
   `P(State) = Sum( P(NextState) * Probability of Transition )`
   *Remember that base cases usually have probability 1 (target reached) or 0 (failed).*

2. **Expected Value**:
   Expected number of steps: `E(State) = 1 + Sum( E(NextState) * Probability of Transition )`
   *You add 1 for the current step, plus the expected steps from the next states weighted by their probabilities.*

## Identification
- The problem explicitly asks for "Probability of...", "Expected number of...", or "Average...".
- Often involves dice rolls, coin flips, random choices, or random walks.

## Example Standard Template (Expected Steps)
```python
# Finding expected number of steps to reach 0 from N.
# At each step, we can either subtract 1 (prob p) or 2 (prob 1-p).
memo = {}
def expected_steps(n):
    if n <= 0:
        return 0
    if n in memo:
        return memo[n]
        
    # E[n] = 1 (current step) + p * E[n-1] + (1-p) * E[n-2]
    ans = 1 + p * expected_steps(n - 1) + (1 - p) * expected_steps(n - 2)
    memo[n] = ans
    return ans
```

## Top LeetCode Questions
- [688. Knight Probability in Chessboard](https://leetcode.com/problems/knight-probability-in-chessboard/) (Medium)
- [808. Soup Servings](https://leetcode.com/problems/soup-servings/) (Medium)
- [837. New 21 Game](https://leetcode.com/problems/new-21-game/) (Medium/Hard)
- [1227. Airplane Seat Assignment Probability](https://leetcode.com/problems/airplane-seat-assignment-probability/) (Medium - Math/DP)
- [1467. Probability of a Two Boxes Having The Same Number of Distinct Balls](https://leetcode.com/problems/probability-of-a-two-boxes-having-the-same-number-of-distinct-balls/) (Hard)
