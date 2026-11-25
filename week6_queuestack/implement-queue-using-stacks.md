# Implement Queue Using Stacks

**LeetCode:** [232. Implement Queue using Stacks](https://leetcode.com/problems/implement-queue-using-stacks/)

**Pattern:** Queue / Stack — Two Stacks

## 1. Problem Statement

Implement a first-in-first-out (FIFO) queue using only two stacks. The queue should support:

- `push(x)`: Push element `x) to the back of the queue.
- `pop()`: Remove the element from the front of the queue and return it.
- `peek()`: Return the front element.
- `empty()`: Return `true` if the queue is empty, otherwise `false`.

## 2. Practiced Approach — Two Stacks

Use two stacks:
- **stack1 (input stack):** Every new element is pushed into this stack.
- **stack2 (output stack):** Elements are popped or peeked from this stack.

The key idea is to transfer elements from stack1 to stack2 only when stack2 is empty. This reverses the order and gives FIFO behaviour.

### Logic

1. For `push(x)`, always push `x` into stack1.
2. For `pop()` or `peek()`, first check whether stack2 is empty.
3. If stack2 is empty, move all elements from stack1 to stack2.
4. Return or remove the top element of stack2.
5. The queue is empty only when **both** stacks are empty.

### Why should we transfer only when stack2 is empty?

Suppose stack2 already contains older queue elements. Transferring new elements from stack1 at this point would put newer elements above older ones and break FIFO order. So we leave stack2 as it is until it becomes empty.

## 3. Complete Java Code

```java
import java.util.Stack;

class MyQueue {

    Stack<Integer> stack1;
    Stack<Integer> stack2;

    public MyQueue() {
        stack1 = new Stack<>();
        stack2 = new Stack<>();
    }

    public void push(int x) {
        stack1.push(x);
    }

    public int pop() {

        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }

        return stack2.pop();
    }

    public int peek() {

        if (stack2.isEmpty()) {
            while (!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }

        return stack2.peek();
    }

    public boolean empty() {
        return stack1.isEmpty() && stack2.isEmpty();
    }
}
```

## 4. Dry Run

Operations:

```text
push(1), push(2), peek(), pop(), empty()
```

Stacks are shown from bottom to top, with the top element on the right.

| Operation | stack1 | stack2 | Result |
|---|---|---|---|
| Initial | [] | [] | — |
| push(1) | [1] | [] | — |
| push(2) | [1, 2] | [] | — |
| peek() | [] | [2, 1] | 1 |
| pop() | [] | [2] | 1 |
| empty() | [] | [2] | false |

Explanation:
- Both new elements are pushed into stack1.
- On `peek()`, stack2 is empty, so all elements are transferred. The order becomes `[2, 1]`; now `1` is on top and is the queue front.
- `pop()` removes `1` from stack2.
- `empty()` returns false because stack2 still contains `2`.

## 5. Complexity Analysis

Let `n` be the number of elements in the queue.

| Operation | Time Complexity | Reason |
|---|---|---|
| `push()` | `O(1)` | Push directly into stack1 |
| `pop()` | Amortized `O(1)` | Each element is transferred at most once, then popped once |
| `peek()` | Amortized `O(1)` | Transfer happens only when stack2 is empty |
| `empty()` | `O(1)` | Check whether both stacks are empty |

- **Space Complexity:** `O(n)` — both stacks together store all queue elements.
- A single `pop()` or `peek()` can take `O(n)` when it triggers a transfer, but across a sequence of operations each element moves only once. Therefore the amortized time is `O(1)`.

## 6. Important Revision Takeaways

- Queue follows FIFO; Stack follows LIFO.
- Two stacks can implement a queue by reversing element order.
- Always push new values into stack1.
- Transfer stack1 to stack2 only when stack2 is empty.
- Use `stack2.pop()` for queue removal and `stack2.peek()` for the front.
- Queue is empty only if both stacks are empty.
