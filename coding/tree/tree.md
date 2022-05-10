## 二叉树遍历
### 深度遍历
#### 前序遍历
**101：[判断树是否对称](https://leetcode.com/problems/symmetric-tree/)**
![问题描述](https://img-blog.csdnimg.cn/2020120712332730.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)

**108：[有序序列转化为平衡二叉搜索树](https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/)**![问题描述](https://img-blog.csdnimg.cn/20201207123651836.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)

**105：[根据中序遍历和先序遍历还原树](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)**
![问题描述](https://img-blog.csdnimg.cn/20201207104405241.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**
1. 首先从先序序列中找到根节点3
2. 在中序序列中找到根序列3，并将树分为左子树[9]与右子树[15,20,7]
3. 先序序列根据中序左右子树长度，得到左子树[9]与右子树[20,15,7]
4. 重复1,2,3步骤，知道先序和中序序列中只有一个节点，构造叶子节点

**114：[将树拉直](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/)**
![问题描述](https://img-blog.csdnimg.cn/20201208091145478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)

**129:[树的路径和](https://leetcode.com/problems/sum-root-to-leaf-numbers/)**![问题描述](https://img-blog.csdnimg.cn/20201209104315304.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)


#### 中序遍历
94：[中序遍历二叉树](https://leetcode.com/problems/binary-tree-inorder-traversal/)
![问题描述](https://img-blog.csdnimg.cn/20201207084045890.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
99：[恢复搜索二叉树序列](https://leetcode.com/problems/recover-binary-search-tree/)
![问题描述](https://img-blog.csdnimg.cn/20201207100057222.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**
中序遍历排列1,2...i...j...n，其中i，j顺序需要交换，找出i，j节点即可。
1. 若i，j相邻
2. 若i，j不相邻
#### 后序遍历

**110：[判断是否为平衡二叉树](https://leetcode.com/problems/balanced-binary-tree/)**

<img src="https://img-blog.csdnimg.cn/20201207125110563.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70" alt="问题描述" style="zoom:50%;" />
**解题思路：**

 1. 后序遍历判断每个结点的左右子树深度差是否大于1
    &emsp;大于1，返回-1
    &emsp;不大于1，返回该节点深度
  2. 若根节点返回值为-1则不是平衡二叉树；反之则是。

 **113：[列出树路径和](https://leetcode.com/problems/path-sum-ii/)**
<img src="https://img-blog.csdnimg.cn/20201208085430690.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70" alt="问题描述" style="zoom:50%;" />



**124：[最大路径](https://leetcode.com/problems/binary-tree-maximum-path-sum/)**

<img src="https://img-blog.csdnimg.cn/20201208084318943.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70" alt="问题描述" style="zoom:50%;" />
**解题思路：**

1. 后序遍历节点
2. 计算该节点**两路径**（左右子树）的最大值，并试图更新最大值
3. 计算该节点**单路径**（左/右子树）的最大值，并结合节点值返回

### 广度遍历
#### 层次遍历
102：[层次遍历](https://leetcode.com/problems/binary-tree-level-order-traversal/)
![问题描述](https://img-blog.csdnimg.cn/20201207101251141.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
103：[Z字形遍历树](https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/)
![问题描述](https://img-blog.csdnimg.cn/20201207101725645.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**
类似层次遍历，但需要设置boolean，控制偶数序号的数组反转

**116：[为完全二叉树补全next指针](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/)**
![问题描述](https://img-blog.csdnimg.cn/20201208093144422.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**

 1. 层次遍历适用于普通二叉树，完全二叉树更特殊，可寻求更简解法
 2. 使用相邻的两个节点递归
 &emsp;left.left->left.right
 &emsp;left.right->right.left
 &emsp;right.left->right.right


> 若不是完全二叉树，可充分使用多余的next指针，代替层次遍历的队列，将空间复杂度降低到O(0)

**199：[树的右视图](https://leetcode.com/problems/binary-tree-right-side-view/)**
![问题描述](https://img-blog.csdnimg.cn/20201210082745736.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)


## 动态规划
**95：[获取所有的二叉查找树](https://leetcode.com/problems/unique-binary-search-trees-ii/)**
![问题描述](https://img-blog.csdnimg.cn/20201207084850597.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**
 ![树](https://img-blog.csdnimg.cn/20201207085505821.png#pic_center)

1)	该问题实际是一个动态规划问题，i树可由j为root，左子树j-1，右子树为i-j（j<=i）
2)	左子树j-1可沿用之前计算的结果，右子树i-j沿用之前的结果并将每个结点+j

96：[计算所有二叉搜索树数目](https://leetcode.com/problems/unique-binary-search-trees/)
	![问题描述](https://img-blog.csdnimg.cn/2020120709135527.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0F0aGF6ZW1lbnQ=,size_16,color_FFFFFF,t_70)
**解题思路：**
 ![树](https://img-blog.csdnimg.cn/20201207085505821.png#pic_center)

1)	该问题实际是一个动态规划问题，i树可由j为root，左子树j-1，右子树为i-j（j<=i）
2)	i树的总数=（j-1）树总数*（i-j）树总数。

> 该问题可看作给定中序遍历序列对应的二叉树数目（卡特兰数）

## 二叉搜索树

### 二叉搜索树的删除

**根据值删除**

- 如果 key > root.val，说明要删除的节点在右子树，root.right = deleteNode(root.right, key)。
- 如果 key < root.val，说明要删除的节点在左子树，root.left = deleteNode(root.left, key)。
- 如果 key == root.val，则该节点就是我们要删除的节点，则：
  - 如果该节点是叶子节点，则直接删除它：root = null。
  - 如果该节点不是叶子节点且有右节点，则用它的后继节点的值替代 root.val = successor.val，然后删除后继节点。
  - 如果该节点不是叶子节点且只有左节点，则用它的前驱节点的值替代 root.val = predecessor.val，然后删除前驱节点。
    返回 root。

```java
class Solution {
  /*
  One step right and then always left
  */
  public int successor(TreeNode root) {
    root = root.right;
    while (root.left != null) root = root.left;
    return root.val;
  }

  /*
  One step left and then always right
  */
  public int predecessor(TreeNode root) {
    root = root.left;
    while (root.right != null) root = root.right;
    return root.val;
  }

  public TreeNode deleteNode(TreeNode root, int key) {
    if (root == null) return null;

    // delete from the right subtree
    if (key > root.val) root.right = deleteNode(root.right, key);
    // delete from the left subtree
    else if (key < root.val) root.left = deleteNode(root.left, key);
    // delete the current node
    else {
      // the node is a leaf
      if (root.left == null && root.right == null) {
          root = null;
      } 
      // the node is not a leaf and has a right child
      else if (root.right != null) {
        root.val = successor(root);
        root.right = deleteNode(root.right, root.val);
      }
      // the node is not a leaf, has no right child, and has a left child    
      else {
        root.val = predecessor(root);
        root.left = deleteNode(root.left, root.val);
      }
    }
    return root;
  }
}
```

根据值删除只适用于严格二叉搜索树,即没有重复值的二叉搜索树.
