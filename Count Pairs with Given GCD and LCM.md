# ⚡ GeeksforGeeks — Count Pairs with Given GCD and LCM

### 📝 Problem Description
Given two positive integers $x$ and $y$ representing the Greatest Common Divisor (GCD) and Least Common Multiple (LCM) of two unknown positive integers $a$ and $b$, count the number of valid pairs $(a, b)$ satisfying these conditions.

Note that $(a, b)$ and $(b, a)$ are treated as distinct pairs when $a \ne b$.

**Input:**
- Two positive integers $x$ and $y$ ($1 \le x, y \le 10^4$).

**Output:**
- Return an integer representing the total count of valid pairs $(a, b)$.

**Examples:**
- **Example 1:**
  - **Input:** `x = 2, y = 12`
  - **Output:** `4`
  - **Explanation:** The valid pairs are $(2, 12)$, $(4, 6)$, $(6, 4)$, and $(12, 2)$. Each pair satisfies $\gcd(a, b) = 2$ and $\text{lcm}(a, b) = 12$.
- **Example 2:**
  - **Input:** `x = 6, y = 4`
  - **Output:** `0`
  - **Explanation:** The LCM must always be a multiple of the GCD. Since $4$ is not divisible by $6$, no valid pair exists.

---

### 💡 Key Insights

1. **Divisibility Condition:**
   - By definition, $\gcd(a, b)$ must divide $\text{lcm}(a, b)$. Therefore, if $y \pmod x \ne 0$, it is mathematically impossible for any such pair to exist, and the answer is immediately $0$.

2. **Coprime Reduction:**
   - Let $a = x \cdot a'$ and $b = x \cdot b'$.
   - Since $\gcd(a, b) = x$, we have:
     $$\gcd(a', b') = 1$$
   - Using the relationship $\gcd(a, b) \times \text{lcm}(a, b) = a \times b$:
     $$x \cdot y = (x \cdot a') \cdot (x \cdot b') \implies a' \cdot b' = \frac{y}{x}$$
   - Let $k = \frac{y}{x}$. The problem reduces to finding the number of ordered pairs of coprime positive integers $(a', b')$ such that $a' \cdot b' = k$.

3. **Prime Factorization Strategy:**
   - Let the prime factorization of $k$ be:
     $$k = p_1^{e_1} \cdot p_2^{e_2} \cdots p_d^{e_d}$$
     where $d$ is the number of distinct prime factors of $k$.
   - Because $\gcd(a', b') = 1$, no prime factor $p_i$ can be shared between $a'$ and $b'$.
   - Consequently, for each distinct prime power $p_i^{e_i}$, the entire factor must be assigned either to $a'$ or to $b'$:
     - Choice 1: $p_i^{e_i} \mid a'$ and $p_i \nmid b'$
     - Choice 2: $p_i^{e_i} \mid b'$ and $p_i \nmid a'$
   - Since each of the $d$ distinct prime factors provides $2$ independent choices, the total number of valid ordered pairs $(a', b')$ is:
     $$2^d$$

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(\sqrt{k}) = \mathcal{O}\left(\sqrt{\frac{y}{x}}\right)$. For $y \le 10^4$, $\sqrt{y} \le 100$, executing in less than $1$ ms.
   - **Space Complexity:** $\mathcal{O}(1)$ auxiliary space as only scalar counters are maintained.

---

### 💻 Java Solution

```java
/**
 * Problem: Count Pairs with Given GCD and LCM
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public int pairCount(int x, int y) {
        // LCM must be divisible by GCD
        if (y % x != 0) {
            return 0;
        }

        int k = y / x;
        int distinctPrimeFactors = 0;

        // Find the number of distinct prime factors of k
        int temp = k;
        for (int i = 2; i * i <= temp; i++) {
            if (temp % i == 0) {
                distinctPrimeFactors++;
                while (temp % i == 0) {
                    temp /= i;
                }
            }
        }

        // If remaining temp > 1, it is itself a prime factor
        if (temp > 1) {
            distinctPrimeFactors++;
        }

        // Total valid ordered pairs = 2^d
        return 1 << distinctPrimeFactors;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        System.out.println(sol.pairCount(2, 12)); // Output: 4
        System.out.println(sol.pairCount(6, 4));  // Output: 0
        System.out.println(sol.pairCount(3, 3));  // Output: 1
    }
}
