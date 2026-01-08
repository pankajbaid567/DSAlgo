# 🔥 String - Hard Problems Collection

A comprehensive collection of challenging String problems with complete solutions, advanced algorithms (Z-algorithm, Manacher's), and detailed explanations.

---

## 📚 Table of Contents

1. [Shortest Palindrome](#problem-1-shortest-palindrome)
2. [Edit Distance](#problem-2-edit-distance)
3. [Regular Expression Matching](#problem-3-regular-expression-matching)
4. [Wildcard Matching](#problem-4-wildcard-matching)
5. [Distinct Subsequences](#problem-5-distinct-subsequences)
6. [Interleaving String](#problem-6-interleaving-string)
7. [Minimum Window Substring](#problem-7-minimum-window-substring)
8. [Longest Palindromic Substring (Manacher's)](#problem-8-longest-palindromic-substring-manachers-algorithm)
9. [Pattern Summary](#-pattern-summary)
10. [Advanced Algorithms](#-advanced-algorithms-z-algorithm--manachers)

---

## Problem 1: Shortest Palindrome

**LeetCode 214 - Hard**

### Problem Statement
Find shortest palindrome by adding characters in front of string.

```
Input: s = "aacecaaa"
Output: "aaacecaaa"

Input: s = "abcd"
Output: "dcbabcd"
```

### 🎯 Intuition
**KMP-based approach:**
1. Find longest palindrome prefix
2. Reverse remaining suffix
3. Prepend to original string

**Key insight:** Use KMP to find longest prefix that's also suffix of `s + '#' + reverse(s)`

### 📊 Visual Representation

```
s = "aacecaaa"
reverse = "aaacecaa"

Combined: "aacecaaa#aaacecaa"

KMP table finds overlap:
  "aacecaaa" has prefix "aaa" = suffix "aaa" of reverse
  
Longest palindrome prefix: "aacecaa"
Remaining: "a"
Prepend reverse of "a" = "a"

Result: "aaacecaaa"
```

### Solution 1: KMP Approach

```python
def shortestPalindrome(s):
    """
    KMP for finding longest palindrome prefix.
    
    Logic:
    - Build KMP table for s + '#' + reverse(s)
    - Table[end] = longest prefix = suffix
    - Prepend reverse of remaining part
    
    Time: O(n)
    Space: O(n)
    """
    if not s:
        return s
    
    # Create combined string
    rev = s[::-1]
    combined = s + '#' + rev
    n = len(combined)
    
    # Build KMP table
    lps = [0] * n
    j = 0
    
    for i in range(1, n):
        while j > 0 and combined[i] != combined[j]:
            j = lps[j - 1]
        
        if combined[i] == combined[j]:
            j += 1
            lps[i] = j
        else:
            lps[i] = 0
    
    # lps[n-1] = length of longest palindrome prefix
    palindrome_len = lps[n - 1]
    
    # Add reverse of non-palindrome part
    to_add = rev[:len(s) - palindrome_len]
    
    return to_add + s

# Example usage
print(shortestPalindrome("aacecaaa"))  # "aaacecaaa"
print(shortestPalindrome("abcd"))      # "dcbabcd"
```

### Solution 2: Rolling Hash

```python
def shortestPalindrome_hash(s):
    """
    Rolling hash to find palindrome.
    
    Time: O(n)
    Space: O(1)
    """
    n = len(s)
    base = 29
    mod = 10**9 + 7
    
    forward_hash = 0
    reverse_hash = 0
    power = 1
    palindrome_end = 0
    
    for i in range(n):
        char_val = ord(s[i]) - ord('a') + 1
        
        # Forward hash
        forward_hash = (forward_hash * base + char_val) % mod
        
        # Reverse hash
        reverse_hash = (reverse_hash + char_val * power) % mod
        power = (power * base) % mod
        
        # Check if palindrome
        if forward_hash == reverse_hash:
            palindrome_end = i
    
    # Add reverse of remaining part
    to_add = s[palindrome_end + 1:][::-1]
    return to_add + s
```

### Solution 3: Manacher's Algorithm

```python
def shortestPalindrome_manacher(s):
    """
    Use Manacher's to find longest palindrome prefix.
    
    Time: O(n)
    Space: O(n)
    """
    if not s:
        return s
    
    # Transform string
    t = '#'.join('^{}$'.format(s))
    n = len(t)
    p = [0] * n
    center = right = 0
    max_len = 0
    
    for i in range(1, n - 1):
        if i < right:
            p[i] = min(right - i, p[2 * center - i])
        
        # Expand around i
        while t[i + p[i] + 1] == t[i - p[i] - 1]:
            p[i] += 1
        
        if i + p[i] > right:
            center, right = i, i + p[i]
        
        # Check if palindrome starts at beginning
        if i - p[i] == 1:
            max_len = max(max_len, p[i])
    
    # Add reverse of non-palindrome part
    to_add = s[max_len:][::-1]
    return to_add + s
```

### 🔍 Dry Run (KMP)

```
s = "aacecaaa"
rev = "aaacecaa"
combined = "aacecaaa#aaacecaa"

Building KMP table:
  i=0: lps[0]=0
  i=1: a==a, j=1, lps[1]=1
  i=2: c!=a, j=0, c!=a, lps[2]=0
  ...
  i=17: a==a, j=3, lps[17]=3

lps[17]=3 → palindrome_len=3
to_add = rev[:8-3] = "aaace"[:5] = "a"

Result: "a" + "aacecaaa" = "aaacecaaa"
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| KMP | O(n) | O(n) | ⭐ Most reliable |
| Rolling Hash | O(n) | O(1) | Collision risk |
| Manacher's | O(n) | O(n) | Elegant solution |

---

## Problem 2: Edit Distance

**LeetCode 72 - Hard**

### Problem Statement
Find minimum operations (insert, delete, replace) to convert word1 to word2.

```
Input: word1 = "horse", word2 = "ros"
Output: 3
Explanation: 
  horse -> rorse (replace 'h' with 'r')
  rorse -> rose (remove 'r')
  rose -> ros (remove 'e')
```

### 🎯 Intuition
**Classic DP (Levenshtein Distance):**
- `dp[i][j]` = min operations for word1[0:i] → word2[0:j]
- Three choices: insert, delete, replace
- If chars match: no operation needed

### 📊 Visual Representation

```
word1 = "horse", word2 = "ros"

DP Table:
      ""  r  o  s
  ""   0  1  2  3
  h    1  1  2  3
  o    2  2  1  2
  r    3  2  2  2
  s    4  3  3  2
  e    5  4  4  3

Bottom-right cell = answer: 3
```

### Solution

```python
def minDistance(word1, word2):
    """
    DP for edit distance.
    
    Logic:
    - dp[i][j] = min ops for word1[0:i] → word2[0:j]
    - If match: dp[i][j] = dp[i-1][j-1]
    - Else: min(insert, delete, replace) + 1
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Initialize base cases
    for i in range(m + 1):
        dp[i][0] = i  # Delete all from word1
    for j in range(n + 1):
        dp[0][j] = j  # Insert all from word2
    
    # Fill DP table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]  # No operation
            else:
                dp[i][j] = 1 + min(
                    dp[i-1][j],     # Delete from word1
                    dp[i][j-1],     # Insert to word1
                    dp[i-1][j-1]    # Replace
                )
    
    return dp[m][n]

# Example usage
print(minDistance("horse", "ros"))     # Output: 3
print(minDistance("intention", "execution"))  # Output: 5
```

### Space Optimized: O(n)

```python
def minDistance_optimized(word1, word2):
    """
    Use only two rows for DP.
    
    Time: O(m*n)
    Space: O(n)
    """
    m, n = len(word1), len(word2)
    
    if m < n:
        word1, word2 = word2, word1
        m, n = n, m
    
    prev = list(range(n + 1))
    curr = [0] * (n + 1)
    
    for i in range(1, m + 1):
        curr[0] = i
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                curr[j] = prev[j-1]
            else:
                curr[j] = 1 + min(prev[j], curr[j-1], prev[j-1])
        prev, curr = curr, prev
    
    return prev[n]
```

### With Path Reconstruction

```python
def minDistance_with_path(word1, word2):
    """
    Return operations sequence.
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n = len(word1), len(word2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(m + 1):
        dp[i][0] = i
    for j in range(n + 1):
        dp[0][j] = j
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if word1[i-1] == word2[j-1]:
                dp[i][j] = dp[i-1][j-1]
            else:
                dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
    
    # Reconstruct path
    operations = []
    i, j = m, n
    
    while i > 0 or j > 0:
        if i == 0:
            operations.append(f"Insert '{word2[j-1]}'")
            j -= 1
        elif j == 0:
            operations.append(f"Delete '{word1[i-1]}'")
            i -= 1
        elif word1[i-1] == word2[j-1]:
            i -= 1
            j -= 1
        else:
            if dp[i][j] == dp[i-1][j-1] + 1:
                operations.append(f"Replace '{word1[i-1]}' with '{word2[j-1]}'")
                i -= 1
                j -= 1
            elif dp[i][j] == dp[i-1][j] + 1:
                operations.append(f"Delete '{word1[i-1]}'")
                i -= 1
            else:
                operations.append(f"Insert '{word2[j-1]}'")
                j -= 1
    
    return dp[m][n], operations[::-1]

# Example
distance, ops = minDistance_with_path("horse", "ros")
print(f"Distance: {distance}")
print("Operations:", ops)
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| 2D DP | O(m*n) | O(m*n) | ⭐ Clear |
| 1D DP | O(m*n) | O(n) | Space optimal |
| With Path | O(m*n) | O(m*n) | Full trace |

---

## Problem 3: Regular Expression Matching

**LeetCode 10 - Hard**

### Problem Statement
Implement regex with `.` (any char) and `*` (0+ of preceding).

```
Input: s = "aa", p = "a*"
Output: true

Input: s = "mississippi", p = "mis*is*p*."
Output: false
```

### Solution

```python
def isMatch(s, p):
    """
    DP for regex matching.
    
    Logic:
    - dp[i][j] = s[0:i] matches p[0:j]
    - Handle * for 0 or more matches
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Handle patterns like a*, a*b*, etc.
    for j in range(2, n + 1):
        if p[j-1] == '*':
            dp[0][j] = dp[0][j-2]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j-1] == '*':
                # Match 0 times or 1+ times
                dp[i][j] = dp[i][j-2] or \
                          (dp[i-1][j] and (s[i-1] == p[j-2] or p[j-2] == '.'))
            else:
                # Must match current
                dp[i][j] = dp[i-1][j-1] and (s[i-1] == p[j-1] or p[j-1] == '.')
    
    return dp[m][n]
```

### ⏱️ Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time | O(m*n) |
| Space | O(m*n) |

---

## Problem 4: Wildcard Matching

**LeetCode 44 - Hard**

### Problem Statement
Implement wildcard matching with `?` (any char) and `*` (any sequence).

```
Input: s = "adceb", p = "*a*b"
Output: true
```

### Solution

```python
def isMatch_wildcard(s, p):
    """
    DP for wildcard matching.
    
    Logic:
    - dp[i][j] = s[0:i] matches p[0:j]
    - * can match empty or any sequence
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Handle leading *
    for j in range(1, n + 1):
        if p[j-1] == '*':
            dp[0][j] = dp[0][j-1]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j-1] == '*':
                dp[i][j] = dp[i-1][j] or dp[i][j-1]
            elif p[j-1] == '?' or s[i-1] == p[j-1]:
                dp[i][j] = dp[i-1][j-1]
    
    return dp[m][n]
```

### Greedy Two-Pointer

```python
def isMatch_wildcard_greedy(s, p):
    """
    Greedy matching with backtracking.
    
    Time: O(m*n) worst, O(m+n) average
    Space: O(1)
    """
    i = j = 0
    star_idx = -1
    match = 0
    
    while i < len(s):
        if j < len(p) and (p[j] == s[i] or p[j] == '?'):
            i += 1
            j += 1
        elif j < len(p) and p[j] == '*':
            star_idx = j
            match = i
            j += 1
        elif star_idx != -1:
            j = star_idx + 1
            match += 1
            i = match
        else:
            return False
    
    while j < len(p) and p[j] == '*':
        j += 1
    
    return j == len(p)
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| DP | O(m*n) | O(m*n) | Reliable |
| Greedy | O(m*n) | O(1) | ⭐ Space optimal |

---

## Problem 5: Distinct Subsequences

**LeetCode 115 - Hard**

### Problem Statement
Count distinct subsequences of `s` that equal `t`.

```
Input: s = "rabbbit", t = "rabbit"
Output: 3
Explanation: 
  rab-b-bit
  ra-bb-bit
  rab-bb-it
```

### Solution

```python
def numDistinct(s, t):
    """
    DP for counting subsequences.
    
    Logic:
    - dp[i][j] = count of t[0:j] in s[0:i]
    - If match: count with + count without
    - If no match: count without
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty t matches in 1 way
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            # Don't use s[i-1]
            dp[i][j] = dp[i-1][j]
            
            # Use s[i-1] if match
            if s[i-1] == t[j-1]:
                dp[i][j] += dp[i-1][j-1]
    
    return dp[m][n]

# Example usage
print(numDistinct("rabbbit", "rabbit"))  # Output: 3
```

### Space Optimized

```python
def numDistinct_optimized(s, t):
    """
    Use 1D DP array.
    
    Time: O(m*n)
    Space: O(n)
    """
    n = len(t)
    dp = [0] * (n + 1)
    dp[0] = 1
    
    for char in s:
        for j in range(n, 0, -1):
            if char == t[j-1]:
                dp[j] += dp[j-1]
    
    return dp[n]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| 2D DP | O(m*n) | O(m*n) | Clear |
| 1D DP | O(m*n) | O(n) | ⭐ Optimal |

---

## Problem 6: Interleaving String

**LeetCode 97 - Hard**

### Problem Statement
Check if `s3` is formed by interleaving `s1` and `s2`.

```
Input: s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
Output: true
```

### Solution

```python
def isInterleave(s1, s2, s3):
    """
    DP for interleaving check.
    
    Logic:
    - dp[i][j] = s1[0:i] + s2[0:j] can form s3[0:i+j]
    - Try taking from s1 or s2
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n, l = len(s1), len(s2), len(s3)
    
    if m + n != l:
        return False
    
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True
    
    # Initialize first row
    for j in range(1, n + 1):
        dp[0][j] = dp[0][j-1] and s2[j-1] == s3[j-1]
    
    # Initialize first column
    for i in range(1, m + 1):
        dp[i][0] = dp[i-1][0] and s1[i-1] == s3[i-1]
    
    # Fill DP table
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            dp[i][j] = (dp[i-1][j] and s1[i-1] == s3[i+j-1]) or \
                       (dp[i][j-1] and s2[j-1] == s3[i+j-1])
    
    return dp[m][n]

# Example usage
print(isInterleave("aabcc", "dbbca", "aadbbcbcac"))  # True
```

### BFS Approach

```python
from collections import deque

def isInterleave_bfs(s1, s2, s3):
    """
    BFS to find valid interleaving.
    
    Time: O(m*n)
    Space: O(m*n)
    """
    m, n, l = len(s1), len(s2), len(s3)
    
    if m + n != l:
        return False
    
    queue = deque([(0, 0)])
    visited = {(0, 0)}
    
    while queue:
        i, j = queue.popleft()
        
        if i + j == l:
            return True
        
        # Try s1
        if i < m and s1[i] == s3[i+j] and (i+1, j) not in visited:
            visited.add((i+1, j))
            queue.append((i+1, j))
        
        # Try s2
        if j < n and s2[j] == s3[i+j] and (i, j+1) not in visited:
            visited.add((i, j+1))
            queue.append((i, j+1))
    
    return False
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| DP | O(m*n) | O(m*n) | ⭐ Clean |
| BFS | O(m*n) | O(m*n) | Alternative |

---

## Problem 7: Minimum Window Substring

**LeetCode 76 - Hard**

### Problem Statement
Find minimum window in `s` containing all characters of `t`.

```
Input: s = "ADOBECODEBANC", t = "ABC"
Output: "BANC"
```

### Solution

```python
from collections import Counter

def minWindow(s, t):
    """
    Sliding window with character count.
    
    Logic:
    - Expand window until valid
    - Contract while valid
    - Track minimum window
    
    Time: O(m+n)
    Space: O(n)
    """
    if not s or not t:
        return ""
    
    # Count characters in t
    target_count = Counter(t)
    required = len(target_count)
    
    # Window variables
    left = right = 0
    formed = 0
    window_count = {}
    
    # Result
    min_len = float('inf')
    min_left = 0
    
    while right < len(s):
        # Add character from right
        char = s[right]
        window_count[char] = window_count.get(char, 0) + 1
        
        if char in target_count and window_count[char] == target_count[char]:
            formed += 1
        
        # Contract window while valid
        while formed == required and left <= right:
            # Update result
            if right - left + 1 < min_len:
                min_len = right - left + 1
                min_left = left
            
            # Remove character from left
            char = s[left]
            window_count[char] -= 1
            if char in target_count and window_count[char] < target_count[char]:
                formed -= 1
            
            left += 1
        
        right += 1
    
    return "" if min_len == float('inf') else s[min_left:min_left + min_len]

# Example usage
print(minWindow("ADOBECODEBANC", "ABC"))  # "BANC"
```

### ⏱️ Complexity Analysis

| Metric | Complexity | Notes |
|--------|------------|-------|
| Time | O(m+n) | ⭐ Optimal |
| Space | O(n) | For counters |

---

## Problem 8: Longest Palindromic Substring (Manacher's Algorithm)

**LeetCode 5 - Medium/Hard with optimal solution**

### Problem Statement
Find longest palindromic substring.

```
Input: s = "babad"
Output: "bab" or "aba"
```

### Manacher's Algorithm

```python
def longestPalindrome_manacher(s):
    """
    Manacher's algorithm for O(n) palindrome finding.
    
    Logic:
    - Transform string to avoid even/odd cases
    - Use center and right boundary
    - Exploit palindrome symmetry
    
    Time: O(n)
    Space: O(n)
    """
    # Transform: "babad" -> "^#b#a#b#a#d#$"
    t = '#'.join('^{}$'.format(s))
    n = len(t)
    p = [0] * n  # p[i] = radius of palindrome at i
    center = right = 0
    
    for i in range(1, n - 1):
        # Mirror of i about center
        mirror = 2 * center - i
        
        # If within right boundary, use mirror info
        if i < right:
            p[i] = min(right - i, p[mirror])
        
        # Expand around i
        try:
            while t[i + p[i] + 1] == t[i - p[i] - 1]:
                p[i] += 1
        except:
            pass
        
        # Update center and right if expanded past
        if i + p[i] > right:
            center, right = i, i + p[i]
    
    # Find longest palindrome
    max_len = max(p)
    center_idx = p.index(max_len)
    
    # Convert back to original string indices
    start = (center_idx - max_len) // 2
    return s[start:start + max_len]

# Example usage
print(longestPalindrome_manacher("babad"))  # "bab" or "aba"
```

### 🔍 How Manacher's Works

```
Original: "babad"
Transformed: "^#b#a#b#a#d#$"
              0123456789...

p[] array:
  i:  0 1 2 3 4 5 6 7 8 9 10 11 12
  t:  ^ # b # a # b # a # d  #  $
  p:  0 0 1 0 3 0 1 4 1 0 1  0  0

p[7]=4 → longest palindrome centered at t[7]='b'
  Extends 4 positions: "#a#b#a#"
  Original substring: "aba"

Key insight: Use symmetry to avoid recomputation
  If i is within right boundary:
    p[i] = min(p[mirror], right - i)
    Then expand if possible
```

### Alternative: Expand Around Center (O(n²))

```python
def longestPalindrome_expand(s):
    """
    Expand around each center.
    
    Time: O(n²)
    Space: O(1)
    """
    if not s:
        return ""
    
    def expand(left, right):
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return right - left - 1
    
    start = end = 0
    
    for i in range(len(s)):
        # Odd length
        len1 = expand(i, i)
        # Even length
        len2 = expand(i, i + 1)
        max_len = max(len1, len2)
        
        if max_len > end - start:
            start = i - (max_len - 1) // 2
            end = i + max_len // 2
    
    return s[start:end + 1]
```

### ⏱️ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Manacher's | O(n) | O(n) | ⭐ Optimal |
| Expand | O(n²) | O(1) | Simple |
| DP | O(n²) | O(n²) | Educational |

---

## 🎯 Pattern Summary

### Core String Patterns

1. **KMP/Z-Algorithm** - Pattern matching in O(n)
2. **Manacher's** - Palindromes in O(n)
3. **Rolling Hash** - Fast string comparison
4. **DP** - Edit distance, subsequences
5. **Sliding Window** - Substring problems
6. **Two Pointers** - In-place manipulation

### Problem Types

| Pattern | Problems | Key Technique |
|---------|----------|---------------|
| Prefix/Suffix | KMP, Z-algo | LPS array |
| Palindrome | Manacher's | Center expansion |
| Matching | Regex, Wildcard | DP states |
| Distance | Edit, Levenshtein | Min operations |
| Window | Min substring | Expand-contract |

---

## 🔬 Advanced Algorithms: Z-Algorithm & Manacher's

### Z-Algorithm

**Purpose:** Find all occurrences of pattern in text in O(n+m) time.

**Concept:** Z[i] = length of longest substring starting at i matching prefix.

```python
def z_algorithm(s):
    """
    Compute Z array for string s.
    
    Z[i] = length of longest substring starting at i
           that matches prefix of s
    
    Example: s = "aabcaabxaaz"
    Z = [11, 1, 0, 0, 3, 1, 0, 0, 2, 1, 0]
         ^           ^        ^
         |           |        |
      full match  "aab"     "aa"
    
    Time: O(n)
    Space: O(n)
    """
    n = len(s)
    z = [0] * n
    z[0] = n
    
    left = right = 0
    
    for i in range(1, n):
        if i > right:
            # Outside current Z-box, explicit comparison
            left = right = i
            while right < n and s[right] == s[right - left]:
                right += 1
            z[i] = right - left
            right -= 1
        else:
            # Inside Z-box, use symmetry
            k = i - left
            if z[k] < right - i + 1:
                z[i] = z[k]
            else:
                left = i
                while right < n and s[right] == s[right - left]:
                    right += 1
                z[i] = right - left
                right -= 1
    
    return z

# Example usage
print(z_algorithm("aabcaabxaaz"))
# Output: [11, 1, 0, 0, 3, 1, 0, 0, 2, 1, 0]
```

### Pattern Matching with Z-Algorithm

```python
def pattern_search_z(text, pattern):
    """
    Find all occurrences of pattern in text.
    
    Logic:
    - Create combined = pattern + '$' + text
    - Find Z values
    - Z[i] == len(pattern) → match found
    
    Time: O(n+m)
    Space: O(n+m)
    """
    combined = pattern + '$' + text
    z = z_algorithm(combined)
    
    matches = []
    pattern_len = len(pattern)
    
    for i in range(pattern_len + 1, len(combined)):
        if z[i] == pattern_len:
            # Match at position (i - pattern_len - 1) in text
            matches.append(i - pattern_len - 1)
    
    return matches

# Example
text = "abcabcabc"
pattern = "abc"
print(pattern_search_z(text, pattern))  # [0, 3, 6]
```

### When to Use Z-Algorithm vs KMP

| Aspect | Z-Algorithm | KMP |
|--------|-------------|-----|
| Conceptual | Simpler | More complex |
| Preprocessing | Z array | LPS array |
| Multiple patterns | Better | Standard |
| Implementation | Cleaner code | More bookkeeping |
| Use case | Single pattern, multiple texts | Stream processing |

### Manacher's Algorithm (Detailed)

**Purpose:** Find longest palindrome in O(n) time.

**Key Insight:** Use symmetry around centers to avoid recomputation.

```python
def manacher_detailed(s):
    """
    Complete Manacher's with explanation.
    
    Time: O(n)
    Space: O(n)
    """
    # Step 1: Transform string
    # "abc" -> "^#a#b#c#$"
    # Adds sentinels and separators
    t = '#'.join('^{}$'.format(s))
    n = len(t)
    
    # p[i] = radius of palindrome centered at i
    p = [0] * n
    
    # center = center of rightmost palindrome
    # right = right boundary of rightmost palindrome
    center = right = 0
    
    for i in range(1, n - 1):
        # Mirror of i about center
        mirror = 2 * center - i
        
        # If i is within rightmost palindrome
        if i < right:
            # Copy from mirror (symmetry)
            # But cap at boundary
            p[i] = min(right - i, p[mirror])
        
        # Try to expand palindrome around i
        # This is the only "real work"
        try:
            while t[i + p[i] + 1] == t[i - p[i] - 1]:
                p[i] += 1
        except IndexError:
            pass
        
        # If expanded past right, update center/right
        if i + p[i] > right:
            center = i
            right = i + p[i]
    
    # Find maximum palindrome
    max_len = max(p)
    center_idx = p.index(max_len)
    
    # Convert to original string
    start = (center_idx - max_len) // 2
    return s[start:start + max_len], max_len

# Example
text = "babad"
palindrome, length = manacher_detailed(text)
print(f"Longest palindrome: '{palindrome}', length: {length}")
```

### Manacher's Step-by-Step

```
Original: "aba"
Transform: "^#a#b#a#$"
            0123456789

Iteration:
  i=1 ('#'): p[1]=0, center=1, right=1
  i=2 ('a'): 
    mirror=0, p[mirror]=0
    Expand: t[3]='#' != t[1]='#' (wait, equal!)
    Continue: t[4]='b' != t[0]='^', stop
    p[2]=1, update center=2, right=3
  
  i=3 ('#'):
    mirror=1, i<right, p[3]=min(right-i, p[1])=min(0,0)=0
    Expand: t[4]='b' == t[2]='a'? No
    p[3]=0
  
  i=4 ('b'):
    mirror=0, i>right (4>3)
    Expand: t[5]='#' != t[3]='#' (equal!)
    Continue: t[6]='a' == t[2]='a', yes!
    Continue: t[7]='#' == t[1]='#', yes!
    Continue: t[8]='$' != t[0]='^', stop
    p[4]=3, center=4, right=7

Result: p=[0,0,1,0,3,0,1,0,0]
        max=3 at i=4
        Original: (4-3)//2 to (4+3)//2 = 0 to 3 → "aba"
```

### Applications

**Z-Algorithm:**
- Pattern matching (alternative to KMP)
- Finding all occurrences
- Substring problems
- Period detection

**Manacher's:**
- Longest palindromic substring
- Count palindromes
- Palindrome partitioning optimization
- Shortest palindrome (with KMP)

---

## 🎓 Interview Tips

### String Problem Checklist

1. **Preprocessing:**
   - Transform string for easier processing?
   - Build auxiliary data structure (Trie, suffix array)?

2. **Algorithm Selection:**
   - Pattern matching → KMP or Z-algorithm
   - Palindrome → Manacher's or expand
   - Distance/matching → DP
   - Substring → Sliding window

3. **Complexity:**
   - Can we do better than O(n²)?
   - Space-time tradeoff available?

4. **Edge Cases:**
   - Empty strings
   - Single character
   - All same characters
   - No match found

### Common Pitfalls

1. **String immutability** in Python (use list for modifications)
2. **Index off-by-one** errors in DP
3. **Forgetting base cases** in recursion
4. **Not handling empty patterns**
5. **Integer overflow** in rolling hash

Master these advanced string algorithms for success! 🚀

