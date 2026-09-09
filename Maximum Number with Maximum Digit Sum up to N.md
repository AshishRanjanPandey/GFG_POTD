# ⚡ GeeksforGeeks — Maximum Number with Maximum Digit Sum up to N

### 📝 Problem Description
Given a number $n$, find a number in the range from $1$ to $n$ such that its digit sum is maximum. If there are multiple such numbers, return the largest of them.

**Input:**
- A positive integer $n$ ($1 \le n \le 10^9$).

**Output:**
- Return a single integer representing the number $\le n$ with the maximum digit sum (largest in case of ties).

**Examples:**
- **Example 1:**
  - **Input:** `n = 48`
  - **Output:** `48`
  - **Explanation:** There are two numbers with maximum digit sum = 12: `48` and `39`. Since $48 > 39$, `48` is the answer.
- **Example 2:**
  - **Input:** `n = 90`
  - **Output:** `89`
  - **Explanation:** `89` gives the largest digit sum ($8 + 9 = 17$) in the range from $1$ to $90$. Hence the answer is `89`.

---

### 💡 Key Insights
1. **Candidate Construction:**
   - To maximize the digit sum while ensuring the value is $\le n$, the optimal number must take one of two forms:
     1. The number $n$ itself.
     2. A number obtained by choosing an index $i$, decrementing the $i$-th digit by $1$, and replacing all digits to its right with $9$.
   - Any other construction strictly decreases the digit sum or results in a number larger than $n$.

2. **Greedy Traversal:**
   - Convert $n$ into a digit array of length $L$.
   - Initialize the best candidate as $n$ and compute its digit sum.
   - Iterate through each position $i$ from left to right:
     - If digit at $i$ is `'0'`, it cannot be decremented without borrowing, so skip.
     - Otherwise, create a candidate by setting `candidate[i] = candidate[i] - 1` and `candidate[j] = '9'` for all $j > i$.
     - Compute the digit sum of this candidate.
     - Update the best answer if the candidate achieves a strictly greater digit sum, or an equal sum with a strictly larger numerical value.

3. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(L^2)$ where $L$ is the number of digits in $n$. For $n \le 10^9$, $L \le 10$, making the runtime $\approx 100$ operations ($< 0.001$ seconds).
   - **Space Complexity:** $\mathcal{O}(L)$ auxiliary space to hold the digit array representation.

---

### 💻 Java Solution

```java
/**
 * Problem: Maximum Number with Maximum Digit Sum up to N
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public static long findMaxDigitSumNum(long n) {
        String s = Long.toString(n);
        long bestNum = n;
        int bestSum = getDigitSum(s);

        char[] digits = s.toCharArray();

        for (int i = 0; i < digits.length; i++) {
            // Cannot decrement '0' directly
            if (digits[i] == '0') {
                continue;
            }

            char[] candidate = digits.clone();
            candidate[i] = (char) (candidate[i] - 1);

            for (int j = i + 1; j < candidate.length; j++) {
                candidate[j] = '9';
            }

            String candStr = new String(candidate);
            long candNum = Long.parseLong(candStr);
            int candSum = getDigitSum(candStr);

            if (candSum > bestSum || (candSum == bestSum && candNum > bestNum)) {
                bestSum = candSum;
                bestNum = candNum;
            }
        }

        return bestNum;
    }

    private static int getDigitSum(String s) {
        int sum = 0;
        for (int i = 0; i < s.length(); i++) {
            sum += s.charAt(i) - '0';
        }
        return sum;
    }

    public static void main(String[] args) {
        System.out.println(findMaxDigitSumNum(48)); // Output: 48
        System.out.println(findMaxDigitSumNum(90)); // Output: 89
        System.out.println(findMaxDigitSumNum(100)); // Output: 99
    }
}
