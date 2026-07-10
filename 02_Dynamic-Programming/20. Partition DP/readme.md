# Partition DP (Advanced MCM)

## Introduction
Partition DP is a generalization of the Matrix Chain Multiplication (MCM) pattern. It applies when we need to partition an array or string into chunks, or merge adjacent elements, and we want to find the optimal way to do this. The problem usually inherently forms a binary tree structure of decisions.

This is a classic $O(N^3)$ dynamic programming pattern. It's often required in FAANG interviews (like Google and Meta).

## Core Concepts
Instead of iterating left to right, we define a range `[i, j]` and guess the last action to be performed in that range (e.g., which balloon to burst last, where to make the first cut).
We then recursively solve for the subproblems `[i, k]` and `[k+1, j]`.

## Example Standard Template (Burst Balloons)
```python
# To maximize coins from bursting balloons, we add dummy balloons with value 1 at ends.
# dp(i, j) represents max coins obtainable from bursting balloons in (i, j) EXCLUSIVE.
memo = {}
def dp(i, j):
    # Base case: no balloons between i and j
    if i + 1 == j:
        return 0
    if (i, j) in memo:
        return memo[(i, j)]
        
    ans = 0
    # k is the index of the LAST balloon to burst in (i, j)
    for k in range(i + 1, j):
        # The coins we get from bursting k last are:
        # nums[i] * nums[k] * nums[j]
        # plus whatever we got from bursting balloons on the left and right of k
        coins = nums[i] * nums[k] * nums[j] + dp(i, k) + dp(k, j)
        ans = max(ans, coins)
        
    memo[(i, j)] = ans
    return ans
```

## Top LeetCode Questions
- [312. Burst Balloons](https://leetcode.com/problems/burst-balloons/) (Hard)
- [1547. Minimum Cost to Cut a Stick](https://leetcode.com/problems/minimum-cost-to-cut-a-stick/) (Hard)
- [1000. Minimum Cost to Merge Stones](https://leetcode.com/problems/minimum-cost-to-merge-stones/) (Hard)
- [1039. Minimum Score Triangulation of Polygon](https://leetcode.com/problems/minimum-score-triangulation-of-polygon/) (Medium)
- [87. Scramble String](https://leetcode.com/problems/scramble-string/) (Hard)
