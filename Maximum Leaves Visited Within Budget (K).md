# ⚡ GeeksforGeeks — Maximum Leaves Visited Within Budget (K)

### 📝 Problem Description
Given a binary tree where you start from the root at level 1, visiting a leaf node incurs a cost equal to the level of that leaf node. You can visit any number of leaf nodes, provided the total cumulative cost of visiting them does not exceed a given budget $k$.

Return the maximum number of leaf nodes that can be visited within the given budget.

**Input:**
- A binary tree representation `root` ($1 \le \text{size of binary tree} \le 10^5$).
- An integer `k` ($1 \le k \le 10^4$).

**Output:**
- Return an integer representing the maximum number of leaf nodes that can be visited without exceeding budget $k$.

**Examples:**
- **Example 1:**
  - **Input:** `root[] = [10, 8, 2, 3, N, 3, 6, N, N, N, 4]`, `k = 8`
  - **Output:** `2`
  - **Explanation:**
    - Leaf node 3: Level 3 (Cost = 3)
    - Leaf node 6: Level 3 (Cost = 3)
    - Leaf node 4: Level 4 (Cost = 4)
    - Choosing the two cheapest leaves yields a cost of $3 + 3 = 6 \le 8$. Choosing a third leaf would cost at least $6 + 4 = 10 > 8$. Thus, the maximum number of leaf nodes is 2.

- **Example 2:**
  - **Input:** `root[] = [1, 2, 3, 4, 5, 6, 7]`, `k = 5`
  - **Output:** `1`
  - **Explanation:** The leaf nodes are 4, 5, 6, and 7, all situated at level 3. Each leaf costs 3. With a budget of 5, only 1 leaf node can be visited because $3 \le 5$, but $3 + 3 = 6 > 5$.

- **Example 3:**
  - **Input:** `root[] = [1]`, `k = 1`
  - **Output:** `1`
  - **Explanation:** The root node itself is a leaf node at level 1. Its visiting cost is 1. With $k = 1$, we can visit this single leaf node.

---

### 💡 Key Insights

1. **Leaf Identification & Cost Invariance:**
   - A node is a leaf if and only if both its left and right child pointers are `null` (`left == null && right == null`).
   - The cost of visiting a leaf is strictly defined by its level in the tree (where $\text{root level} = 1$).

2. **Greedy Strategy via Sorting:**
   - To maximize the total count of items picked under an upper bound constraint $\sum \text{cost} \le k$, the optimal choice is always to prioritize the items with the smallest individual costs.
   - Hence, collect the levels of all leaf nodes into a list and sort them in non-decreasing order.

3. **Counting Within Budget:**
   - Iterate through the sorted leaf levels, maintaining a running total of the cost.
   - Stop as soon as adding the next leaf's level would exceed $k$. The number of elements accumulated is the optimal answer.

4. **Complexity:**
   - **Time Complexity:** 
     - **Tree Traversal:** $\mathcal{O}(N)$ using DFS or BFS to visit every node once.
     - **Sorting:** $\mathcal{O}(L \log L)$, where $L$ is the number of leaf nodes ($L \le N \le 10^5$).
     - **Greedy Accumulation:** $\mathcal{O}(L)$.
     - **Total Time Complexity:** $\mathcal{O}(N + L \log L)$, easily running within ~0.2s for $N = 10^5$.
   - **Space Complexity:** $\mathcal{O}(N)$ for the recursion stack (in worst-case skewed trees) and storing the list of leaf levels.

---

### 💻 Java Solution

```java
/**
 * Problem: Maximum Leaves Visited Within Budget
 * Language: Java 8 / 11 / 17 / 21
 */

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

/* Binary Tree Node Structure
class Node {
    int data;
    Node left, right;

    public Node(int data) {
        this.data = data;
    }
}
*/

class Solution {
    public int getCount(Node root, int k) {
        if (root == null || k <= 0) {
            return 0;
        }

        List<Integer> leafLevels = new ArrayList<>();

        // Step 1: Traverse the tree and record the level of each leaf node
        collectLeafLevels(root, 1, leafLevels);

        // Step 2: Sort leaf costs in ascending order to pick the cheapest first
        Collections.sort(leafLevels);

        // Step 3: Greedily pick leaves within the budget k
        int count = 0;
        int currentCost = 0;

        for (int levelCost : leafLevels) {
            if (currentCost + levelCost <= k) {
                currentCost += levelCost;
                count++;
            } else {
                break;
            }
        }

        return count;
    }

    private void collectLeafLevels(Node node, int level, List<Integer> leafLevels) {
        if (node == null) {
            return;
        }

        // Identify leaf node
        if (node.left == null && node.right == null) {
            leafLevels.add(level);
            return;
        }

        // Traverse child subtrees
        collectLeafLevels(node.left, level + 1, leafLevels);
        collectLeafLevels(node.right, level + 1, leafLevels);
    }
}
