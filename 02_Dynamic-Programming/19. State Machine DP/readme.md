# State Machine DP

## Introduction
State Machine DP (or Finite State Machine DP) is used when the problem transitions between a small set of well-defined states. The most classic example is the "Buy and Sell Stock" series where the states are `(Holding Stock, Not Holding Stock, Cooldown)`.

This pattern goes beyond stock problems and applies whenever the current valid actions depend on a specific "mode" or "state" the system was left in during the previous step.

## Core Concepts
1. **Identify States**: What are the mutually exclusive states at any given step `i`?
2. **Define Transitions**: How do we move from one state to another at step `i+1`? What is the cost/reward?
3. **Space Optimization**: Because step `i` usually only depends on step `i-1`, the space complexity can often be reduced from $O(N \times S)$ to $O(S)$, where $S$ is the number of states.

## Example Standard Template (Buy and Sell Stock with Cooldown)
```python
# State 0: Not holding stock, ready to buy (Rest)
# State 1: Holding stock
# State 2: Just sold stock (Cooldown)

def maxProfit(prices):
    if not prices: return 0
    
    # Base cases for day 0
    hold = -prices[0]
    rest = 0
    sold = float('-inf')
    
    for i in range(1, len(prices)):
        prev_hold, prev_rest, prev_sold = hold, rest, sold
        
        # State transitions
        hold = max(prev_hold, prev_rest - prices[i])
        sold = prev_hold + prices[i]
        rest = max(prev_rest, prev_sold)
        
    return max(rest, sold)
```

## Top LeetCode Questions
- [188. Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/) (Hard)
- [309. Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/) (Medium)
- [552. Student Attendance Record II](https://leetcode.com/problems/student-attendance-record-ii/) (Hard)
- [1911. Maximum Alternating Subsequence Sum](https://leetcode.com/problems/maximum-alternating-subsequence-sum/) (Medium)
