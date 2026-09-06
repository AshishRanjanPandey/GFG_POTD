# ⚡ GeeksforGeeks — Sum of Products (Pairwise Bitwise AND)

### 📝 Problem Description
Given an array `arr[]` of integers, calculate the sum of bitwise AND for all pairs of elements such that the first index is less than the second index ($i < j$).

**Input:**
- An integer array `arr` of size $n$ ($1 \le n \le 10^5$).
- Each element satisfies $1 \le arr[i] \le 10^8$.

**Output:**
- Return a single 64-bit integer representing the sum of bitwise AND of all pairs $(arr[i] \ \& \ arr[j])$ for all $0 \le i < j < n$.

**Examples:**
- **Example 1:**
  - **Input:** `arr = [5, 10, 15]`
  - **Output:** `15`
  - **Explanation:**
    - $(5, 10) \implies 5 \ \& \ 10 = 0$
    - $(5, 15) \implies 5 \ \& \ 15 = 5$
    - $(10, 15) \implies 10 \ \& \ 15 = 10$
    - Sum = $0 + 5 + 10 = 15$
- **Example 2:**
  - **Input:** `arr = [10, 20, 30, 40]`
  - **Output:** `46`
  - **Explanation:**
    - $(10, 20) \implies 0$, $(10, 30) \implies 10$, $(10, 40) \implies 8$
    - $(20, 30) \implies 20$, $(20, 40) \implies 0$, $(30, 40) \implies 8$
    - Sum = $0 + 10 + 8 + 20 + 0 + 8 = 46$

---

### 💡 Key Insights
1. **Inefficiency of Brute Force:**
   - Evaluating all pairs $(i, j)$ requires $\frac{n(n - 1)}{2}$ operations.
   - For $n = 10^5$, this results in $\approx 5 \times 10^9$ operations, leading to a **Time Limit Exceeded (TLE)** verdict.

2. **Bit-by-Bit Contribution:**
   - Instead of computing pair-by-pair values, consider the contribution of each bit position $k$ independently (from $k = 0$ to $31$).
   - In a bitwise AND operation, the $k$-th bit of $(arr[i] \ \& \ arr[j])$ evaluates to $1$ **if and only if** both $arr[i]$ and $arr[j]$ have their $k$-th bit set.
   - If exactly $c$ elements in the array have their $k$-th bit set, the number of valid pairs is:
     $$\text{pairs} = \binom{c}{2} = \frac{c \times (c - 1)}{2}$$
   - The total numerical contribution of bit $k$ to the answer is:
     $$\text{contribution}_k = \frac{c \times (c - 1)}{2} \times 2^k$$

3. **Edge Cases & Overflow Prevention:**
   - **Integer Overflow:** The total answer can easily exceed $2^{31} - 1$. Always use `long` for the running sum, bit-count tracking, and the power-of-two shift (`1L << bit`).
   - **Bit Range:** Because $arr[i] \le 10^8 < 2^{27}$, checking bits $0$ through $31$ is sufficient and guarantees complete coverage.

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(32 \cdot n) \equiv \mathcal{O}(n)$ — scanning the array 32 times performs $\approx 3.2 \times 10^6$ operations, executing in under $0.05$ seconds.
   - **Space Complexity:** $\mathcal{O}(1)$ auxiliary space — only counter variables are maintained.

---

### 💻 Java Solution

```java
/**
 * Problem: Sum of Products / Pairwise Bitwise AND
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    static long pairAndSum(int n, long arr[]) {
        long totalSum = 0;

        // arr[i] <= 10^8 < 2^27; iterating through 32 bits covers all possible set bits
        for (int bit = 0; bit < 32; bit++) {
            long countSetBits = 0;

            // Count how many numbers have their bit-th position set to 1
            for (int i = 0; i < n; i++) {
                if (((arr[i] >> bit) & 1L) == 1L) {
                    countSetBits++;
                }
            }

            // Total pairs where both elements have the bit-th bit set
            long pairs = (countSetBits * (countSetBits - 1)) / 2;

            // Add contribution of this bit position
            totalSum += pairs * (1L << bit);
        }

        return totalSum;
    }
}
```
### 📊 Complexity Analysis

| Metric | Complexity | Explanation |
| :--- | :--- | :--- |
| **Time Complexity** | $\mathcal{O}(32 \cdot n) \equiv \mathcal{O}(n)$ | Iterates through the $n$-element array $32$ times (once per bit position). For $n = 10^5$, total operations are $\approx 3.2 \times 10^6$, running well within the time limit. |
| **Space Complexity** | $\mathcal{O}(1)$ | Uses only a few 64-bit primitive variables (`totalSum`, `countSetBits`, `pairs`) without allocating extra dynamic memory. |
