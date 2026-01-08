# 🌳 Trie (Prefix Tree) - Comprehensive Cheatsheet

## 📚 Core Concept

**Trie**: Tree data structure for efficient string storage and retrieval. Each node represents a character.

### When to Use Trie?
✅ **Prefix matching** (autocomplete)  
✅ **Dictionary** implementation  
✅ **Word search** in grid  
✅ **IP routing** tables  
✅ **Spell checker**  

### Time Complexity
- **Insert**: O(m) where m = length of word
- **Search**: O(m)
- **Prefix Search**: O(m)
- **Space**: O(ALPHABET_SIZE * m * n) where n = number of words

---

## Basic Trie Implementation

### Standard Trie Node
```python
class TrieNode:
    def __init__(self):
        self.children = {}  # or [None] * 26 for lowercase only
        self.is_end_of_word = False
        # Optional: count for frequency tracking
        self.count = 0

class Trie:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        """Insert a word into the trie"""
        node = self.root
        
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
            node.count += 1  # Track prefix frequency
        
        node.is_end_of_word = True
    
    def search(self, word):
        """Returns True if word exists in trie"""
        node = self.root
        
        for char in word:
            if char not in node.children:
                return False
            node = node.children[char]
        
        return node.is_end_of_word
    
    def startsWith(self, prefix):
        """Returns True if prefix exists in trie"""
        node = self.root
        
        for char in prefix:
            if char not in node.children:
                return False
            node = node.children[char]
        
        return True
    
    def delete(self, word):
        """Delete a word from trie"""
        def _delete(node, word, index):
            if index == len(word):
                if not node.is_end_of_word:
                    return False
                node.is_end_of_word = False
                return len(node.children) == 0
            
            char = word[index]
            if char not in node.children:
                return False
            
            child = node.children[char]
            should_delete = _delete(child, word, index + 1)
            
            if should_delete:
                del node.children[char]
                return len(node.children) == 0 and not node.is_end_of_word
            
            return False
        
        _delete(self.root, word, 0)
```

---

## Common Patterns

### Pattern 1: Autocomplete / Prefix Suggestions

```python
class AutocompleteSystem:
    def __init__(self):
        self.root = TrieNode()
    
    def insert(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
    
    def get_suggestions(self, prefix):
        """Get all words with given prefix"""
        node = self.root
        
        # Navigate to prefix
        for char in prefix:
            if char not in node.children:
                return []
            node = node.children[char]
        
        # DFS to collect all words
        words = []
        
        def dfs(node, path):
            if node.is_end_of_word:
                words.append(prefix + path)
            
            for char, child in node.children.items():
                dfs(child, path + char)
        
        dfs(node, "")
        return words

# Example usage
autocomplete = AutocompleteSystem()
words = ["apple", "app", "application", "apply", "banana"]
for word in words:
    autocomplete.insert(word)

print(autocomplete.get_suggestions("app"))
# Output: ['app', 'apple', 'application', 'apply']
```

### Pattern 2: Word Search II (Trie + Backtracking)

```python
class Solution:
    def findWords(self, board, words):
        # Build trie from words
        root = TrieNode()
        for word in words:
            node = root
            for char in word:
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.is_end_of_word = True
            node.word = word  # Store word at end node
        
        rows, cols = len(board), len(board[0])
        result = set()
        
        def backtrack(r, c, node):
            char = board[r][c]
            
            if char not in node.children:
                return
            
            next_node = node.children[char]
            
            if next_node.is_end_of_word:
                result.add(next_node.word)
            
            # Mark as visited
            board[r][c] = '#'
            
            # Explore 4 directions
            for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and board[nr][nc] != '#':
                    backtrack(nr, nc, next_node)
            
            # Restore
            board[r][c] = char
        
        # Try starting from each cell
        for r in range(rows):
            for c in range(cols):
                backtrack(r, c, root)
        
        return list(result)
```

### Pattern 3: Longest Common Prefix

```python
def longestCommonPrefix(strs):
    if not strs:
        return ""
    
    # Build trie
    root = TrieNode()
    for word in strs:
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
    
    # Find LCP
    prefix = []
    node = root
    
    while len(node.children) == 1 and not node.is_end_of_word:
        char = list(node.children.keys())[0]
        prefix.append(char)
        node = node.children[char]
    
    return ''.join(prefix)
```

### Pattern 4: Replace Words (Dictionary)

```python
def replaceWords(dictionary, sentence):
    # Build trie
    root = TrieNode()
    for word in dictionary:
        node = root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
    
    def find_root(word):
        node = root
        prefix = []
        
        for char in word:
            if char not in node.children:
                return word
            
            node = node.children[char]
            prefix.append(char)
            
            if node.is_end_of_word:
                return ''.join(prefix)
        
        return word
    
    words = sentence.split()
    return ' '.join(find_root(word) for word in words)

# Example
dictionary = ["cat", "bat", "rat"]
sentence = "the cattle was rattled by the battery"
print(replaceWords(dictionary, sentence))
# Output: "the cat was rat by the bat"
```

### Pattern 5: Maximum XOR of Two Numbers

```python
class Solution:
    def findMaximumXOR(self, nums):
        # Trie for binary representation
        class BitTrie:
            def __init__(self):
                self.children = {}
        
        root = BitTrie()
        
        # Insert all numbers (32-bit representation)
        for num in nums:
            node = root
            for i in range(31, -1, -1):
                bit = (num >> i) & 1
                if bit not in node.children:
                    node.children[bit] = BitTrie()
                node = node.children[bit]
        
        max_xor = 0
        
        # For each number, find maximum XOR
        for num in nums:
            node = root
            current_xor = 0
            
            for i in range(31, -1, -1):
                bit = (num >> i) & 1
                # Try to go opposite direction for max XOR
                toggled_bit = 1 - bit
                
                if toggled_bit in node.children:
                    current_xor |= (1 << i)
                    node = node.children[toggled_bit]
                else:
                    node = node.children[bit]
            
            max_xor = max(max_xor, current_xor)
        
        return max_xor
```

### Pattern 6: Add and Search Word (Wildcard)

```python
class WordDictionary:
    def __init__(self):
        self.root = TrieNode()
    
    def addWord(self, word):
        node = self.root
        for char in word:
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
    
    def search(self, word):
        def dfs(node, i):
            if i == len(word):
                return node.is_end_of_word
            
            char = word[i]
            
            if char == '.':
                # Wildcard: try all children
                for child in node.children.values():
                    if dfs(child, i + 1):
                        return True
                return False
            else:
                if char not in node.children:
                    return False
                return dfs(node.children[char], i + 1)
        
        return dfs(self.root, 0)
```

### Pattern 7: Stream of Characters

```python
class StreamChecker:
    def __init__(self, words):
        self.root = TrieNode()
        self.stream = []
        
        # Build trie with reversed words
        for word in words:
            node = self.root
            for char in reversed(word):
                if char not in node.children:
                    node.children[char] = TrieNode()
                node = node.children[char]
            node.is_end_of_word = True
    
    def query(self, letter):
        self.stream.append(letter)
        node = self.root
        
        # Check reversed stream
        for i in range(len(self.stream) - 1, -1, -1):
            char = self.stream[i]
            if char not in node.children:
                return False
            node = node.children[char]
            if node.is_end_of_word:
                return True
        
        return False
```

### Pattern 8: Palindrome Pairs

```python
def palindromePairs(words):
    # Build trie of reversed words
    root = TrieNode()
    
    for i, word in enumerate(words):
        node = root
        for char in reversed(word):
            if char not in node.children:
                node.children[char] = TrieNode()
            node = node.children[char]
        node.is_end_of_word = True
        node.word_index = i
    
    def is_palindrome(s):
        return s == s[::-1]
    
    result = []
    
    for i, word in enumerate(words):
        node = root
        
        for j, char in enumerate(word):
            # Check if remaining word forms palindrome
            if node.is_end_of_word and is_palindrome(word[j:]):
                if node.word_index != i:
                    result.append([i, node.word_index])
            
            if char not in node.children:
                break
            node = node.children[char]
        else:
            # Traversed entire word
            if node.is_end_of_word and node.word_index != i:
                result.append([i, node.word_index])
    
    return result
```

---

## 🎨 Dry Run Example

### Insert and Search

```
Insert "apple":
       root
        |
        a
        |
        p
        |
        p
        |
        l
        |
        e* (is_end_of_word = True)

Insert "app":
       root
        |
        a
        |
        p
        |
        p* (is_end_of_word = True)
        |
        l
        |
        e*

Search "app": 
  root → a → p → p* → True (found!)

Search "appl":
  root → a → p → p → l → False (not end of word)

StartsWith "app":
  root → a → p → p → True (prefix exists!)
```

---

## ⏱️ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Insert | O(m) | O(m) |
| Search | O(m) | O(1) |
| StartsWith | O(m) | O(1) |
| Delete | O(m) | O(1) |
| Total Space | O(ALPHABET_SIZE * m * n) | - |

Where:
- m = length of word
- n = number of words
- ALPHABET_SIZE = 26 for lowercase letters

---

## 🎯 Must-Know Problems

### Easy
- [208. Implement Trie (Prefix Tree)](https://leetcode.com/problems/implement-trie-prefix-tree/)
- [720. Longest Word in Dictionary](https://leetcode.com/problems/longest-word-in-dictionary/)

### Medium
- [211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)
- [648. Replace Words](https://leetcode.com/problems/replace-words/)
- [677. Map Sum Pairs](https://leetcode.com/problems/map-sum-pairs/)
- [1268. Search Suggestions System](https://leetcode.com/problems/search-suggestions-system/)

### Hard
- [212. Word Search II](https://leetcode.com/problems/word-search-ii/)
- [421. Maximum XOR of Two Numbers in an Array](https://leetcode.com/problems/maximum-xor-of-two-numbers-in-an-array/)
- [472. Concatenated Words](https://leetcode.com/problems/concatenated-words/)
- [336. Palindrome Pairs](https://leetcode.com/problems/palindrome-pairs/)
- [1032. Stream of Characters](https://leetcode.com/problems/stream-of-characters/)

---

## 💡 Pro Tips

1. **Use dict vs array**: Dict more flexible, array faster for fixed alphabet
2. **Store data at end node**: Word, frequency, index, etc.
3. **Wildcard search**: Use DFS with backtracking
4. **Memory optimization**: Delete unused branches
5. **Reversed trie**: For suffix matching (stream problems)
6. **Binary trie**: For XOR problems (store bits)

---

## 🔥 Common Mistakes

❌ **Not marking end of word** correctly  
✅ **Always set `is_end_of_word = True` after insert**

❌ **Confusing search vs startsWith**  
✅ **Search checks end of word, startsWith doesn't**

❌ **Memory leak in delete**  
✅ **Recursively remove empty branches**

❌ **Wrong wildcard implementation**  
✅ **Use DFS to explore all possibilities**

---

## 🎓 Advanced Concepts

### Compressed Trie (Radix Tree)
- Store strings in nodes instead of single characters
- Reduces space complexity
- Used in routing tables, autocomplete

### Suffix Tree
- Stores all suffixes of a string
- Used in pattern matching, substring search
- Linear space construction possible

### Applications
- **Autocomplete**: Google search suggestions
- **Spell checker**: Find closest words
- **IP routing**: Longest prefix matching
- **Genome analysis**: Pattern matching
- **String compression**: Huffman coding

---

**Tries are powerful for string problems - master prefix operations! 🌳**
