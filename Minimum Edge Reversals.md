# ⚡ GeeksforGeeks — Minimum Edge Reversals to Connect Directed Graph

### 📝 Problem Description
Given a directed graph with $n$ vertices numbered from $1$ to $n$ and an array `edges[][]` of size $m$, where each entry `edges[i] = [u, v]` represents a directed edge from vertex $u$ to vertex $v$. You are also given a source vertex `src` and a destination vertex `dst`.

Find the minimum number of edges that need to be reversed so that there exists at least one valid path from `src` to `dst`. If it is impossible to reach `dst` from `src` even after reversing edges, return `-1`.

**Input:**
- An integer $n$ ($1 \le n \le 10^5$) representing the number of vertices.
- A 2D integer array `edges[][]` ($1 \le m \le 10^5$) where each entry is $[u, v]$ ($1 \le u, v \le n$).
- Two integers `src` and `dst` ($1 \le \text{src}, \text{dst} \le n$).

**Output:**
- Return an integer representing the minimum number of edge reversals required, or `-1` if unreachable.

**Examples:**
- **Example 1:**
  - **Input:** `n = 3, edges = [[1, 2], [3, 2]], src = 1, dst = 3`
  - **Output:** `1`
  - **Explanation:** Reversing the edge $3 \to 2$ to $2 \to 3$ yields the directed path $1 \to 2 \to 3$. Thus, $1$ reversal is needed.
- **Example 2:**
  - **Input:** `n = 4, edges = [[1, 2], [2, 3], [3, 4]], src = 1, dst = 4`
  - **Output:** `0`
  - **Explanation:** A path $1 \to 2 \to 3 \to 4$ already exists without reversing any edges.

---

### 💡 Key Insights

1. **Graph Transformation into Weighted Shortest Path:**
   - Traversing an existing edge $u \to v$ in its original direction costs **0 reversals**.
   - Traversing against an edge (i.e., moving from $v$ to $u$) requires reversing that edge, which costs **1 reversal**.
   - For every directed edge $u \to v$, we construct a bidirectional graph containing:
     - Directed edge $u \to v$ with weight $0$.
     - Directed edge $v \to u$ with weight $1$.

2. **0-1 BFS vs. Dijkstra's Algorithm:**
   - Because all edge weights in the transformed graph are either $0$ or $1$, we can find the single-source shortest path using **0-1 BFS** (utilizing a `Deque`) in $\mathcal{O}(V + E)$ time instead of a standard $\mathcal{O}(E \log V)$ priority queue.
   - When relaxing an edge:
     - If the weight is $0$, push the neighbor to the **front** of the deque (`addFirst`).
     - If the weight is $1$, push the neighbor to the **back** of the deque (`addLast`).

3. **Base Cases & Unreachability:**
   - If `src == dst`, the minimum cost is trivially `0`.
   - If the algorithm terminates and the distance to `dst` remains $\infty$, return `-1`.

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n + m)$ — each node is processed at most twice in the deque, and every edge is explored once.
   - **Space Complexity:** $\mathcal{O}(n + m)$ auxiliary space to store the adjacency list and distance array.

---

### 💻 Java Solution

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Deque;
import java.util.List;

/**
 * Problem: Minimum Edge Reversals to Connect Directed Graph
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    static class Edge {
        int to;
        int weight;

        Edge(int to, int weight) {
            this.to = to;
            this.weight = weight;
        }
    }

    public int minimumEdgeReversal(int[][] edges, int n, int src, int dst) {
        if (src == dst) {
            return 0;
        }

        // Build adjacency list for 0-1 graph
        List<List<Edge>> adj = new ArrayList<>(n + 1);
        for (int i = 0; i <= n; i++) {
            adj.add(new ArrayList<>());
        }

        for (int[] edge : edges) {
            int u = edge[0];
            int v = edge[1];
            adj.get(u).add(new Edge(v, 0)); // Original edge: cost 0
            adj.get(v).add(new Edge(u, 1)); // Reversed edge: cost 1
        }

        // Distance array
        int[] dist = new int[n + 1];
        Arrays.fill(dist, Integer.MAX_VALUE);

        // 0-1 BFS using Deque
        Deque<Integer> deque = new ArrayDeque<>();
        dist[src] = 0;
        deque.offerFirst(src);

        while (!deque.isEmpty()) {
            int curr = deque.pollFirst();

            if (curr == dst) {
                return dist[dst];
            }

            for (Edge edge : adj.get(curr)) {
                int next = edge.to;
                int weight = edge.weight;

                if (dist[curr] + weight < dist[next]) {
                    dist[next] = dist[curr] + weight;
                    if (weight == 0) {
                        deque.offerFirst(next);
                    } else {
                        deque.offerLast(next);
                    }
                }
            }
        }

        return dist[dst] == Integer.MAX_VALUE ? -1 : dist[dst];
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        // Example 1
        int[][] edges1 = {{1, 2}, {3, 2}};
        System.out.println(sol.minimumEdgeReversal(edges1, 3, 1, 3)); // Output: 1

        // Example 2
        int[][] edges2 = {{1, 2}, {2, 3}, {3, 4}};
        System.out.println(sol.minimumEdgeReversal(edges2, 4, 1, 4)); // Output: 0
    }
}
