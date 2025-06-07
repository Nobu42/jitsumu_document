# 各日写経コード（アルゴリズム基礎）

## 月曜：線形探索（linear_search.py）

```
# linear_search.py
def linear_search(data, target):
    for i, value in enumerate(data):
        if value == target:
            return i  # 見つかったインデックス
    return -1  # 見つからなかった

nums = [4, 2, 7, 9, 1]
target = 7
result = linear_search(nums, target)
print("位置:", result)
```

## 火曜：二分探索（binary_search.py）

```
# binary_search.py
def binary_search(data, target):
    left, right = 0, len(data) - 1
    while left <= right:
        mid = (left + right) // 2
        if data[mid] == target:
            return mid
        elif data[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

nums = [1, 3, 5, 7, 9, 11]
target = 7
result = binary_search(nums, target)
print("位置:", result)
```

## 水曜：バブルソート（bubble_sort.py）

```
# bubble_sort.py
def bubble_sort(data):
    n = len(data)
    for i in range(n):
        for j in range(n - 1 - i):
            if data[j] > data[j + 1]:
                data[j], data[j + 1] = data[j + 1], data[j]

nums = [5, 2, 4, 1, 3]
bubble_sort(nums)
print("整列結果:", nums)
```

## 木曜：選択ソート（selection_sort.py）

```
# selection_sort.py
def selection_sort(data):
    n = len(data)
    for i in range(n):
        min_idx = i
        for j in range(i+1, n):
            if data[j] < data[min_idx]:
                min_idx = j
        data[i], data[min_idx] = data[min_idx], data[i]

nums = [5, 2, 4, 1, 3]
selection_sort(nums)
print("整列結果:", nums)
```

## 金曜：挿入ソート（insertion_sort.py）

```
# insertion_sort.py
def insertion_sort(data):
    for i in range(1, len(data)):
        key = data[i]
        j = i - 1
        while j >= 0 and data[j] > key:
            data[j + 1] = data[j]
            j -= 1
        data[j + 1] = key

nums = [5, 2, 4, 1, 3]
insertion_sort(nums)
print("整列結果:", nums)
```

## 土曜：累積和（cumulative_sum.py）

```
# cumulative_sum.py
def build_cumsum(data):
    cumsum = [0]
    for x in data:
        cumsum.append(cumsum[-1] + x)
    return cumsum

nums = [3, 5, 2, 6, 1]
cumsum = build_cumsum(nums)

# 1番目〜3番目の合計（5+2+6）= 13
l, r = 1, 4
print("区間合計:", cumsum[r] - cumsum[l])
```

## 日曜：フィボナッチ数列（メモ化再帰）（fibonacci.py）

```
# fibonacci.py
memo = {}

def fib(n):
    if n in memo:
        return memo[n]
    if n <= 1:
        result = n
    else:
        result = fib(n-1) + fib(n-2)
    memo[n] = result
    return result

for i in range(10):
    print(f"F({i}) = {fib(i)}")
```


## 月曜：スタック（stack.py）

```
# stack.py
class Stack:
    def __init__(self):
        self.data = []

    def push(self, value):
        self.data.append(value)

    def pop(self):
        return self.data.pop() if self.data else None

    def peek(self):
        return self.data[-1] if self.data else None

s = Stack()
s.push(10)
s.push(20)
print("pop:", s.pop())
print("peek:", s.peek())
```

## 火曜：キュー（queue.py）

```
# queue.py
from collections import deque

class Queue:
    def __init__(self):
        self.data = deque()

    def enqueue(self, value):
        self.data.append(value)

    def dequeue(self):
        return self.data.popleft() if self.data else None

q = Queue()
q.enqueue("A")
q.enqueue("B")
print("dequeue:", q.dequeue())
print("dequeue:", q.dequeue())
```

## 水曜：両端キュー（deque_demo.py）

```
# deque_demo.py
from collections import deque

dq = deque()

dq.append(1)        # 右に追加
dq.appendleft(0)    # 左に追加
dq.append(2)

print("deque:", list(dq))
print("pop right:", dq.pop())
print("pop left:", dq.popleft())
```

## 木曜：リングバッファ（ring_buffer.py）

```
# ring_buffer.py
class RingBuffer:
    def __init__(self, size):
        self.size = size
        self.buffer = [None] * size
        self.head = 0  # 書き込み位置
        self.full = False

    def append(self, value):
        self.buffer[self.head] = value
        self.head = (self.head + 1) % self.size
        if self.head == 0:
            self.full = True

    def get(self):
        if not self.full:
            return self.buffer[:self.head]
        return self.buffer[self.head:] + self.buffer[:self.head]

rb = RingBuffer(3)
rb.append("A")
rb.append("B")
print(rb.get())
rb.append("C")
print(rb.get())
rb.append("D")  # Aが上書きされる
print(rb.get())
```

## 金曜：優先度付きキュー（priority_queue.py）

```
# priority_queue.py
import heapq

data = []
heapq.heappush(data, (3, "C"))
heapq.heappush(data, (1, "A"))
heapq.heappush(data, (2, "B"))

while data:
    priority, value = heapq.heappop(data)
    print(f"priority={priority}, value={value}")
```

## 土曜：連結リスト（単方向）（linked_list.py）

```
# linked_list.py
class Node:
    def __init__(self, value):
        self.value = value
        self.next = None

class LinkedList:
    def __init__(self):
        self.head = None

    def add(self, value):
        new = Node(value)
        if not self.head:
            self.head = new
        else:
            cur = self.head
            while cur.next:
                cur = cur.next
            cur.next = new

    def display(self):
        cur = self.head
        while cur:
            print(cur.value, end=" → ")
            cur = cur.next
        print("None")

lst = LinkedList()
lst.add(10)
lst.add(20)
lst.display()
```

## 日曜：スタックで再帰を模擬（stack_recursion.py）

```
# stack_recursion.py
def simulate_factorial(n):
    stack = []
    while n > 1:
        stack.append(n)
        n -= 1

    result = 1
    while stack:
        result *= stack.pop()

    return result

print("5! =", simulate_factorial(5))
```

