# ⚡ GeeksforGeeks — Maximum Product Subsequence of Size k

### 📝 Problem Description
Given an array `arr[]` of integers and an integer $k$, find a subsequence of size $k$ whose product is maximum among all possible subsequences of size $k$. Return the maximum product that can be obtained.

**Input:**
- An integer array `arr[]` ($1 \le |arr| \le 30$).
- Element range: $-10 \le arr[i] \le 10$.
- An integer $k$ ($1 \le k \le |arr|$).

**Output:**
- Return an integer representing the maximum product of any subsequence of size $k$.

**Examples:**
- **Example 1:**
  - **Input:** `arr[] = [1, 2, 0, 3], k = 2`
  - **Output:** `6`
  - **Explanation:** Subsequence $\{2, 3\}$ yields the maximum product: $2 \times 3 = 6$.
- **Example 2:**
  - **Input:** `arr[] = [1, 2, -1, -3, -6, 4], k = 4`
  - **Output:** `144`
  - **Explanation:** Subsequence $\{2, -3, -6, 4\}$ yields the maximum product: $2 \times (-3) \times (-6) \times 4 = 144$.

---

### 💡 Key Insights

1. **Sorting Foundation:**
   - Sorting `arr[]` in non-decreasing order clusters the most extreme magnitudes at both ends:
     - Negative numbers with large absolute values are positioned on the far left (`arr[0], arr[1], ...`).
     - Positive numbers with large values are positioned on the far right (`..., arr[n-2], arr[n-1]`).

2. **Mandatory Full Selection ($k = n$):**
   - When $k$ equals the size of the array, every element must be included in the subsequence. The result is simply the product of all elements in `arr[]`.

3. **All Non-Positive Numbers with Odd $k$:**
   - If the largest number in the sorted array is non-positive (`arr[n - 1] <= 0`) and $k$ is odd:
     - Any product of non-zero numbers will inevitably be negative.
     - To maximize a negative product, we must minimize its absolute value.
     - This is achieved by picking the $k$ largest elements in the array (i.e., the elements closest to zero: `arr[n - 1], arr[n - 2], ..., arr[n - k]`).

4. **Greedy Two-Pointer Strategy (When $\ge 0$ Product is Achievable):**
   - **Odd $k$ Step:** If $k$ is odd, we start by including the largest available element `arr[n - 1]` in the product and decrementing $k$ by $1$. This leaves an even number of elements to select.
   - **Pairwise Comparison:** Since the remaining $k$ is even, elements can be incorporated in pairs to guarantee a non-negative contribution:
     - Pair from left: `leftProd = arr[left] * arr[left + 1]` (two large-magnitude negative numbers produce a positive product).
     - Pair from right: `rightProd = arr[right] * arr[right - 1]` (two large positive numbers produce a positive product).
   - In each step, choose $\max(\text{leftProd}, \text{rightProd})$, multiply it into the running product, advance the chosen pointers by $2$, and decrement $k$ by $2$.

5. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n \log n)$ due to the sorting step, followed by an $\mathcal{O}(k)$ greedy scan. For $n \le 30$, this runs in under $1$ ms.
   - **Space Complexity:** $\mathcal{O}(1)$ auxiliary space (ignoring $\mathcal{O}(\log n)$ stack space used internally by standard sorting algorithms).

---

### 💻 Java Solution

```java
import java.util.Arrays;

/**
 * Problem: Maximum Product Subsequence of Size k
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public int maxProduct(int[] arr, int k) {
        int n = arr.length;
        Arrays.sort(arr);

        // Case 1: All elements must be included
        if (k == n) {
            int prod = 1;
            for (int x : arr) {
                prod *= x;
            }
            return prod;
        }

        // Case 2: Maximum possible product is negative (all elements <= 0 and k is odd)
        // Pick the k numbers closest to 0 to minimize absolute value
        if (arr[n - 1] <= 0 && (k % 2 != 0)) {
            int prod = 1;
            for (int i = n - 1; i >= n - k; i--) {
                prod *= arr[i];
            }
            return prod;
        }

        // Case 3: A non-negative product is achievable
        int left = 0;
        int right = n - 1;
        int prod = 1;

        // If k is odd, pick the largest positive element first to reduce to even k
        if (k % 2 != 0) {
            prod *= arr[right];
            right--;
            k--;
        }

        // Greedily pick the best pair from left or right
        while (k > 0) {
            int leftProd = arr[left] * arr[left + 1];
            int rightProd = arr[right] * arr[right - 1];

            if (leftProd > rightProd) {
                prod *= leftProd;
                left += 2;
            } else {
                prod *= rightProd;
                right -= 2;
            }
            k -= 2;
        }

        return prod;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        int[] arr1 = {1, 2, 0, 3};
        System.out.println(sol.maxProduct(arr1, 2)); // Output: 6

        int[] arr2 = {1, 2, -1, -3, -6, 4};
        System.out.println(sol.maxProduct(arr2, 4)); // Output: 144

        int[] arr3 = {-5, -4, -3, -2, -1};
        System.out.println(sol.maxProduct(arr3, 3)); // Output: -24
    }
}
