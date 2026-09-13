# ⚡ GeeksforGeeks — Geek Town Party House

### 📝 Problem Description
Geek Town has $n$ houses numbered from $1$ to $n$. You need to choose a single house to host a party such that the distance from this house to its farthest house is as small as possible. Return this minimum possible maximum distance.

The houses are connected by $n - 1$ bidirectional roads, forming an unweighted tree structure.
The connections are given as an adjacency list `adj`, where `adj[i]` contains all houses directly connected to house $i + 1$.

**Input:**
- An adjacency list `adj` representing the tree connections, where `adj.size() = n` and $1 \le n \le 10^5$.
- Node numbers inside `adj[i]` are $1$-indexed ($1 \le \text{adj}[i][j] \le n$).

**Output:**
- Return an integer representing the minimum possible distance to the farthest house.

**Examples:**
- **Example 1:**
  - **Input:** `adj = [[2], [1, 4, 3], [2], [2]]`
  - **Output:** `1`
  - **Explanation:** Choosing house $2$ gives distances to house $1$, $3$, and $4$ equal to $1$. The farthest distance from house $2$ is $1$.
- **Example 2:**
  - **Input:** `adj = [[2], [1, 3], [4, 2], [3]]`
  - **Output:** `2`
  - **Explanation:** The tree is a simple path: $1 - 2 - 3 - 4$. Hosting the party at house $2$ or house $3$ results in a maximum distance of $2$.

---

### 💡 Key Insights

1. **Tree Center and Radius:**
   - In graph theory, the maximum distance from a node $u$ to any other node is its **eccentricity**, denoted as $\epsilon(u) = \max_{v} \text{dist}(u, v)$.
   - The problem asks for the minimum eccentricity across all nodes:
     $$\min_{u \in V} \epsilon(u)$$
   - This value is precisely the **radius** of the tree, and the optimal host houses are the **center(s)** of the tree.

2. **Relationship with Tree Diameter:**
   - The diameter $D$ of a tree is the longest shortest path between any two nodes.
   - For any unweighted tree, the radius $R$ is related to its diameter $D$ by:
     $$R = \left\lceil \frac{D}{2} \right\rceil = \left\lfloor \frac{D + 1}{2} \right\rfloor$$
   - Thus, the problem reduces to calculating the diameter $D$ of the tree.

3. **Double BFS / DFS Algorithm to Find Diameter:**
   - Start an arbitrary BFS/DFS from node $1$ to find the farthest node $u$. Node $u$ is guaranteed to be one endpoint of a diameter.
   - Run a second BFS/DFS starting from node $u$ to find the farthest node $v$. The distance $\text{dist}(u, v)$ is the exact diameter $D$ of the tree.
   - Calculate the answer as `(diameter + 1) / 2`.

4. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(n)$, as each BFS traverses $n$ nodes and $n - 1$ edges.
   - **Space Complexity:** $\mathcal{O}(n)$ auxiliary space for the distance array and BFS traversal queue.

---

### 💻 Java Solution

```java
import java.util.ArrayDeque;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.Queue;

/**
 * Problem: Geek Town Party House
 * Language: Java 8 / 11 / 17 / 21
 */
class Solution {
    public int partyHouse(ArrayList<ArrayList<Integer>> adj) {
        int n = adj.size();
        if (n <= 1) {
            return 0;
        }

        // Step 1: Run BFS from an arbitrary node (node 1) to find one endpoint of the diameter
        int[] firstBFS = bfs(1, adj, n);
        int endpointU = firstBFS[0];

        // Step 2: Run BFS from endpointU to find the diameter length
        int[] secondBFS = bfs(endpointU, adj, n);
        int diameter = secondBFS[1];

        // Tree radius = ceil(diameter / 2)
        return (diameter + 1) / 2;
    }

    private int[] bfs(int startNode, ArrayList<ArrayList<Integer>> adj, int n) {
        int[] dist = new int[n + 1];
        Arrays.fill(dist, -1);

        Queue<Integer> queue = new ArrayDeque<>();
        queue.add(startNode);
        dist[startNode] = 0;

        int farthestNode = startNode;
        int maxDist = 0;

        while (!queue.isEmpty()) {
            int curr = queue.poll();

            // Adjacency list is 0-indexed, house labels are 1-indexed
            for (int neighbor : adj.get(curr - 1)) {
                if (dist[neighbor] == -1) {
                    dist[neighbor] = dist[curr] + 1;
                    queue.add(neighbor);

                    if (dist[neighbor] > maxDist) {
                        maxDist = dist[neighbor];
                        farthestNode = neighbor;
                    }
                }
            }
        }

        return new int[]{farthestNode, maxDist};
    }

    public static void main(String[] args) {
        Solution sol = new Solution();

        // Example 1: adj = [[2], [1, 4, 3], [2], [2]]
        ArrayList<ArrayList<Integer>> adj1 = new ArrayList<>();
        adj1.add(new ArrayList<>(Arrays.asList(2)));
        adj1.add(new ArrayList<>(Arrays.asList(1, 4, 3)));
        adj1.add(new ArrayList<>(Arrays.asList(2)));
        adj1.add(new ArrayList<>(Arrays.asList(2)));
        System.out.println(sol.partyHouse(adj1)); // Output: 1

        // Example 2: adj = [[2], [1, 3], [4, 2], [3]]
        ArrayList<ArrayList<Integer>> adj2 = new ArrayList<>();
        adj2.add(new ArrayList<>(Arrays.asList(2)));
        adj2.add(new ArrayList<>(Arrays.asList(1, 3)));
        adj2.add(new ArrayList<>(Arrays.asList(4, 2)));
        adj2.add(new ArrayList<>(Arrays.asList(3)));
        System.out.println(sol.partyHouse(adj2)); // Output: 2
    }
}
