# Time Needed to Buy Tickets

**LeetCode:** [2073. Time Needed to Buy Tickets](https://leetcode.com/problems/time-needed-to-buy-tickets/)

**Pattern:** Queue — Simulation

## 1. Problem Statement

There are `n` people standing in a queue to buy tickets. The person at position `i` needs `tickets[i]` tickets.

Each person takes exactly one second to buy one ticket. After buying one ticket:
- If the person still needs more tickets, they go to the end of the queue.
- Otherwise, they leave the queue.

Return the time taken for the person at index `k` to finish buying all their tickets.

## 2. Practiced Approach — Queue Simulation

Simulate the real queue process. Store **person indices** in the queue, not their ticket counts. The ticket counts remain in the input array.

### Logic

1. Add every person's index from `0` to `n - 1` to the queue.
2. Initialize `time = 0`.
3. Remove the person at the front of the queue using `poll()`.
4. Decrement that person's remaining ticket count and increment time.
5. If this person is the target (`person == k`) and their remaining ticket count becomes zero, stop immediately.
6. Otherwise, if this person still needs tickets, add their index to the rear of the queue using `offer()`.
7. Return the elapsed time.

## 3. Complete Java Code

```java
import java.util.*;

class Solution {
    public int timeRequiredToBuy(int[] tickets, int k) {

        Queue<Integer> q = new LinkedList<>();

        // Add all person indices into the queue
        for (int i = 0; i < tickets.length; i++) {
            q.offer(i);
        }

        int time = 0;

        while (!q.isEmpty()) {

            int person = q.poll();

            tickets[person]--;
            time++;

            // Target person has finished buying tickets
            if (person == k && tickets[person] == 0) {
                break;
            }

            // If this person still needs tickets, add them back
            if (tickets[person] > 0) {
                q.offer(person);
            }
        }

        return time;
    }
}
```

**Important:** The queue stores indices such as `0, 1, 2, 3`. The array `tickets` stores each person's remaining ticket count. The code decrements the input array in place.

## 4. Dry Run — Practiced Example

```text
tickets = [4, 7, 5, 3]
k = 1
```

People:
- P0 needs 4 tickets.
- P1 (target) needs 7 tickets.
- P2 needs 5 tickets.
- P3 needs 3 tickets.

Initial queue: `[P0, P1, P2, P3]`

Each row represents one second. The remaining counts are shown as `P0, P1, P2, P3`.

| Time | Person buying | Remaining tickets after purchase | Queue after action |
|---:|---|---|---|
| 0 | — | [4, 7, 5, 3] | [P0, P1, P2, P3] |
| 1 | P0 | [3, 7, 5, 3] | [P1, P2, P3, P0] |
| 2 | P1 | [3, 6, 5, 3] | [P2, P3, P0, P1] |
| 3 | P2 | [3, 6, 4, 3] | [P3, P0, P1, P2] |
| 4 | P3 | [3, 6, 4, 2] | [P0, P1, P2, P3] |
| 5 | P0 | [2, 6, 4, 2] | [P1, P2, P3, P0] |
| 6 | P1 | [2, 5, 4, 2] | [P2, P3, P0, P1] |
| 7 | P2 | [2, 5, 3, 2] | [P3, P0, P1, P2] |
| 8 | P3 | [2, 5, 3, 1] | [P0, P1, P2, P3] |
| 9 | P0 | [1, 5, 3, 1] | [P1, P2, P3, P0] |
| 10 | P1 | [1, 4, 3, 1] | [P2, P3, P0, P1] |
| 11 | P2 | [1, 4, 2, 1] | [P3, P0, P1, P2] |
| 12 | P3 | [1, 4, 2, 0] | [P0, P1, P2] |
| 13 | P0 | [0, 4, 2, 0] | [P1, P2] |
| 14 | P1 | [0, 3, 2, 0] | [P2, P1] |
| 15 | P2 | [0, 3, 1, 0] | [P1, P2] |
| 16 | P1 | [0, 2, 1, 0] | [P2, P1] |
| 17 | P2 | [0, 2, 0, 0] | [P1] |
| 18 | P1 | [0, 1, 0, 0] | [P1] |
| 19 | P1 | [0, 0, 0, 0] | Target finishes; stop |

P1 finishes at **19 seconds**.

**Output:** `19`

Notice that after P3 finishes at time 12, P3 leaves the queue and is not added again. The target is P1, so the simulation stops immediately after P1 buys their seventh ticket at time 19.

## 5. Complexity Analysis

Let:
- `n` be the number of people.
- `S = sum(tickets[i])` be the total number of tickets requested.

- **Time Complexity:** `O(S)` in the worst case — each ticket purchase takes one queue iteration. The simulation stops when the target finishes, so it may do less work than the full sum.
- **Space Complexity:** `O(n)` — the queue stores at most one index per person.

## 6. Extra Insight — Direct Counting Formula

After understanding the queue simulation, we can count each person's contribution without simulating every second.

Let `targetTickets = tickets[k]` be the target's original ticket count.

- For every person at index `i <= k`, they can buy at most `targetTickets` tickets before the target finishes.
- For every person at index `i > k`, they get one fewer turn, so they can buy at most `targetTickets - 1` tickets.

Contribution:
- If `i <= k`: `min(tickets[i], targetTickets)`
- If `i > k`: `min(tickets[i], targetTickets - 1)`

### Java Code — Counting Alternative

Use the original, unmodified ticket array for this calculation.

```java
class Solution {
    public int timeRequiredToBuy(int[] tickets, int k) {

        int time = 0;
        int targetTickets = tickets[k];

        for (int i = 0; i < tickets.length; i++) {
            if (i <= k) {
                time += Math.min(tickets[i], targetTickets);
            } else {
                time += Math.min(tickets[i], targetTickets - 1);
            }
        }

        return time;
    }
}
```

- **Time Complexity:** `O(n)`
- **Space Complexity:** `O(1)`

The queue simulation is the main practiced approach. The counting formula is an optimization derived from the same process.

## 7. Important Revision Takeaways

- Queue follows FIFO: the first person in line gets the next turn.
- Store person indices in the queue, not ticket counts.
- `poll()` removes the front; `offer()` adds to the rear.
- Increment time once for every ticket purchased.
- A person who has no tickets left must leave the queue.
- Stop as soon as person `k` buys their final ticket.
- The direct counting formula can optimize the simulation to `O(n)`.
