# DP on Strings (Advanced)

## Introduction
While basic string DP includes concepts like Longest Common Subsequence (LCS) and Edit Distance, advanced DP on strings explores patterns related to partitioning strings, finding subsequences, and pattern matching. 

## Common Sub-Patterns
1. **Palindrome Partitioning**: Splitting a string into the minimum number of palindromic substrings. Requires precomputing a boolean DP table `isPal[i][j]`.
2. **Distinct Subsequences**: Counting how many times string `T` occurs in string `S` as a subsequence.
3. **Word Break**: Determining if a string can be segmented into a space-separated sequence of dictionary words.
4. **Pattern Matching**: Matching strings against wildcards (`*`, `?`) or regular expressions (`*`, `.`).

## Example Standard Template (Distinct Subsequences)
```python
# Counting occurrences of T in S
memo = {}
def num_distinct(i, j):
    # Base cases
    if j == len(T): return 1 # Matched all of T
    if i == len(S): return 0 # Reached end of S but not T
    
    if (i, j) in memo:
        return memo[(i, j)]
        
    ans = 0
    # Option 1: We can always choose to NOT match S[i] with T[j] and move forward in S
    ans += num_distinct(i + 1, j)
    
    # Option 2: If characters match, we can choose to match them and move forward in both
    if S[i] == T[j]:
        ans += num_distinct(i + 1, j + 1)
        
    memo[(i, j)] = ans
    return ans
```

## Top LeetCode Questions
- [132. Palindrome Partitioning II](https://leetcode.com/problems/palindrome-partitioning-ii/) (Hard)
- [115. Distinct Subsequences](https://leetcode.com/problems/distinct-subsequences/) (Hard)
- [139. Word Break](https://leetcode.com/problems/word-break/) (Medium)
- [140. Word Break II](https://leetcode.com/problems/word-break-ii/) (Hard)
- [44. Wildcard Matching](https://leetcode.com/problems/wildcard-matching/) (Hard)
- [10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/) (Hard)
