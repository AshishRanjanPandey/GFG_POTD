# ⚡ GeeksforGeeks — Minimum Unused Elements in Bitonic Partitioning

### 📝 Problem Description
Given an integer array `arr[]` of size $n$, partition its elements into two disjoint subsequences:
1. A **strictly increasing subsequence**
2. A **strictly decreasing subsequence**

Each element of `arr` can belong to at most one of these subsequences, and some elements may remain unselected (unused). Determine the **minimum number of elements that cannot be included** in either subsequence.

**Input:**
- An integer array `arr` of size $n$ ($1 \le n \le 100$).
- Each element satisfies $1 \le arr[i] \le 100$.

**Output:**
- Return a single integer representing the minimum number of elements that remain unused after partitioning.

**Examples:**
- **Example 1:**
  - **Input:** `arr = [7, 8, 1, 2, 4, 6, 3, 5, 2, 1, 8, 7]`
  - **Output:** `2`
  - **Explanation:**
    - Increasing subsequence: `[1, 2, 4, 5, 8]` (length 5)
    - Decreasing subsequence: `[7, 6, 3, 2, 1]` (length 5)
    - Total selected: $5 + 5 = 10$ elements.
    - Unused elements: `arr[1] = 8` and `arr[11] = 7` (count = $12 - 10 = 2$).
- **Example 2:**
  - **Input:** `arr = [1, 4, 2, 3, 3, 2, 4]`
  - **Output:** `0`
  - **Explanation:**
    - Increasing subsequence: `[1, 2, 3, 4]` (length 4)
    - Decreasing subsequence: `[4, 3, 2]` (length 3)
    - Total selected: $4 + 3 = 7$ elements.
    - Unused elements: `0` (all elements are placed).

---

### 💡 Key Insights
1. **Duality of the Problem:**
   - Minimizing the number of unused elements is equivalent to **maximizing the total number of elements** distributed between the two subsequences:
     $$\text{Min Unused} = n - \max(\text{elements in increasing} + \text{elements in decreasing})$$

2. **Sequential Decision-Making via Dynamic Programming:**
   - As we process each element $x = arr[i]$ from left to right, we have three mutually exclusive choices:
     1. Append $x$ to the increasing subsequence (valid only if $x > \text{last\_inc}$).
     2. Append $x$ to the decreasing subsequence (valid only if $x < \text{last\_dec}$).
     3. Skip $x$ (leave it unused).
   - Because future compatibility depends strictly on the *last chosen value* in each subsequence, our state needs to track only:
     - `inc`: the tail element of the strictly increasing subsequence ($0$ denotes empty).
     - `dec`: the tail element of the strictly decreasing subsequence ($101$ denotes empty / $+\infty$).

3. **State Space and Transition:**
   - Since $arr[i] \le 100$, both `inc` and `dec` take values in the range $[0, 101]$.
   - Let $dp[inc][dec]$ be the maximum number of elements included across both sequences given tail values `inc` and `dec`.
   - Transition for each element $x$:
     - **Skip:** $nextDp[inc][dec] = \max(nextDp[inc][dec], dp[inc][dec])$
     - **Add to Inc:** If $x > inc$, $nextDp[x][dec] = \max(nextDp[x][dec], dp[inc][dec] + 1)$
     - **Add to Dec:** If $x < dec$, $nextDp[inc][x] = \max(nextDp[inc][x], dp[inc][dec] + 1)$

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n \cdot V^2)$ where $V = 101$. For $n = 100$, operations are $\approx 100 \times 101^2 \approx 1.02 \times 10^6$, running in under $0.05$ seconds.
   - **Space Complexity:** $\mathcal{O}(V^2)$ auxiliary space — rolling 2D DP tables of size $102 \times 102$.

---

### 💻 Java Solution

```java
import java.util.Arrays;

/**
 * Problem: Minimum Unused Elements in Bitonic Partitioning
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public static int minUnusedElements(int[] arr) {
        int n = arr.length;
        int maxVal = 101; // Values are in [1, 100]; 101 represents infinity for decreasing sequence

        // dp[inc][dec] stores the max elements selected so far
        // inc: tail of increasing sequence (0 = empty)
        // dec: tail of decreasing sequence (101 = empty)
        int[][] dp = new int[maxVal + 1][maxVal + 1];
        for (int[] row : dp) {
            Arrays.fill(row, -1);
        }

        // Base case: both sequences are empty with 0 elements selected
        dp[0][101] = 0;

        for (int x : arr) {
            int[][] nextDp = new int[maxVal + 1][maxVal + 1];
            for (int[] row : nextDp) {
                Arrays.fill(row, -1);
            }

            for (int inc = 0; inc <= maxVal; inc++) {
                for (int dec = 0; dec <= maxVal; dec++) {
                    if (dp[inc][dec] == -1) continue;

                    int cur = dp[inc][dec];

                    // Choice 1: Leave x unused
                    if (cur > nextDp[inc][dec]) {
                        nextDp[inc][dec] = cur;
                    }

                    // Choice 2: Add x to the increasing subsequence
                    if (x > inc && cur + 1 > nextDp[x][dec]) {
                        nextDp[x][dec] = cur + 1;
                    }

                    // Choice 3: Add x to the decreasing subsequence
                    if (x < dec && cur + 1 > nextDp[inc][x]) {
                        nextDp[inc][x] = cur + 1;
                    }
                }
            }
            dp = nextDp;
        }

        // Find the maximum number of elements included
        int maxIncluded = 0;
        for (int inc = 0; inc <= maxVal; inc++) {
            for (int dec = 0; dec <= maxVal; dec++) {
                if (dp[inc][dec] > maxIncluded) {
                    maxIncluded = dp[inc][dec];
                }
            }
        }

        return n - maxIncluded;
    }
}
```
| Approach | Time Complexity | Space Complexity | Status |
| :--- | :--- | :--- | :--- |
| **Brute Force (3-Way Branching Recursion)** | $\mathcal{O}(3^n)$ | $\mathcal{O}(n)$ | ❌ **TLE** ($3^{100} \approx 5 \times 10^{47}$ ops) |
| **Full 3D Dynamic Programming** | $\mathcal{O}(n \cdot V^2)$ | $\mathcal{O}(n \cdot V^2)$ | ⚠️ Passes, but uses $\approx 4\text{ MB}$ memory |
| **Space-Optimized 2D Rolling DP** | $\mathcal{O}(n \cdot V^2)$ | $\mathcal{O}(V^2)$ | ✅ **Optimal** ($\approx 10^6$ ops, $\approx 80\text{ KB}$) |
