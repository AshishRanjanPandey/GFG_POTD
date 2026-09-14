# ⚡ GeeksforGeeks — Find Shortest Safe Route in a Matrix

### 📝 Problem Description
Given a 2D matrix `mat[][]` of size $n \times m$, where each cell is either `0` (landmine) or `1` (safe), find the minimum number of steps required to travel from any safe cell in the leftmost column to any safe cell in the rightmost column.

You can move in four directions: **up**, **down**, **left**, and **right**.

A cell is considered **unsafe** if it contains a landmine (`0`) or is directly adjacent (up, down, left, or right) to a landmine. Such cells cannot be visited.

Return `-1` if no safe route exists from the leftmost column to the rightmost column.

**Input:**
- A 2D integer array `mat[][]` of dimensions $n \times m$.
- Constraints: $1 \le n, m \le 10^3$, $mat[i][j] \in \{0, 1\}$.

**Output:**
- Return an integer representing the minimum number of steps (cells visited along the shortest route) to travel from column $0$ to column $m - 1$, or `-1` if no path is possible.

**Examples:**
- **Example 1:**
  - **Input:** 
    ```text
    mat = [
      [1, 0, 1, 1, 1],
      [1, 1, 1, 1, 1],
      [1, 1, 1, 1, 1],
      [1, 1, 1, 0, 1],
      [1, 1, 1, 1, 0]
    ]
    ```
  - **Output:** `6`
  - **Explanation:** Avoiding all `0`s and their immediate orthogonal neighbors, the shortest valid path from the first column to the last column takes $6$ steps.
- **Example 2:**
  - **Input:** 
    ```text
    mat = [
      [1, 1, 1, 1, 1],
      [1, 1, 0, 1, 1],
      [1, 1, 1, 1, 1]
    ]
    ```
  - **Output:** `-1`
  - **Explanation:** Landmine hazards and their adjacent cells block every possible traversal route from the leftmost column to the rightmost column.

---

### 💡 Key Insights

1. **Safety Precomputation:**
   - A cell $(r, c)$ is strictly safe if and only if:
     $$mat[r][c] = 1 \quad \text{and} \quad \forall (dr, dc) \in \{(-1,0), (1,0), (0,-1), (0,1)\}, \; mat[r + dr][c + dc] \ne 0$$
   - Precomputing safe cells into a boolean grid $\mathcal{O}(n \times m)$ prevents race conditions where marking an unsafe cell could accidentally corrupt checks for neighboring cells.

2. **Multi-Source Breadth-First Search (BFS):**
   - The objective is the unweighted shortest path starting from *any* safe cell in column $0$ and ending at *any* cell in column $m - 1$.
   - BFS guarantees the shortest path in an unweighted grid.
   - Enqueue all safe cells $(r, 0)$ from the leftmost column initially with distance $1$.
   - The first time any cell in column $m - 1$ is dequeued, its recorded distance is guaranteed to be minimal.

3. **Early Termination & Edge Cases:**
   - If $m = 1$, any safe cell in column $0$ is already in the final column, resulting in an immediate return value of $1$.
   - If no cell in column $0$ is safe, or if the BFS queue empties without reaching column $m - 1$, return `-1`.

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n \times m)$ — Each cell evaluates up to $4$ neighbors during preprocessing, and each safe cell is enqueued and visited at most once during the BFS traversal.
   - **Space Complexity:** $\mathcal{O}(n \times m)$ — Auxiliary space used for the `isSafe` grid, the `visited` array, and the BFS queue.

---

### 💻 Java Solution

```java
import java.util.ArrayDeque;
import java.util.Queue;

/**
 * Problem: Find Shortest Safe Route in a Matrix
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public int shortestPath(int[][] mat) {
        int n = mat.length;
        int m = mat[0].length;

        int[] dr = {-1, 1, 0, 0};
        int[] dc = {0, 0, -1, 1};

        // Step 1: Precompute strictly safe cells
        boolean[][] isSafe = new boolean[n][m];
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < m; c++) {
                if (mat[r][c] == 1) {
                    boolean adjacentToMine = false;
                    for (int d = 0; d < 4; d++) {
                        int nr = r + dr[d];
                        int nc = c + dc[d];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < m) {
                            if (mat[nr][nc] == 0) {
                                adjacentToMine = true;
                                break;
                            }
                        }
                    }
                    if (!adjacentToMine) {
                        isSafe[r][c] = true;
                    }
                }
            }
        }

        // Step 2: Multi-source BFS starting from all safe cells in column 0
        Queue<int[]> queue = new ArrayDeque<>();
        boolean[][] visited = new boolean[n][m];

        for (int r = 0; r < n; r++) {
            if (isSafe[r][0]) {
                queue.offer(new int[]{r, 0, 1}); // {row, col, distance}
                visited[r][0] = true;
            }
        }

        // Step 3: Traverse level by level
        while (!queue.isEmpty()) {
            int[] current = queue.poll();
            int r = current[0];
            int c = current[1];
            int dist = current[2];

            // Target reached: rightmost column
            if (c == m - 1) {
                return dist;
            }

            for (int d = 0; d < 4; d++) {
                int nr = r + dr[d];
                int nc = c + dc[d];

                if (nr >= 0 && nr < n && nc >= 0 && nc < m) {
                    if (isSafe[nr][nc] && !visited[nr][nc]) {
                        visited[nr][nc] = true;
                        queue.offer(new int[]{nr, nc, dist + 1});
                    }
                }
            }
        }

        return -1;
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        int[][] mat1 = {
            {1, 0, 1, 1, 1},
            {1, 1, 1, 1, 1},
            {1, 1, 1, 1, 1},
            {1, 1, 1, 0, 1},
            {1, 1, 1, 1, 0}
        };
        System.out.println(sol.shortestPath(mat1)); // Output: 6

        int[][] mat2 = {
            {1, 1, 1, 1, 1},
            {1, 1, 0, 1, 1},
            {1, 1, 1, 1, 1}
        };
        System.out.println(sol.shortestPath(mat2)); // Output: -1
    }
}
