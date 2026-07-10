# Game Theory DP

## Introduction
Game Theory DP is frequently asked in product-based companies and FAANG. These problems usually involve two players playing optimally. The goal is to determine if the first player can win, or to find the maximum score the first player can achieve.

## Core Concepts
1. **Minimax Algorithm**:
   - Player 1 tries to **maximize** their score (or chance of winning).
   - Player 2 tries to **minimize** Player 1's score (which is equivalent to maximizing their own).
2. **State Representation**:
   - Usually represented by the remaining elements (e.g., `(left, right)` pointers in an array, or remaining stones).
   - Sometimes an extra parameter `is_player1_turn` (boolean) is added, or the return value is generalized as "max relative score" (Player 1's score - Player 2's score).

## Example Standard Template (Minimax - Maximize relative score)
```python
# Array of stones, players can pick from left or right.
memo = {}
def max_score_diff(left, right):
    if left > right:
        return 0
    if (left, right) in memo:
        return memo[(left, right)]
        
    # If I pick left, my relative score increases by stones[left], and opponent will do their best in the remaining array, which decreases my relative score by max_score_diff(left + 1, right)
    pick_left = stones[left] - max_score_diff(left + 1, right)
    pick_right = stones[right] - max_score_diff(left, right - 1)
    
    ans = max(pick_left, pick_right)
    memo[(left, right)] = ans
    return ans

# Player 1 wins if max_score_diff(0, n-1) >= 0
```

## Top LeetCode Questions
- [292. Nim Game](https://leetcode.com/problems/nim-game/) (Easy - Math/Game Theory)
- [486. Predict the Winner](https://leetcode.com/problems/predict-the-winner/) (Medium)
- [877. Stone Game](https://leetcode.com/problems/stone-game/) (Medium)
- [1140. Stone Game II](https://leetcode.com/problems/stone-game-ii/) (Medium)
- [1406. Stone Game III](https://leetcode.com/problems/stone-game-iii/) (Hard)
- [1510. Stone Game IV](https://leetcode.com/problems/stone-game-iv/) (Hard)
