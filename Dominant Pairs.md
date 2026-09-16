# ⚡ GeeksforGeeks — Dominant Pairs

### 📝 Problem Description
Given an even-sized integer array `arr[]`, count the number of dominant pairs. A pair of indices $(i, j)$ is called dominant if all of the following conditions hold:
- $0 \le i < \frac{\text{arr.size()}}{2}$
- $\frac{\text{arr.size()}}{2} \le j < \text{arr.size()}$
- $\text{arr}[i] \ge 5 \times \text{arr}[j]$

Return the total number of dominant pairs.

**Input:**
- An even-sized integer array `arr[]` ($1 \le \text{arr.size()} \le 10^4$, $-10^4 \le \text{arr}[i] \le 10^4$).

**Output:**
- Return an integer representing the total count of valid dominant pairs $(i, j)$.

**Examples:**
- **Example 1:**
  - **Input:** `arr[] = [10, 2, 2, 1]`
  - **Output:** `2`
  - **Explanation:** First half: `[10, 2]`, Second half: `[2, 1]`. The valid pairs are:
    - $\{0, 2\}: 10 \ge 5 \times 2$
    - $\{0, 3\}: 10 \ge 5 \times 1$
- **Example 2:**
  - **Input:** `arr[] = [10, 8, 2, 1, 1, 2]`
  - **Output:** `5`
  - **Explanation:** First half: `[10, 8, 2]`, Second half: `[1, 1, 2]`. The valid pairs are:
    - $\{0, 3\}: 10 \ge 5 \times 1$
    - $\{0, 4\}: 10 \ge 5 \times 1$
    - $\{0, 5\}: 10 \ge 5 \times 2$
    - $\{1, 3\}: 8 \ge 5 \times 1$
    - $\{1, 4\}: 8 \ge 5 \times 1$

---

### 💡 Key Insights

1. **Independent Subarrays:**
   - Any valid index $i$ must come strictly from the first half ($[0, n/2 - 1]$), and any valid index $j$ must come strictly from the second half ($[n/2, n - 1]$).
   - Because cross-half index comparisons are never required, reordering the elements inside either half independently does not alter the frequency or validity of value pairs $(arr[i], arr[j])$.

2. **Sorting Strategy:**
   - Sort the first half `arr[0 ... mid - 1]` in non-decreasing order.
   - Sort the second half `arr[mid ... n - 1]` in non-decreasing order.

3. **Two-Pointer Scan:**
   - Initialize pointer $j = \text{mid}$.
   - Iterate pointer $i$ from $0$ to $\text{mid} - 1$:
     - While $j < n$ and $\text{arr}[i] \ge 5 \times \text{arr}[j]$, advance $j$.
     - Since the second half is sorted, every element from index $\text{mid}$ to $j - 1$ satisfies $\text{arr}[i] \ge 5 \times \text{arr}[k]$.
     - Add $(j - \text{mid})$ to the running count.
   - Because the first half is also sorted, as $\text{arr}[i]$ increases, the boundary $j$ only moves forward (monotonicity), giving a linear sweep after sorting.

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n \log n)$ — Sorting two subarrays of size $n/2$ takes $\mathcal{O}(n \log n)$ time. The two-pointer sweep completes in $\mathcal{O}(n)$ time.
   - **Space Complexity:** $\mathcal{O}(1)$ auxiliary space — Performed in place using standard primitive sorting.

---

### 💻 Java Solution

```java
import java.util.Arrays;

/**
 * Problem: Dominant Pairs
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public int dominantPairs(int[] arr) {
        int n = arr.length;
        int mid = n / 2;

        // Sort both halves independently
        Arrays.sort(arr, 0, mid);
        Arrays.sort(arr, mid, n);

        int count = 0;
        int j = mid;

        // Two-pointer linear sweep
        for (int i = 0; i < mid; i++) {
            while (j < n && arr[i] >= 5 * arr[j]) {
                j++;
            }
            count += (j - mid);
        }

        return count;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        int[] arr1 = {10, 2, 2, 1};
        System.out.println(sol.dominantPairs(arr1)); // Output: 2

        int[] arr2 = {10, 8, 2, 1, 1, 2};
        System.out.println(sol.dominantPairs(arr2)); // Output: 5
    }
}
