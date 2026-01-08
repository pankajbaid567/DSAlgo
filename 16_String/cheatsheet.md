# 🔤 String Algorithms - Comprehensive Cheatsheet

## 📚 Core Concepts

**Strings**: Sequences of characters - fundamental in programming interviews.

### Common Patterns
1. Two Pointers / Sliding Window
2. HashMap / Counter
3. String Matching (KMP, Rabin-Karp)
4. Palindromes
5. Anagrams
6. Subsequences vs Substrings

---

## Essential String Operations

### Python String Methods
```python
s = "Hello World"

# Basic operations
s.lower()          # "hello world"
s.upper()          # "HELLO WORLD"
s.strip()          # Remove whitespace
s.split()          # ["Hello", "World"]
s.replace("o", "0") # "Hell0 W0rld"

# Searching
s.find("World")    # 6 (index, -1 if not found)
s.index("World")   # 6 (raises error if not found)
s.count("l")       # 3

# Checking
s.startswith("He") # True
s.endswith("ld")   # True
s.isalpha()        # False (has space)
s.isdigit()        # False
s.isalnum()        # False

# Joining
"-".join(["a", "b", "c"])  # "a-b-c"

# Character operations
ord('a')           # 97 (ASCII value)
chr(97)            # 'a'
```

---

## Pattern 1: Palindromes

### Check Palindrome
```python
def isPalindrome(s):
    """Two pointers approach"""
    left, right = 0, len(s) - 1
    
    while left < right:
        # Skip non-alphanumeric
        while left < right and not s[left].isalnum():
            left += 1
        while left < right and not s[right].isalnum():
            right -= 1
        
        if s[left].lower() != s[right].lower():
            return False
        
        left += 1
        right -= 1
    
    return True
```

### Longest Palindromic Substring
```python
def longestPalindrome(s):
    """Expand around center"""
    def expand(left, right):
        while left >= 0 and right < len(s) and s[left] == s[right]:
            left -= 1
            right += 1
        return s[left+1:right]
    
    result = ""
    
    for i in range(len(s)):
        # Odd length palindrome
        odd = expand(i, i)
        # Even length palindrome
        even = expand(i, i + 1)
        
        result = max(result, odd, even, key=len)
    
    return result
```

### Palindromic Substrings Count
```python
def countSubstrings(s):
    """Count all palindromic substrings"""
    def count_palindromes(left, right):
        count = 0
        while left >= 0 and right < len(s) and s[left] == s[right]:
            count += 1
            left -= 1
            right += 1
        return count
    
    total = 0
    for i in range(len(s)):
        # Odd length
        total += count_palindromes(i, i)
        # Even length
        total += count_palindromes(i, i + 1)
    
    return total
```

### Palindrome Partitioning
```python
def partition(s):
    """All palindrome partitions"""
    def is_palindrome(s):
        return s == s[::-1]
    
    result = []
    
    def backtrack(start, path):
        if start == len(s):
            result.append(path[:])
            return
        
        for end in range(start + 1, len(s) + 1):
            if is_palindrome(s[start:end]):
                path.append(s[start:end])
                backtrack(end, path)
                path.pop()
    
    backtrack(0, [])
    return result
```

---

## Pattern 2: Anagrams

### Check Anagram
```python
from collections import Counter

def isAnagram(s, t):
    """Using Counter"""
    return Counter(s) == Counter(t)

def isAnagram_sort(s, t):
    """Using sorting"""
    return sorted(s) == sorted(t)

def isAnagram_array(s, t):
    """Using array (for lowercase only)"""
    if len(s) != len(t):
        return False
    
    count = [0] * 26
    
    for i in range(len(s)):
        count[ord(s[i]) - ord('a')] += 1
        count[ord(t[i]) - ord('a')] -= 1
    
    return all(c == 0 for c in count)
```

### Group Anagrams
```python
def groupAnagrams(strs):
    """Group strings by anagram"""
    from collections import defaultdict
    
    groups = defaultdict(list)
    
    for s in strs:
        # Sort as key
        key = ''.join(sorted(s))
        groups[key].append(s)
    
    return list(groups.values())

# Alternative: Use character count as key
def groupAnagrams_count(strs):
    groups = defaultdict(list)
    
    for s in strs:
        count = [0] * 26
        for char in s:
            count[ord(char) - ord('a')] += 1
        
        key = tuple(count)
        groups[key].append(s)
    
    return list(groups.values())
```

### Find All Anagrams
```python
def findAnagrams(s, p):
    """Find all anagram indices of p in s"""
    from collections import Counter
    
    if len(p) > len(s):
        return []
    
    p_count = Counter(p)
    window_count = Counter(s[:len(p)])
    
    result = []
    if window_count == p_count:
        result.append(0)
    
    # Sliding window
    for i in range(len(p), len(s)):
        # Add new character
        window_count[s[i]] += 1
        
        # Remove old character
        old_char = s[i - len(p)]
        window_count[old_char] -= 1
        if window_count[old_char] == 0:
            del window_count[old_char]
        
        if window_count == p_count:
            result.append(i - len(p) + 1)
    
    return result
```

---

## Pattern 3: Subsequences

### Longest Common Subsequence (LCS)
```python
def longestCommonSubsequence(text1, text2):
    """DP approach"""
    m, n = len(text1), len(text2)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if text1[i-1] == text2[j-1]:
                dp[i][j] = dp[i-1][j-1] + 1
            else:
                dp[i][j] = max(dp[i-1][j], dp[i][j-1])
    
    return dp[m][n]
```

### Is Subsequence
```python
def isSubsequence(s, t):
    """Two pointers"""
    i = 0
    
    for char in t:
        if i < len(s) and s[i] == char:
            i += 1
    
    return i == len(s)
```

### Number of Distinct Subsequences
```python
def numDistinct(s, t):
    """DP: count ways to form t from s"""
    m, n = len(s), len(t)
    dp = [[0] * (n + 1) for _ in range(m + 1)]
    
    # Empty string is subsequence of any string
    for i in range(m + 1):
        dp[i][0] = 1
    
    for i in range(1, m + 1):
        for j in range(1, n + 1):
            dp[i][j] = dp[i-1][j]
            
            if s[i-1] == t[j-1]:
                dp[i][j] += dp[i-1][j-1]
    
    return dp[m][n]
```

---

## Pattern 4: String Matching

### KMP Algorithm
```python
def KMP(text, pattern):
    """Knuth-Morris-Pratt pattern matching"""
    def compute_lps(pattern):
        """Longest Proper Prefix which is also Suffix"""
        lps = [0] * len(pattern)
        length = 0
        i = 1
        
        while i < len(pattern):
            if pattern[i] == pattern[length]:
                length += 1
                lps[i] = length
                i += 1
            else:
                if length != 0:
                    length = lps[length - 1]
                else:
                    lps[i] = 0
                    i += 1
        
        return lps
    
    if not pattern:
        return 0
    
    lps = compute_lps(pattern)
    i = j = 0
    
    while i < len(text):
        if text[i] == pattern[j]:
            i += 1
            j += 1
        
        if j == len(pattern):
            return i - j  # Found at index
        elif i < len(text) and text[i] != pattern[j]:
            if j != 0:
                j = lps[j - 1]
            else:
                i += 1
    
    return -1  # Not found
```

### Rabin-Karp Algorithm
```python
def rabinKarp(text, pattern):
    """Rolling hash for pattern matching"""
    d = 256  # Number of characters
    q = 101  # Prime number
    m, n = len(pattern), len(text)
    
    if m > n:
        return -1
    
    # Calculate hash for pattern and first window
    p_hash = 0  # Pattern hash
    t_hash = 0  # Text window hash
    h = 1
    
    # h = d^(m-1) % q
    for i in range(m - 1):
        h = (h * d) % q
    
    # Calculate initial hashes
    for i in range(m):
        p_hash = (d * p_hash + ord(pattern[i])) % q
        t_hash = (d * t_hash + ord(text[i])) % q
    
    # Slide pattern over text
    for i in range(n - m + 1):
        # Check if hash matches
        if p_hash == t_hash:
            # Verify character by character
            if text[i:i+m] == pattern:
                return i
        
        # Calculate hash for next window
        if i < n - m:
            t_hash = (d * (t_hash - ord(text[i]) * h) + ord(text[i + m])) % q
            
            # Handle negative hash
            if t_hash < 0:
                t_hash += q
    
    return -1
```

---

## Pattern 5: String Transformations

### String to Integer (atoi)
```python
def myAtoi(s):
    """Convert string to integer"""
    s = s.lstrip()
    
    if not s:
        return 0
    
    sign = 1
    i = 0
    
    if s[0] in ['-', '+']:
        sign = -1 if s[0] == '-' else 1
        i = 1
    
    result = 0
    
    while i < len(s) and s[i].isdigit():
        result = result * 10 + int(s[i])
        i += 1
    
    result *= sign
    
    # Handle overflow
    INT_MAX = 2**31 - 1
    INT_MIN = -2**31
    
    if result > INT_MAX:
        return INT_MAX
    if result < INT_MIN:
        return INT_MIN
    
    return result
```

### Integer to Roman
```python
def intToRoman(num):
    """Convert integer to Roman numeral"""
    values = [1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1]
    symbols = ["M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"]
    
    result = []
    
    for i in range(len(values)):
        count = num // values[i]
        if count:
            result.append(symbols[i] * count)
            num %= values[i]
    
    return ''.join(result)
```

### Roman to Integer
```python
def romanToInt(s):
    """Convert Roman numeral to integer"""
    roman = {'I': 1, 'V': 5, 'X': 10, 'L': 50, 
             'C': 100, 'D': 500, 'M': 1000}
    
    result = 0
    prev = 0
    
    for char in reversed(s):
        value = roman[char]
        
        if value < prev:
            result -= value
        else:
            result += value
        
        prev = value
    
    return result
```

---

## Pattern 6: String Compression & Encoding

### Run-Length Encoding
```python
def compress(chars):
    """In-place compression"""
    write = 0
    i = 0
    
    while i < len(chars):
        char = chars[i]
        count = 0
        
        # Count consecutive characters
        while i < len(chars) and chars[i] == char:
            count += 1
            i += 1
        
        # Write character
        chars[write] = char
        write += 1
        
        # Write count if > 1
        if count > 1:
            for digit in str(count):
                chars[write] = digit
                write += 1
    
    return write

def decompress(s):
    """Decompress run-length encoded string"""
    result = []
    i = 0
    
    while i < len(s):
        char = s[i]
        i += 1
        
        count = ""
        while i < len(s) and s[i].isdigit():
            count += s[i]
            i += 1
        
        count = int(count) if count else 1
        result.append(char * count)
    
    return ''.join(result)
```

### Encode and Decode Strings
```python
def encode(strs):
    """Encode list of strings"""
    return ''.join(f"{len(s)}#{s}" for s in strs)

def decode(s):
    """Decode to list of strings"""
    result = []
    i = 0
    
    while i < len(s):
        # Find length
        j = i
        while s[j] != '#':
            j += 1
        
        length = int(s[i:j])
        i = j + 1
        
        # Extract string
        result.append(s[i:i+length])
        i += length
    
    return result
```

---

## 🎨 Dry Run Example

### KMP Pattern Matching

```
Text: "ABABDABACDABABCABAB"
Pattern: "ABABCABAB"

Step 1: Compute LPS array
Pattern: A B A B C A B A B
LPS:     0 0 1 2 0 1 2 3 4

Step 2: Match pattern
i=0, j=0: A=A, i++, j++ (match)
i=1, j=1: B=B, i++, j++ (match)
i=2, j=2: A=A, i++, j++ (match)
i=3, j=3: B=B, i++, j++ (match)
i=4, j=4: D≠C, j=lps[3]=2 (skip to position 2)
i=4, j=2: D≠A, j=lps[1]=0
i=4, j=0: D≠A, i++ (no match)
...continue until match found at position 10
```

---

## ⏱️ Complexity Analysis

| Pattern | Time | Space |
|---------|------|-------|
| Palindrome Check | O(n) | O(1) |
| Longest Palindrome | O(n²) | O(1) |
| Anagram Check | O(n) | O(1) |
| KMP | O(n + m) | O(m) |
| Rabin-Karp | O(n * m) worst | O(1) |
| LCS | O(n * m) | O(n * m) |

---

## 🎯 Must-Know Problems

### Easy
- [125. Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)
- [242. Valid Anagram](https://leetcode.com/problems/valid-anagram/)
- [387. First Unique Character](https://leetcode.com/problems/first-unique-character-in-a-string/)
- [20. Valid Parentheses](https://leetcode.com/problems/valid-parentheses/)

### Medium
- [3. Longest Substring Without Repeating Characters](https://leetcode.com/problems/longest-substring-without-repeating-characters/)
- [5. Longest Palindromic Substring](https://leetcode.com/problems/longest-palindromic-substring/)
- [49. Group Anagrams](https://leetcode.com/problems/group-anagrams/)
- [438. Find All Anagrams](https://leetcode.com/problems/find-all-anagrams-in-a-string/)
- [647. Palindromic Substrings](https://leetcode.com/problems/palindromic-substrings/)

### Hard
- [76. Minimum Window Substring](https://leetcode.com/problems/minimum-window-substring/)
- [10. Regular Expression Matching](https://leetcode.com/problems/regular-expression-matching/)
- [72. Edit Distance](https://leetcode.com/problems/edit-distance/)

---

## 💡 Pro Tips

1. **Use Counter**: Simplifies frequency counting
2. **Two pointers**: For in-place operations
3. **Sliding window**: For substring problems
4. **DP for subsequences**: Build table carefully
5. **KMP for pattern matching**: Learn LPS computation
6. **ASCII tricks**: `ord()`, `chr()` for character math

---

**Strings are everywhere - master these patterns! 🔤**
