# Game Strategy & Minimax DP Notes

> **Core Golden Rule:** Always play your **best move** on your turn, and assume the opponent will play **their best move** on theirs. Because this is a zero-sum game, the opponent playing their "best" means they will leave you with the **worst possible outcome**.

---

## The 2 Core Patterns

Most two-player optimal game problems on arrays/strings fall into one of two Dynamic Programming patterns. 

### Pattern 1: Maximize Absolute Score (Your Total Points)
**Goal:** Find the exact maximum score the first player (Alice) can get (e.g., *Stone Game II, Stone Game III*). 

There are two distinct ways to write this state transition. Both yield the exact same answer, but they think about the opponent's turn differently.

#### Approach A: The 2-Step Lookahead (Pessimistic Minimax)
* **Intuition:** Take the points you get right now, plus the **minimum** of what Bob leaves for you after his turn.
* In its turn a player will take the maximum and when we are jumping indexes for other player (Bob e.g) then we should expect wrost for us from the other player i.e. Bob 
* **The "Math.min" Trap:** When looking at Bob's choices, you must use `Math.min()`, not `Math.max()`. Bob is trying to maximize his own score, which mathematically means he will pick the option that leaves Alice with the lowest possible future score. **Expect the worst.**
* **Transition:**
  ```java
  // 1. ALICE'S TURN: Alice takes some stones (currentTake)
  
  // 2. BOB'S TURN: Bob evaluates his options and picks the one that hurts Alice the most
  int minNextAliceScore = Integer.MAX_VALUE;
  minNextAliceScore = Math.min(minNextAliceScore, helper(j + 2)); // Bob takes 1
  minNextAliceScore = Math.min(minNextAliceScore, helper(j + 3)); // Bob takes 2



  #### Approach B: The 1-Step Lookahead (Subtracting Opponent's Score)
- **Intuition:** Instead of simulating Bob's turn and Alice's next turn (2 steps ahead), look just 1 step ahead. Take your points now, plus the total remaining points **minus** Bob's best possible absolute score from the rest.
- **Why it works:** Since every remaining stone goes to either Alice or Bob, `Alice's Future Score = Total Remaining Points - Bob's Best Score`.
- Means instead of jumping directly we can continue as we were doing but as its Bob turn so we should expect wrost and should subtract it from the Alice score (if it's a sum game)
- **Transition:**
  ~~~java
  // 1. Precalculate suffixSum[] so you know total remaining points from index (j + 1) to (n - 1)
  // 2. Bob plays optimally on the remaining stones starting at (j + 1): helper(j + 1)
  int bobBestScore = helper(stoneValue, j + 1, suffixSum, memo);
  int aliceFutureScore = suffixSum[j + 1] - bobBestScore;

  // 3. Alice maximizes her current take + what remains after subtracting Bob's best score
  maxAliceScore = Math.max(maxAliceScore, currentTake + aliceFutureScore);
  ~~~
- **Complete Code Example using (Stone Game III using Approach B):**
  ~~~java
  class Solution {
      int n;
      public String stoneGameIII(int[] stoneValue) {
          n = stoneValue.length;
          int[] suffixSum = new int[n + 1];
          for (int i = n - 1; i >= 0; i--) {
              suffixSum[i] = suffixSum[i + 1] + stoneValue[i];
          }

          Integer[] memo = new Integer[n];
          int aliceScore = helper(stoneValue, 0, suffixSum, memo);
          int bobScore = suffixSum[0] - aliceScore;

          if (aliceScore > bobScore) return "Alice";
          else if (bobScore > aliceScore) return "Bob";
          return "Tie";
      }

      public int helper(int[] stoneValue, int i, int[] suffixSum, Integer[] memo) {
          if (i >= n) return 0;
          if (memo[i] != null) return memo[i];

          int maxScore = Integer.MIN_VALUE;
          int currentTake = 0;

          for (int j = i; j < Math.min(i + 3, n); j++) {
              currentTake += stoneValue[j];
              int bobBest = helper(stoneValue, j + 1, suffixSum, memo);
              int aliceFuture = suffixSum[j + 1] - bobBest;

              maxScore = Math.max(maxScore, currentTake + aliceFuture);
          }
          return memo[i] = maxScore;
      }
  }
  ~~~

---

### Pattern 2: Maximize Score Difference (Zero-Sum / Negamax)
**Goal:** Find out **who wins** or the **margin of victory** without needing a prefix/suffix sum array (e.g., *Predict the Winner, Stone Game, Stone Game III*).

- **Intuition:** Instead of tracking absolute points, track only the **net lead** (`Current Player's Score - Opponent's Score`). Take the points you get right now and **subtract** the opponent's best net lead from the remaining stones.
- **Why it works:** Both players use the exact same optimal strategy. If Bob achieves a `+10` net lead on the remaining subarray, that directly translates to a `-10` disadvantage for Alice.
- **DP State:** `dp[i]` = Maximum net lead the current player can get starting from index `i`.
- **Transition:**
  ~~~java
  // My Net Lead = (Points I take now) - (Opponent's best Net Lead from index j + 1)
  int currentTake = 0;
  int maxDiff = Integer.MIN_VALUE;

  for (int j = i; j < Math.min(i + 3, n); j++) {
      currentTake += stoneValue[j];
      maxDiff = Math.max(maxDiff, currentTake - helper(stoneValue, j + 1, memo));
  }
  ~~~
- **Win Condition:** 
  - `dp[0] > 0` → First player (Alice) wins
  - `dp[0] < 0` → Second player (Bob) wins
  - `dp[0] == 0` → Tie

---

## Quick Comparison

| Feature | Pattern 1A (2-Step Min) | Pattern 1B (Subtract Opponent) | Pattern 2 (Score Difference) |
| :--- | :--- | :--- | :--- |
| **Formula** | `gain + min(next_states)` | `gain + (remSum - next_state)` | `gain - next_state` |
| **State Meaning** | Max absolute points | Max absolute points | `Alice - Bob` point difference |
| **Looks Ahead** | 2 moves (Alice → Bob → Alice) | 1 move (Current → Next) | 1 move (Current → Next) |
| **Extra Precomputation** | None | Prefix / Suffix Sum array | None |
| **Win Condition** | `score > totalSum - score` | `score > totalSum - score` | `dp[0] > 0` |

---

## Solved Problems Index

| Problem | Pattern Used | Time / Space | Solution |
| :--- | :--- | :--- | :--- |
| Predict the Winner | Pattern 2 (Score Diff) | $O(N^2)$ / $O(N^2)$ | [PredictTheWinner.java](./PredictTheWinner.java) |
| Stone Game | Pattern 2 (Score Diff) | $O(N^2)$ / $O(N^2)$ | [StoneGame.java](./StoneGame.java) |
| Stone Game II | Pattern 1B (Subtract Opponent) | $O(N^3)$ / $O(N^2)$ | [StoneGameII.java](./StoneGameII.java) |
| Stone Game III | Pattern 1A, 1B & Pattern 2 | $O(N)$ / $O(N)$ | [StoneGameIII.java](./StoneGameIII.java) |
  
  // 3. ALICE'S DECISION: Alice maximizes her current take + the worst-case scenario Bob leaves her
  maxAliceScore = Math.max(maxAliceScore, currentTake + minNextAliceScore);
