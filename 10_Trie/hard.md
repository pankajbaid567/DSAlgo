# 🔥 Trie - Hard Problems Collection

## Problem 1: [212. Word Search II](https://leetcode.com/problems/word-search-ii/)

### Problem Statement
Given a 2D board and a list of words, find all words in the board. Words can be constructed from letters of sequentially adjacent cells (horizontally or vertically).

### Intuition
**Trie + Backtracking**:
1. Build Trie from all words (avoid repeated word checking)
2. DFS from each cell, pruning with Trie
3. Mark cells as visited during search
4. Remove found words from Trie to avoid duplicates

### Visual Representation
```
Board:
[['o','a','a','n'],
 ['e','t','a','e'],
 ['i','h','k','r'],
 ['i','f','l','v']]

Words: ["oath","pea","eat","rain"]

Trie:
       root
      / | \ \
     o  p  e  r
     |  |  |  |
     a  e  a  a
     |  |  |  |
     t  a  t  i
     |        |
     h        n

DFS finds: "oath", "eat"
```

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word = None

def findWords(board, words):
    # Build Trie
    root = TrieNode()
    for word in words:
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.word = word
    
    rows, cols = len(board), len(board[0])
    result = []
    
    def backtrack(r, c, node):
        char = board[r][c]
        
        if char not in node.children:
            return
        
        next_node = node.children[char]
        
        # Found a word
        if next_node.word:
            result.append(next_node.word)
            next_node.word = None  # Avoid duplicates
        
        # Mark as visited
        board[r][c] = '#'
        
        # Explore 4 directions
        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
            nr, nc = r + dr, c + dc
            if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                backtrack(nr, nc, next_node)
        
        # Restore
        board[r][c] = char
        
        # Optimization: remove leaf nodes
        if not next_node.children:
            del node.children[char]
    
    # Start DFS from each cell
    for r in range(rows):
        for c in range(cols):
            if board[r][c] in root.children:
                backtrack(r, c, root)
    
    return result
```

### Dry Run
```
Board:
o a a n
e t a e
i h k r
i f l v

Words: ["oath"]

Build Trie: root → o → a → t → h*

Start at (0,0) 'o':
  board[0][0] in root.children? Yes
  backtrack(0, 0, root)
    char = 'o', next_node = node(a→t→h)
    Mark (0,0) = '#'
    Try (1,0) 'e': 'e' not in node.children of 'o'
    Try (0,1) 'a': 'a' in children!
      backtrack(0, 1, node_o)
        char = 'a', next_node = node(t→h)
        Mark (0,1) = '#'
        Try (1,1) 't': 't' in children!
          backtrack(1, 1, node_a)
            char = 't', next_node = node(h)
            Mark (1,1) = '#'
            Try (1,2) 'a': not in children
            Try (2,1) 'h': 'h' in children!
              backtrack(2, 1, node_t)
                char = 'h', next_node has word "oath"
                result = ["oath"] ✓
                
Result: ["oath"]
```

**Time**: O(M * 4 * 3^(L-1)) where M = cells, L = max word length  
**Space**: O(N) where N = total characters in all words

---

## Problem 2: [421. Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/)

### Problem Statement
Given an integer array, find the maximum result of `nums[i] XOR nums[j]`.

### Intuition
**Binary Trie**:
1. Insert all numbers as 32-bit binary in Trie
2. For each number, traverse Trie trying to go opposite bit (maximize XOR)
3. Greedy approach: prefer toggled bit at each level

### Visual Representation
```
Input: [3, 10, 5, 25, 2, 8]

Binary representations:
3:  00011
10: 01010
5:  00101
25: 11001
2:  00010
8:  01000

Trie (showing relevant bits):
       root
      /    \
     0      1
    / \      \
   0   1      1
  / \  /\    / \
 0  1 0  1  0   1
...

For num=3 (00011), find max XOR:
- Bit 4: want 1, have 0 → take 1 path → max_xor |= 16
- Bit 3: want 1, have 0 → take 1 path → max_xor |= 8
- Bit 2: want 1, have 0 → take 0 path (no 1)
- Bit 1: want 0, have 1 → take 0 path → max_xor |= 2
- Bit 0: want 0, have 1 → take 0 path

Result: 3 XOR 28 = 31 (but 28 not in array, so continue...)
Actual: 3 XOR 25 = 26
```

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}

def findMaximumXOR(nums):
    root = TrieNode()
    
    # Insert all numbers into Trie
    for num in nums:
        node = root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if bit not in node.children:
                node.children[bit] = TrieNode()
            node = node.children[bit]
    
    max_xor = 0
    
    # For each number, find maximum XOR
    for num in nums:
        node = root
        current_xor = 0
        
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            toggled_bit = 1 - bit
            
            # Try to go opposite direction
            if toggled_bit in node.children:
                current_xor |= (1 << i)
                node = node.children[toggled_bit]
            else:
                node = node.children[bit]
        
        max_xor = max(max_xor, current_xor)
    
    return max_xor
```

**Time**: O(32 * n), **Space**: O(32 * n)

---

## Problem 3: [1032. Stream of Characters](https://leetcode.com/problems/stream-of-characters/)

### Problem Statement
Implement `StreamChecker` class:
- `StreamChecker(words)`: Constructor
- `query(letter)`: Returns true if any suffix of the stream forms a word

### Intuition
**Reversed Trie**:
1. Store words in reversed order in Trie
2. Maintain stream of characters
3. For each query, check reversed stream against Trie
4. Early termination when match found

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False

class StreamChecker:
    def __init__(self, words):
        self.root = TrieNode()
        self.stream = []
        self.max_len = 0
        
        # Build Trie with reversed words
        for word in words:
            node = self.root
            self.max_len = max(self.max_len, len(word))
            
            for char in reversed(word):
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.is_word = True
    
    def query(self, letter):
        self.stream.append(letter)
        
        # Keep only recent max_len characters
        if len(self.stream) > self.max_len:
            self.stream.pop(0)
        
        # Check reversed stream
        node = self.root
        for i in range(len(self.stream) - 1, -1, -1):
            char = self.stream[i]
            
            if char not in node.children:
                return False
            
            node = node.children[char]
            
            if node.is_word:
                return True
        
        return False
```

### Dry Run
```
words = ["cd", "f", "kl"]
Trie (reversed): root → d→c*, root → f*, root → l→k*

query('a'): stream=['a'], check 'a' → not in root → False
query('b'): stream=['a','b'], check 'b' then 'a' → not in root → False
query('c'): stream=['a','b','c'], check 'c'→'b' → 'c' in root, but 'b' not in node.children['c'] → False
query('d'): stream=['a','b','c','d'], check 'd'→'c' → found 'cd' reversed! → True
query('e'): stream=['a','b','c','d','e'], check 'e' → not in root → False
query('f'): stream=['b','c','d','e','f'], check 'f' → is_word=True → True
```

**Time**: O(m) per query where m = max word length  
**Space**: O(ALPHABET_SIZE * m * n)

---

## Problem 4: [472. Concatenated Words](https://leetcode.com/problems/concatenated-words/)

### Problem Statement
Given an array of strings, return all concatenated words (words formed by concatenating shorter words from array).

### Intuition
**Trie + DP**:
1. Sort words by length
2. Build Trie incrementally
3. For each word, check if it can be formed by shorter words using DP
4. Add word to Trie after checking

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_word = False

def findAllConcatenatedWordsInADict(words):
    root = TrieNode()
    
    def insert(word):
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_word = True
    
    def can_form(word):
        """Check if word can be formed by concatenating shorter words"""
        n = len(word)
        dp = [False] * (n + 1)
        dp[0] = True
        
        for i in range(1, n + 1):
            if not dp[i]:
                node = root
                for j in range(i - 1, -1, -1):
                    if not dp[j]:
                        break
                    
                    char = word[j]
                    if char not in node.children:
                        break
                    
                    node = node.children[char]
                    
                    if node.is_word and j < i - 1:  # Ensure concatenation
                        dp[i] = True
                        break
        
        return dp[n]
    
    # Sort by length
    words.sort(key=len)
    result = []
    
    for word in words:
        if can_form(word):
            result.append(word)
        insert(word)
    
    return result
```

**Time**: O(n * m²) where m = max word length  
**Space**: O(n * m)

---

## Problem 5: [336. Palindrome Pairs](https://leetcode.com/problems/palindrome-pairs/)

### Problem Statement
Given a list of unique words, find all pairs of indices `(i, j)` such that concatenation of `words[i] + words[j]` is a palindrome.

### Intuition
**Trie of Reversed Words**:
1. Build Trie with reversed words
2. For each word, check:
   - If suffix is palindrome and prefix exists in Trie
   - If word + another word forms palindrome
3. Handle empty string case

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.word_idx = -1
        self.palindrome_indices = []

def palindromePairs(words):
    root = TrieNode()
    
    def is_palindrome(s, start, end):
        while start < end:
            if s[start] != s[end]:
                return False
            start += 1
            end -= 1
        return True
    
    # Build Trie with reversed words
    for idx, word in enumerate(words):
        node = root
        
        for i in range(len(word) - 1, -1, -1):
            # If remaining prefix is palindrome
            if is_palindrome(word, 0, i):
                node.palindrome_indices.append(idx)
            
            char = word[i]
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        
        node.word_idx = idx
        node.palindrome_indices.append(idx)
    
    result = []
    
    # Search for palindrome pairs
    for idx, word in enumerate(words):
        node = root
        
        for i, char in enumerate(word):
            # Case 1: Found complete word in Trie
            if node.word_idx != -1 and node.word_idx != idx:
                if is_palindrome(word, i, len(word) - 1):
                    result.append([idx, node.word_idx])
            
            if char not in node.children:
                break
            
            node = node.children[char]
        else:
            # Case 2: Traversed entire word
            for j in node.palindrome_indices:
                if j != idx:
                    result.append([idx, j])
    
    return result
```

**Time**: O(n * m²), **Space**: O(n * m)

---

## Problem 6: [1707. Maximum XOR With an Element From Array](https://leetcode.com/problems/maximum-xor-with-an-element-from-array/)

### Problem Statement
Given array `nums` and queries `[xi, mi]`, find maximum `xi XOR nums[j]` where `nums[j] <= mi`. Return -1 if no such element exists.

### Intuition
**Offline Queries + Binary Trie**:
1. Sort queries by `mi`
2. Sort nums
3. Insert nums into Trie as we process queries
4. Only insert nums[j] if nums[j] <= mi

### Solution
```python
class TrieNode:
    def __init__(self):
        self.children = {}

def maximizeXor(nums, queries):
    # Sort nums and queries
    nums.sort()
    indexed_queries = sorted([(m, x, i) for i, (x, m) in enumerate(queries)])
    
    root = TrieNode()
    result = [-1] * len(queries)
    num_idx = 0
    
    def insert(num):
        node = root
        for i in range(31, -1, -1):
            bit = (num >> i) & 1
            if bit not in node.children:
                node.children[bit] = TrieNode()
            node = node.children[bit]
    
    def find_max_xor(x):
        if not root.children:
            return -1
        
        node = root
        max_xor = 0
        
        for i in range(31, -1, -1):
            bit = (x >> i) & 1
            toggled = 1 - bit
            
            if toggled in node.children:
                max_xor |= (1 << i)
                node = node.children[toggled]
            else:
                if bit not in node.children:
                    return -1
                node = node.children[bit]
        
        return max_xor
    
    for m, x, idx in indexed_queries:
        # Insert all nums <= m
        while num_idx < len(nums) and nums[num_idx] <= m:
            insert(nums[num_idx])
            num_idx += 1
        
        result[idx] = find_max_xor(x)
    
    return result
```

**Time**: O((n + q) * log(max_num)), **Space**: O(n * 32)

---

## Summary Table

| Problem | Pattern | Key Technique | Difficulty |
|---------|---------|---------------|------------|
| Word Search II | Trie + Backtracking | Pruning with Trie | Hard |
| Maximum XOR | Binary Trie | Greedy bit selection | Medium-Hard |
| Stream of Characters | Reversed Trie | Suffix matching | Hard |
| Concatenated Words | Trie + DP | Word break variant | Hard |
| Palindrome Pairs | Trie + Palindrome | Reversed word matching | Hard |
| Maximum XOR Queries | Offline + Trie | Sorted insertion | Hard |

**Master Trie to excel at string and prefix problems! 🌳**
