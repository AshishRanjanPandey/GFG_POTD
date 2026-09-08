# ⚡ GeeksforGeeks — Word Search in a Grid

### 📝 Problem Description
Given a 2D grid `mat[][]` of size $n \times m$ consisting of characters and a string `word`, find all unique starting positions where `word` occurs in the grid.

The word can be formed starting from any cell and moving in any of the **8 directions** (2 horizontal, 2 vertical, and 4 diagonal) along a straight line without changing direction. Each cell can be used at most once per occurrence.

Return all unique starting coordinates $[r, c]$ in **lexicographically smallest order**.

**Input:**
- A 2D character matrix `mat` of size $n \times m$.
- A string `word`.
- Constraints: $1 \le n \le m \le 50$, $1 \le |word| \le 20$.

**Output:**
- A list of integer coordinate pairs `[r, c]` representing each unique starting cell.

**Examples:**
- **Example 1:**
  - **Input:** `mat = [['a','b','a','b'], ['a','b','e','b'], ['e','b','e','b']], word = "abe"`
  - **Output:** `[[0, 0], [0, 2], [1, 0]]`
  - **Explanation:**
    - From `(0, 0)`: down-right diagonal forms `"abe"`.
    - From `(0, 2)`: down-left diagonal forms `"abe"`.
    - From `(1, 0)`: horizontally right forms `"abe"`.
- **Example 2:**
  - **Input:** `mat = [['G','E','E','K','S','F','O','R','G','E','E','K','S'], ['G','E','E','K','S','Q','U','I','Z','G','E','E','K'], ['I','D','E','Q','A','P','R','A','C','T','I','C','E']], word = "GEEKS"`
  - **Output:** `[[0, 0], [0, 8], [1, 0]]`
  - **Explanation:**
    - From `(0, 0)`, `(0, 8)`, and `(1, 0)`, the word `"GEEKS"` appears horizontally to the right.

---

### 💡 Key Insights
1. **Row-Major Traversal Guarantees Lexicographical Order:**
   - Scanning rows $r$ from $0$ to $n-1$ and columns $c$ from $0$ to $m-1$ naturally visits cells in lexicographical order $(r_1, c_1) < (r_2, c_2)$.

2. **Direction Vectors for 8-Directional Straight Line Movement:**
   - Straight-line movements correspond to 8 directional offsets:
     $$\Delta r \in \{-1, 0, 1\}, \quad \Delta c \in \{-1, 0, 1\} \quad \text{excluding } (0, 0)$$
   - Direction vectors:
     - `dr = {-1, -1, -1,  0, 0,  1, 1, 1}`
     - `dc = {-1,  0,  1, -1, 1, -1, 0, 1}`

3. **Boundary Pruning:**
   - Before character-by-character comparison, verify whether the ending index lies inside grid boundaries:
     $$\text{endR} = r + dr[d] \times (|word| - 1), \quad \text{endC} = c + dc[d] \times (|word| - 1)$$
   - If $0 \le \text{endR} < n$ and $0 \le \text{endC} < m$ does not hold, the target direction can be skipped immediately.

4. **Avoiding Duplicate Coordinates:**
   - If a word can be formed in multiple valid directions starting from the same cell $(r, c)$, record $(r, c)$ **once** and break out of the direction loop.

5. **Complexity Analysis:**
   - **Time Complexity:** $\mathcal{O}(n \cdot m \cdot 8 \cdot |word|)$. For $n, m \le 50$ and $|word| \le 20$, maximum operations $\le 50 \times 50 \times 8 \times 20 \approx 4 \times 10^5$, easily executing in $< 0.05$ seconds.
   - **Space Complexity:** $\mathcal{O}(1)$ auxiliary space excluding the returned result list.

---

### 💻 Java Solution

```java
import java.util.ArrayList;

/**
 * Problem: Word Search in a Grid
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public ArrayList<ArrayList<Integer>> searchWord(char[][] mat, String word) {
        ArrayList<ArrayList<Integer>> result = new ArrayList<>();
        int n = mat.length;
        int m = mat[0].length;
        int wordLen = word.length();

        // 8 directional deltas: Up-Left, Up, Up-Right, Left, Right, Down-Left, Down, Down-Right
        int[] dr = {-1, -1, -1,  0, 0,  1, 1, 1};
        int[] dc = {-1,  0,  1, -1, 1, -1, 0, 1};

        // Traverse in row-major order to naturally satisfy lexicographical ordering
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < m; c++) {
                // Quick filter: starting character must match word[0]
                if (mat[r][c] != word.charAt(0)) {
                    continue;
                }

                // Check all 8 directions
                for (int d = 0; d < 8; d++) {
                    int endR = r + dr[d] * (wordLen - 1);
                    int endC = c + dc[d] * (wordLen - 1);

                    // Skip direction if the entire word length falls outside the boundaries
                    if (endR < 0 || endR >= n || endC < 0 || endC >= m) {
                        continue;
                    }

                    int k = 0;
                    int currR = r;
                    int currC = c;

                    while (k < wordLen && mat[currR][currC] == word.charAt(k)) {
                        currR += dr[d];
                        currC += dc[d];
                        k++;
                    }

                    // Complete word match found
                    if (k == wordLen) {
                        ArrayList<Integer> coord = new ArrayList<>();
                        coord.add(r);
                        coord.add(c);
                        result.add(coord);

                        // Break immediately to prevent duplicate coordinate entry
                        break;
                    }
                }
            }
        }

        return result;
    }
}
