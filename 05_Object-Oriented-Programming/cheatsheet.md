# 🏗️ Object-Oriented Programming (OOP) - Design Patterns Cheatsheet

## 📚 Core OOP Principles

### The Four Pillars

1. **Encapsulation**: Bundle data and methods, hide internal details
2. **Abstraction**: Hide complex implementation, show only essential features
3. **Inheritance**: Reuse code through parent-child relationships
4. **Polymorphism**: One interface, multiple implementations

---

## Python OOP Basics

### Class Definition
```python
class Person:
    # Class variable (shared by all instances)
    species = "Homo sapiens"
    
    def __init__(self, name, age):
        # Instance variables
        self.name = name
        self.age = age
        self._private = "semi-private"  # Convention
        self.__very_private = "name-mangled"  # Really private
    
    # Instance method
    def greet(self):
        return f"Hello, I'm {self.name}"
    
    # Class method
    @classmethod
    def from_birth_year(cls, name, birth_year):
        age = 2026 - birth_year
        return cls(name, age)
    
    # Static method
    @staticmethod
    def is_adult(age):
        return age >= 18
    
    # Property (getter)
    @property
    def description(self):
        return f"{self.name} is {self.age} years old"
    
    # Setter
    @description.setter
    def description(self, value):
        name, age = value.split(',')
        self.name = name
        self.age = int(age)
    
    # Special methods (dunder methods)
    def __str__(self):
        return f"Person({self.name}, {self.age})"
    
    def __repr__(self):
        return f"Person(name='{self.name}', age={self.age})"
    
    def __eq__(self, other):
        return self.name == other.name and self.age == other.age
```

### Inheritance
```python
class Student(Person):
    def __init__(self, name, age, student_id):
        super().__init__(name, age)
        self.student_id = student_id
    
    # Override parent method
    def greet(self):
        return f"Hi, I'm {self.name}, student #{self.student_id}"
    
    # Extend parent method
    def study(self):
        return f"{self.name} is studying"
```

### Multiple Inheritance
```python
class Teacher:
    def teach(self):
        return "Teaching"

class Researcher:
    def research(self):
        return "Researching"

class Professor(Teacher, Researcher):
    """Inherits from both Teacher and Researcher"""
    pass

# Method Resolution Order (MRO)
print(Professor.__mro__)
# (<class 'Professor'>, <class 'Teacher'>, <class 'Researcher'>, <class 'object'>)
```

---

## Design Pattern 1: Singleton

**Purpose**: Ensure only one instance of a class exists.

### Implementation
```python
class Singleton:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
        return cls._instance

# Usage
s1 = Singleton()
s2 = Singleton()
print(s1 is s2)  # True

# Alternative: Using decorator
def singleton(cls):
    instances = {}
    
    def get_instance(*args, **kwargs):
        if cls not in instances:
            instances[cls] = cls(*args, **kwargs)
        return instances[cls]
    
    return get_instance

@singleton
class DatabaseConnection:
    def __init__(self):
        self.connection = "Connected"
```

### Real-World Example: Logger
```python
class Logger:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.logs = []
        return cls._instance
    
    def log(self, message):
        self.logs.append(message)
        print(f"[LOG] {message}")

# Both use same logger instance
logger1 = Logger()
logger2 = Logger()
logger1.log("Error occurred")
print(logger2.logs)  # ['Error occurred']
```

---

## Design Pattern 2: Factory

**Purpose**: Create objects without specifying exact class.

### Simple Factory
```python
class Dog:
    def speak(self):
        return "Woof!"

class Cat:
    def speak(self):
        return "Meow!"

class AnimalFactory:
    @staticmethod
    def create_animal(animal_type):
        if animal_type == "dog":
            return Dog()
        elif animal_type == "cat":
            return Cat()
        else:
            raise ValueError(f"Unknown animal type: {animal_type}")

# Usage
animal = AnimalFactory.create_animal("dog")
print(animal.speak())  # Woof!
```

### Factory Method Pattern
```python
from abc import ABC, abstractmethod

class Pizza(ABC):
    @abstractmethod
    def prepare(self):
        pass
    
    @abstractmethod
    def bake(self):
        pass

class CheesePizza(Pizza):
    def prepare(self):
        return "Preparing cheese pizza"
    
    def bake(self):
        return "Baking cheese pizza"

class PepperoniPizza(Pizza):
    def prepare(self):
        return "Preparing pepperoni pizza"
    
    def bake(self):
        return "Baking pepperoni pizza"

class PizzaStore(ABC):
    def order_pizza(self, pizza_type):
        pizza = self.create_pizza(pizza_type)
        pizza.prepare()
        pizza.bake()
        return pizza
    
    @abstractmethod
    def create_pizza(self, pizza_type):
        pass

class NYPizzaStore(PizzaStore):
    def create_pizza(self, pizza_type):
        if pizza_type == "cheese":
            return CheesePizza()
        elif pizza_type == "pepperoni":
            return PepperoniPizza()
```

---

## Design Pattern 3: Observer

**Purpose**: Define one-to-many dependency between objects.

### Implementation
```python
class Subject:
    def __init__(self):
        self._observers = []
        self._state = None
    
    def attach(self, observer):
        self._observers.append(observer)
    
    def detach(self, observer):
        self._observers.remove(observer)
    
    def notify(self):
        for observer in self._observers:
            observer.update(self._state)
    
    def set_state(self, state):
        self._state = state
        self.notify()

class Observer:
    def update(self, state):
        pass

class ConcreteObserver(Observer):
    def __init__(self, name):
        self.name = name
    
    def update(self, state):
        print(f"{self.name} received update: {state}")

# Usage
subject = Subject()
obs1 = ConcreteObserver("Observer 1")
obs2 = ConcreteObserver("Observer 2")

subject.attach(obs1)
subject.attach(obs2)
subject.set_state("New State")
# Output:
# Observer 1 received update: New State
# Observer 2 received update: New State
```

### Real-World: Stock Price Notification
```python
class Stock:
    def __init__(self, symbol, price):
        self.symbol = symbol
        self._price = price
        self._observers = []
    
    def attach(self, observer):
        self._observers.append(observer)
    
    def set_price(self, price):
        self._price = price
        self._notify()
    
    def _notify(self):
        for observer in self._observers:
            observer.update(self)
    
    @property
    def price(self):
        return self._price

class Investor:
    def __init__(self, name):
        self.name = name
    
    def update(self, stock):
        print(f"{self.name}: {stock.symbol} is now ${stock.price}")

# Usage
apple_stock = Stock("AAPL", 150)
investor1 = Investor("Alice")
investor2 = Investor("Bob")

apple_stock.attach(investor1)
apple_stock.attach(investor2)
apple_stock.set_price(155)
```

---

## Design Pattern 4: Decorator

**Purpose**: Add behavior to objects dynamically.

### Implementation
```python
class Coffee:
    def cost(self):
        return 5
    
    def description(self):
        return "Coffee"

class CoffeeDecorator:
    def __init__(self, coffee):
        self._coffee = coffee
    
    def cost(self):
        return self._coffee.cost()
    
    def description(self):
        return self._coffee.description()

class MilkDecorator(CoffeeDecorator):
    def cost(self):
        return self._coffee.cost() + 1
    
    def description(self):
        return self._coffee.description() + ", Milk"

class SugarDecorator(CoffeeDecorator):
    def cost(self):
        return self._coffee.cost() + 0.5
    
    def description(self):
        return self._coffee.description() + ", Sugar"

# Usage
coffee = Coffee()
coffee_with_milk = MilkDecorator(coffee)
coffee_with_milk_and_sugar = SugarDecorator(coffee_with_milk)

print(coffee_with_milk_and_sugar.description())  # Coffee, Milk, Sugar
print(coffee_with_milk_and_sugar.cost())  # 6.5
```

### Python's Built-in Decorator
```python
def timing_decorator(func):
    import time
    
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.4f} seconds")
        return result
    
    return wrapper

@timing_decorator
def slow_function():
    import time
    time.sleep(1)
    return "Done"

slow_function()  # slow_function took 1.0001 seconds
```

---

## Design Pattern 5: Strategy

**Purpose**: Define family of algorithms, make them interchangeable.

### Implementation
```python
from abc import ABC, abstractmethod

class SortStrategy(ABC):
    @abstractmethod
    def sort(self, data):
        pass

class QuickSort(SortStrategy):
    def sort(self, data):
        if len(data) <= 1:
            return data
        pivot = data[len(data) // 2]
        left = [x for x in data if x < pivot]
        middle = [x for x in data if x == pivot]
        right = [x for x in data if x > pivot]
        return self.sort(left) + middle + self.sort(right)

class MergeSort(SortStrategy):
    def sort(self, data):
        if len(data) <= 1:
            return data
        
        mid = len(data) // 2
        left = self.sort(data[:mid])
        right = self.sort(data[mid:])
        
        return self._merge(left, right)
    
    def _merge(self, left, right):
        result = []
        i = j = 0
        
        while i < len(left) and j < len(right):
            if left[i] <= right[j]:
                result.append(left[i])
                i += 1
            else:
                result.append(right[j])
                j += 1
        
        result.extend(left[i:])
        result.extend(right[j:])
        return result

class Sorter:
    def __init__(self, strategy: SortStrategy):
        self._strategy = strategy
    
    def set_strategy(self, strategy: SortStrategy):
        self._strategy = strategy
    
    def sort(self, data):
        return self._strategy.sort(data)

# Usage
data = [3, 1, 4, 1, 5, 9, 2, 6]

sorter = Sorter(QuickSort())
print(sorter.sort(data))  # [1, 1, 2, 3, 4, 5, 6, 9]

sorter.set_strategy(MergeSort())
print(sorter.sort(data))  # [1, 1, 2, 3, 4, 5, 6, 9]
```

---

## Design Pattern 6: Builder

**Purpose**: Construct complex objects step by step.

### Implementation
```python
class Pizza:
    def __init__(self):
        self.size = None
        self.cheese = False
        self.pepperoni = False
        self.mushrooms = False
    
    def __str__(self):
        toppings = []
        if self.cheese:
            toppings.append("cheese")
        if self.pepperoni:
            toppings.append("pepperoni")
        if self.mushrooms:
            toppings.append("mushrooms")
        
        return f"{self.size} pizza with {', '.join(toppings)}"

class PizzaBuilder:
    def __init__(self):
        self.pizza = Pizza()
    
    def set_size(self, size):
        self.pizza.size = size
        return self
    
    def add_cheese(self):
        self.pizza.cheese = True
        return self
    
    def add_pepperoni(self):
        self.pizza.pepperoni = True
        return self
    
    def add_mushrooms(self):
        self.pizza.mushrooms = True
        return self
    
    def build(self):
        return self.pizza

# Usage (method chaining)
pizza = (PizzaBuilder()
         .set_size("Large")
         .add_cheese()
         .add_pepperoni()
         .build())

print(pizza)  # Large pizza with cheese, pepperoni
```

---

## Design Pattern 7: Adapter

**Purpose**: Convert interface of a class into another interface.

### Implementation
```python
class EuropeanSocketInterface:
    def voltage(self):
        return 230
    
    def live(self):
        return 1
    
    def neutral(self):
        return -1

class USASocketInterface:
    def voltage(self):
        return 120
    
    def live(self):
        return 1
    
    def neutral(self):
        return -1

class Adapter(USASocketInterface):
    def __init__(self, socket):
        self.socket = socket
    
    def voltage(self):
        return 120  # Convert 230V to 120V
    
    def live(self):
        return self.socket.live()
    
    def neutral(self):
        return self.socket.neutral()

# Usage
european_socket = EuropeanSocketInterface()
adapter = Adapter(european_socket)
print(f"Adapted voltage: {adapter.voltage()}V")
```

---

## LeetCode OOP Problems

### LRU Cache
```python
class Node:
    def __init__(self, key, val):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity):
        self.capacity = capacity
        self.cache = {}
        
        # Dummy head and tail
        self.head = Node(0, 0)
        self.tail = Node(0, 0)
        self.head.next = self.tail
        self.tail.prev = self.head
    
    def _remove(self, node):
        prev, nxt = node.prev, node.next
        prev.next = nxt
        nxt.prev = prev
    
    def _add(self, node):
        prev = self.tail.prev
        prev.next = node
        node.prev = prev
        node.next = self.tail
        self.tail.prev = node
    
    def get(self, key):
        if key in self.cache:
            node = self.cache[key]
            self._remove(node)
            self._add(node)
            return node.val
        return -1
    
    def put(self, key, value):
        if key in self.cache:
            self._remove(self.cache[key])
        
        node = Node(key, value)
        self._add(node)
        self.cache[key] = node
        
        if len(self.cache) > self.capacity:
            lru = self.head.next
            self._remove(lru)
            del self.cache[lru.key]
```

### Min Stack
```python
class MinStack:
    def __init__(self):
        self.stack = []
        self.min_stack = []
    
    def push(self, val):
        self.stack.append(val)
        
        if not self.min_stack or val <= self.min_stack[-1]:
            self.min_stack.append(val)
    
    def pop(self):
        if self.stack:
            val = self.stack.pop()
            if val == self.min_stack[-1]:
                self.min_stack.pop()
            return val
    
    def top(self):
        return self.stack[-1] if self.stack else None
    
    def getMin(self):
        return self.min_stack[-1] if self.min_stack else None
```

### Design Twitter
```python
from collections import defaultdict, deque
import heapq

class Twitter:
    def __init__(self):
        self.tweets = defaultdict(deque)  # userId -> tweets
        self.following = defaultdict(set)  # userId -> set of followees
        self.timestamp = 0
    
    def postTweet(self, userId, tweetId):
        self.tweets[userId].appendleft((self.timestamp, tweetId))
        self.timestamp += 1
    
    def getNewsFeed(self, userId):
        # Get tweets from user and followees
        heap = []
        
        # Add user's own tweets
        if userId in self.tweets:
            for tweet in self.tweets[userId]:
                heapq.heappush(heap, (-tweet[0], tweet[1]))
        
        # Add followees' tweets
        for followeeId in self.following[userId]:
            if followeeId in self.tweets:
                for tweet in self.tweets[followeeId]:
                    heapq.heappush(heap, (-tweet[0], tweet[1]))
        
        # Get 10 most recent
        result = []
        for _ in range(min(10, len(heap))):
            result.append(heapq.heappop(heap)[1])
        
        return result
    
    def follow(self, followerId, followeeId):
        if followerId != followeeId:
            self.following[followerId].add(followeeId)
    
    def unfollow(self, followerId, followeeId):
        self.following[followerId].discard(followeeId)
```

### Design HashMap
```python
class MyHashMap:
    def __init__(self):
        self.size = 1000
        self.buckets = [[] for _ in range(self.size)]
    
    def _hash(self, key):
        return key % self.size
    
    def put(self, key, value):
        bucket = self.buckets[self._hash(key)]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                bucket[i] = (key, value)
                return
        
        bucket.append((key, value))
    
    def get(self, key):
        bucket = self.buckets[self._hash(key)]
        
        for k, v in bucket:
            if k == key:
                return v
        
        return -1
    
    def remove(self, key):
        bucket = self.buckets[self._hash(key)]
        
        for i, (k, v) in enumerate(bucket):
            if k == key:
                del bucket[i]
                return
```

---

## SOLID Principles

### S - Single Responsibility Principle
```python
# ❌ Bad: Class has multiple responsibilities
class User:
    def __init__(self, name):
        self.name = name
    
    def save_to_database(self):
        # Database logic
        pass
    
    def send_email(self):
        # Email logic
        pass

# ✅ Good: Separate responsibilities
class User:
    def __init__(self, name):
        self.name = name

class UserRepository:
    def save(self, user):
        # Database logic
        pass

class EmailService:
    def send_email(self, user):
        # Email logic
        pass
```

### O - Open/Closed Principle
```python
# ✅ Open for extension, closed for modification
class Shape(ABC):
    @abstractmethod
    def area(self):
        pass

class Rectangle(Shape):
    def __init__(self, width, height):
        self.width = width
        self.height = height
    
    def area(self):
        return self.width * self.height

class Circle(Shape):
    def __init__(self, radius):
        self.radius = radius
    
    def area(self):
        return 3.14 * self.radius ** 2

# Can add new shapes without modifying existing code
```

### L - Liskov Substitution Principle
```python
# ✅ Subclass should be substitutable for parent class
class Bird:
    def fly(self):
        return "Flying"

class Sparrow(Bird):
    def fly(self):
        return "Sparrow flying"

class Ostrich(Bird):
    def fly(self):
        raise Exception("Can't fly")  # Violates LSP!

# Better design:
class Bird:
    pass

class FlyingBird(Bird):
    def fly(self):
        return "Flying"

class Sparrow(FlyingBird):
    pass

class Ostrich(Bird):
    def run(self):
        return "Running"
```

### I - Interface Segregation Principle
```python
# ❌ Bad: Fat interface
class Worker(ABC):
    @abstractmethod
    def work(self):
        pass
    
    @abstractmethod
    def eat(self):
        pass

# ✅ Good: Segregated interfaces
class Workable(ABC):
    @abstractmethod
    def work(self):
        pass

class Eatable(ABC):
    @abstractmethod
    def eat(self):
        pass

class Human(Workable, Eatable):
    def work(self):
        return "Working"
    
    def eat(self):
        return "Eating"

class Robot(Workable):
    def work(self):
        return "Working"
```

### D - Dependency Inversion Principle
```python
# ❌ Bad: High-level depends on low-level
class EmailService:
    def send_email(self, message):
        print(f"Sending email: {message}")

class Notification:
    def __init__(self):
        self.email_service = EmailService()
    
    def send(self, message):
        self.email_service.send_email(message)

# ✅ Good: Depend on abstraction
class MessageService(ABC):
    @abstractmethod
    def send(self, message):
        pass

class EmailService(MessageService):
    def send(self, message):
        print(f"Sending email: {message}")

class SMSService(MessageService):
    def send(self, message):
        print(f"Sending SMS: {message}")

class Notification:
    def __init__(self, service: MessageService):
        self.service = service
    
    def send(self, message):
        self.service.send(message)
```

---

## 🎯 Must-Know LeetCode OOP Problems

### Easy
- [705. Design HashSet](https://leetcode.com/problems/design-hashset/)
- [706. Design HashMap](https://leetcode.com/problems/design-hashmap/)
- [155. Min Stack](https://leetcode.com/problems/min-stack/)

### Medium
- [146. LRU Cache](https://leetcode.com/problems/lru-cache/)
- [380. Insert Delete GetRandom O(1)](https://leetcode.com/problems/insert-delete-getrandom-o1/)
- [355. Design Twitter](https://leetcode.com/problems/design-twitter/)
- [211. Design Add and Search Words Data Structure](https://leetcode.com/problems/design-add-and-search-words-data-structure/)

### Hard
- [460. LFU Cache](https://leetcode.com/problems/lfu-cache/)
- [432. All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/)

---

## 💡 Pro Tips

1. **Favor composition over inheritance**: More flexible
2. **Program to interface, not implementation**: Use abstract base classes
3. **Use properties for getters/setters**: More Pythonic
4. **Keep classes small and focused**: Single Responsibility Principle
5. **Use `__slots__` for memory optimization**: When creating many instances
6. **Document with docstrings**: Essential for OOP design
7. **Use type hints**: Improves code clarity and catches errors

---

**Master OOP - design elegant, maintainable systems! 🏗️**
