# ⚡ GeeksforGeeks — Minimum Absolute Difference in BST

### 📝 Problem Description
Given the root of a Binary Search Tree (BST) containing $n$ ($n > 1$) nodes, find the minimum absolute difference between the values of any two different nodes in the tree.

**Input:**
- A binary search tree root containing $2 \le \text{size} \le 10^5$ nodes, with node values $0 \le \text{node.data} \le 10^6$.

**Output:**
- Return an integer representing the minimum absolute difference between any two nodes.

**Examples:**
- **Example 1:**
  - **Input:** `root[] = [50, 30, 70, 20, N, 60, 80]`
  - **Output:** `10`
  - **Explanation:** There are no two nodes whose absolute difference is smaller than $10$ (e.g., $60 - 50 = 10$).
- **Example 2:**
  - **Input:** `root[] = [60, 30, 90, 10]`
  - **Output:** `20`
  - **Explanation:** The minimum absolute difference is between $30$ and $10$ ($30 - 10 = 20$).

---

### 💡 Key Insights

1. **BST Property & In-Order Traversal:**
   - By definition, an **in-order traversal** of a Binary Search Tree visits the nodes in ascending order of their values.
   - Consequently, the minimum absolute difference between *any* two nodes in a BST will always be the minimum difference between *adjacent* elements in the sorted in-order sequence.

2. **Optimized Traversal Strategy:**
   - Instead of storing all node values in an array and sorting or scanning them (which takes $\mathcal{O}(N \log N)$ or extra $\mathcal{O}(N)$ space), we can compute the difference dynamically.
   - Maintain a tracker for the `prev` visited node's value during the recursive in-order traversal.
   - Compare `root.data - prev` at every step, update a running `minDiff`, and advance `prev` to the current node.

3. **Complexity:**
   - **Time Complexity:** $\mathcal{O}(N)$, where $N$ is the number of nodes in the BST, since we visit every node exactly once.
   - **Space Complexity:** $\mathcal{O}(H)$, where $H$ is the height of the tree, consumed by the recursive call stack ($\mathcal{O}(\log N)$ for a balanced tree, $\mathcal{O}(N)$ for a skewed tree).

---

### 💻 Java Solution

```java
/* The Node structure is defined as
class Node {
    int data;
    Node left;
    Node right;
    Node(int data){
        this.data = data;
        left = null;
        right = null;
    }
}
*/

class Solution {
    int minDiff;
    Integer prev;

    public int absDiff(Node root) {
        minDiff = Integer.MAX_VALUE;
        prev = null;
        inorder(root);
        return minDiff;
    }
    
    private void inorder(Node root) {
        if (root == null) {
            return;
        }
        
        // Traverse left subtree
        inorder(root.left);
        
        // Process current node
        if (prev != null) {
            minDiff = Math.min(minDiff, root.data - prev);
        }
        prev = root.data;
        
        // Traverse right subtree
        inorder(root.right);
    }
}
