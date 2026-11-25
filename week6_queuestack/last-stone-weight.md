# Last Stone Weight

**LeetCode:** [1046. Last Stone Weight](https://leetcode.com/problems/last-stone-weight/)

**Pattern:** Heap / Priority Queue — Max Heap

## 1. Problem Statement

You are given an array of integers `stones`, where each value represents a stone's weight.

Each turn, choose the two heaviest stones and smash them:
- If both stones have equal weight, both are destroyed.
- If their weights are different, the smaller stone is destroyed and the larger stone is replaced by the difference between their weights.

Continue until at most one stone remains. Return its weight, or `0` if no stone remains.

### Example

```text
Input:  stones = [2, 7, 4, 1, 8, 1]
Output: 1
```

## 2. Practiced Approach — Max Heap Using PriorityQueue

We need the two largest stones in every iteration. A Max Heap keeps the largest element at its top.

Java's `PriorityQueue` is a heap-based data structure. By default it behaves as a Min Heap. We use `Collections.reverseOrder()` to make it behave as a Max Heap.

### Logic

1. Create a Max Heap and insert all stones.
2. Continue while the heap has at least two stones.
3. Remove the two largest stones using `poll()`.
4. If their difference is greater than zero, insert that difference back into the heap.
5. If the difference is zero, both stones are destroyed; insert nothing.
6. Return `0` if the heap is empty, otherwise return the remaining stone.

## 3. Complete Java Code

```java
import java.util.*;

class Solution {
    public int lastStoneWeight(int[] stones) {

        PriorityQueue<Integer> pq =
            new PriorityQueue<>(Collections.reverseOrder());

        for (int stone : stones) {
            pq.add(stone);
        }

        while (pq.size() > 1) {

            int first = pq.poll();
            int second = pq.poll();

            if (first - second > 0) {
                pq.add(first - second);
            }
        }

        return pq.isEmpty() ? 0 : pq.poll();
    }
}
```

## 4. Important Concepts

### Min Heap vs Max Heap

- `new PriorityQueue<Integer>()` creates a Min Heap: the smallest element is at the top.
- `new PriorityQueue<Integer>(Collections.reverseOrder())` behaves as a Max Heap: the largest element is at the top.

### PriorityQueue methods

| Method | Meaning |
|---|---|
| `add(x)` | Insert an element |
| `peek()` | Return the top element without removing it |
| `poll()` | Remove and return the top element |
| `size()` | Return the number of elements |
| `isEmpty()` | Check whether the heap is empty |

### Why `while (pq.size() > 1)`?

A smash requires two stones. If zero or one stone remains, no more smash is possible.

### Why is there no `else` block?

If `first - second == 0`, both stones disappear. We simply do not add a new stone to the heap.

## 5. Dry Run

Input: `stones = [2, 7, 4, 1, 8, 1]`

For readability, the heap is shown in descending order after each operation.

| Turn | Two largest stones | Difference | Heap after smash |
|---:|---|---:|---|
| Initial | — | — | [8, 7, 4, 2, 1, 1] |
| 1 | 8 and 7 | 1 | [4, 2, 1, 1, 1] |
| 2 | 4 and 2 | 2 | [2, 1, 1, 1] |
| 3 | 2 and 1 | 1 | [1, 1, 1] |
| 4 | 1 and 1 | 0 | [1] |

Only one stone remains, with weight `1`.

**Output:** `1`

## 6. Complexity Analysis

Let `n` be the number of stones.

- **Time Complexity:** `O(n log n)` — inserting all stones takes `O(n log n)`; each smash performs heap removals and possibly one insertion, each taking `O(log n)`.
- **Space Complexity:** `O(n)` — the PriorityQueue stores the stones.

## 7. Important Revision Takeaways

- Use a Max Heap when you repeatedly need the largest element.
- Java's PriorityQueue is Min Heap by default.
- Use `Collections.reverseOrder()` for Max Heap behaviour.
- `poll()` removes the top element; `peek()` does not remove it.
- Continue while `pq.size() > 1`, because each operation needs two stones.
- If the two stones are equal, both are removed and nothing is inserted back.
