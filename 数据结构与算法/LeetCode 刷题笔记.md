# LeetCode 刷题笔记

<tt>tag</tt>：

[二叉树](#二叉树)、[二叉搜索树](#二叉搜索树)、[Morris遍历](#Morris遍历)、][双指针](#双指针)、[前缀和](#前缀和)、[差分数组](#差分数组)、[队列/栈](#队列/栈)、[堆](#堆)、

[数据结构设计](#数据结构设计)、[图论](#图论)、[BFS/DFS](#暴力搜索算法)、[动态规划](#动态规划)、[其他经典算法](#其他经典算法)

## 二叉树

```c++
//c++版本的二叉树
struct TreeNode{
    int val;
    TreeNode *left, *right;
    TreeNode() : val(0), left(nullptr), right(nullptr){}
    TreeNode(int x) : val(x), left(nullptr), right(nullptr){}
    TreeNode(int x, TreeNode *left, TreeNode *right) : val(0), left(left), right(right){}
};
//java版本的二叉树
class TreeNode{
    int val;
    TreeNode *left, *right;
    TreeNode(){}
    TreeNode(int val){this.val = val;}
    TreeNode(int val, TreeNode *left, TreeNode *right){
        this.val = val;
        this.left = left;
        this.right = right;
    }
}
```

### Morris遍历

```java

```



### EASY

#### [94. Binary Tree Inorder Traversal](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

```c++
class Solution {
public:
    //递归  时间复杂度O(n)  空间复杂度O(h),h为树的高度
    vector<int> res;
    void inOrder(TreeNode* root){
        if(root == nullptr) return;
        inOrder(root->left);
        res.push_back(root->val);
        inOrder(root->right);
    }
    vector<int> inorderTraversal(TreeNode* root) {
        inOrder(root);
        return res;
    }
    //迭代  时间复杂度O(n)  空间复杂度O(n)
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> stk;
        while(root != nullptr || !stk.empty()){
            while(root != nullptr){
                stk.push(root);
                root = root->left;    //左
            }
            root = stk.top();
            stk.pop();
            res.push_back(root->val); //中
            root = root->right;       //右
        }
        return res;
    }
};
```

#### [100. Same Tree](https://leetcode-cn.com/problems/same-tree/)

```c++
class Solution {
public:
    //时间复杂度 O(min(m,n))  空间复杂度 O(min(m,n))
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(p == nullptr && q == nullptr) return true;
        if(p == nullptr || q == nullptr) return false;
        if(p->val != q->val) return false; //中
        return isSameTree(p->left, q->left) 
            && isSameTree(p->right, q->right); //左 右
    }
};
```

#### [101. Symmetric Tree](https://leetcode-cn.com/problems/symmetric-tree/)

```c++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(root == nullptr) return true;
        return check(root->left, root->right);
    }
    bool check(TreeNode* l, TreeNode* r){
        if(l == nullptr || r == nullptr) return l == r; //左右子树有一个为空或全为空
        //左右子树均不为空，先判断结点值是否相同，在向下遍历(注意对称性)
        return l->val == r->val
            && check(l->left, r->right) 
            && check(l->right, r->left);
    }
};
```

#### [104. Maximum Depth of Binary Tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```c++
class Solution {
public:
    //时间复杂度O(n) 空间复杂度O(h)
    //动态规划思路
    int maxDepth(TreeNode* root) {
        if(root == nullptr) return 0;
        int lMaxDepth = maxDepth(root->left); //左子树最大深度
        int rMaxDepth = maxDepth(root->right);//右子树最大深度
        return 1 + max(lMaxDepth, rMaxDepth);
    }
    //回溯算法思路
    int depth = 0;
    int res = 0;
    int maxDepth(TreeNode* root) {
        traverseTree(root);
        return res;
    }
    void traverseTree(TreeNode* root){
        if(root == nullptr) return;
        depth++; //先序遍历位置，下一访问结点为左子结点，depth++
        res = max(res, depth); 
        traverseTree(root->left);
        traverseTree(root->right);
        depth--; //后序遍历位置，后序遍历完会回溯到父结点，depth--
    }
};
```

#### [110. Balanced Binary Tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

```c++
class Solution {
public:
    //自顶向下时间复杂度O(n^2) 空间复杂度O(n)，由于树的高度会被重复计算，因此效率较低
    bool isBalanced(TreeNode* root) {
        if(root == nullptr) return true;
        return abs(maxDepth(root->left)-maxDepth(root->right)) <= 1
            && isBalanced(root->left)
            && isBalanced(root->right); //注意，左右子树也必须是平衡二叉树
    }
    int maxDepth(TreeNode* root){
        if(root == nullptr) return 0;
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
    //自底向上：优化，考虑在计算每个结点的高度的时候，顺便判断是否符合平衡条件，时间复杂度降为O(n)
    bool isBalanced(TreeNode* root) {
        if(root == nullptr) return true;
        return maxDepth(root) > 0;
    }
    int maxDepth(TreeNode* root){
        if(root == nullptr) return 0;
        int ldepth = maxDepth(root->left);
        int rdepth = maxDepth(root->right);
        //后序遍历位置，比较左右两个子树是满足平衡条件
        if(ldepth == -1 || rdepth == -1 || abs(ldepth-rdepth) > 1) 
            return -1;
        return 1 + max(ldepth, rdepth);
    }
};
```

#### [111. Minimum Depth of Binary Tree](https://leetcode-cn.com/problems/minimum-depth-of-binary-tree/)

```c++
class Solution {
public:
    //DFS  时间复杂度O(n)  空间复杂度O(h)
    int minDepth(TreeNode* root) {
        if(root == nullptr) return 0;
        if(root->left == nullptr && root->right == nullptr) return 1;
        int depth = INT_MAX;
        if(root->left != nullptr) 
            depth = min(minDepth(root->left), depth);
        if(root->right != nullptr)
            depth = min(minDepth(root->right), depth);
        return depth + 1;
    }
    //BFS  时间复杂度O(n)  空间复杂度O(n)
    int minDepth(TreeNode* root) {
        if(root == nullptr) return 0;
        int depth = 1; //root不为空时，本身高度为1
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            for(int i = 0; i < size; i++){
                TreeNode* root = q.front(); q.pop();
                //BFS遍历方式最先访问到叶子结点，此时为树的最小深度
                if(root->left == nullptr && root->right == nullptr)
                    return depth;
                if(root->left != nullptr) q.push(root->left);
                if(root->right != nullptr) q.push(root->right);
            }
            depth++; //一层遍历完，深度加一
        }
        return depth;
    }
};
```

#### [112. Path Sum](https://leetcode-cn.com/problems/path-sum/)

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int targetSum) {
        if(root == nullptr) return false;
        if(root->left == nullptr && root->right == nullptr) //遍历到了叶子结点
            return root->val == targetSum;
        return hasPathSum(root->left, targetSum - root->val)
            || hasPathSum(root->right, targetSum - root->val);
    }
};
```

#### [144. Binary Tree Preorder Traversal](https://leetcode-cn.com/problems/binary-tree-preorder-traversal/)

```c++
class Solution {
public:
    //递归(回溯) 时间复杂度O(n) 空间复杂度O(n)
    vector<int> res;
    vector<int> preorderTraversal(TreeNode* root) {
        preOrder(root);
        return res;
    }
    void preOrder(TreeNode* root){
        if(root == nullptr) return;
        res.push_back(root->val);
        preOrder(root->left);
        preOrder(root->right);
    }
    //迭代 时间复杂度O(n) 空间复杂度O(n)
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        if(root == nullptr) return res;
        stack<TreeNode*> s;
        TreeNode* temp = root;
        while(!s.empty() || temp != nullptr){
            while(temp != nullptr){
                res.push_back(temp->val); //先将根结点的值加入res
                s.push(temp);        //根结点入栈
                temp = temp->left;   //递归遍历左子树
            }
            temp = s.top(); s.pop(); //左子树遍历完，回溯到上一个根结点
            temp = temp->right;      //递归遍历右子树
        }
        return res;
    }
    //Morris算法 时间复杂度O() 空间复杂度O()
};
```

#### [145. Binary Tree Postorder Traversal](https://leetcode-cn.com/problems/binary-tree-postorder-traversal/)

```c++
class Solution {
public:
    //迭代 时间复杂度O(n)  空间复杂度O(n)
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> res;
        if(root == nullptr) return res;
        stack<TreeNode*> s;
        TreeNode* prev = nullptr;
        while(!s.empty() || root != nullptr){
            while(root != nullptr){ //先找到最左下结点
                s.push(root);
                root = root->left;
            }
            root = s.top(); s.pop(); //最左下结点出栈
            //如果当前根结点没有右子树或右子树已经访问过，则访问该根结点
            if(root->right == nullptr || root->right == prev){
                res.push_back(root->val);
                prev = root; //标记该根结点为上次访问
                root = nullptr; //置空访问过的结点
            }else{ //否则，应先访问该根结点的右子树
                s.push(root); //注意，根结点要重新入栈
                root = root->right;
            }
        }
        return res;
    }
};
```

#### [226. Invert Binary Tree](https://leetcode-cn.com/problems/invert-binary-tree/)

同[剑指 Offer 27. 二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

```c++
class Solution {
public:
    //先序遍历 时间复杂度O(n) 空间复杂度O(n)
    TreeNode* invertTree(TreeNode* root) {
        if(root == nullptr) return nullptr;
        //交换左右子树
        TreeNode* temp = root->right;
        root->right = root->left;
        root->left = temp;
        //向下遍历左右子树
        invertTree(root->left);
        invertTree(root->right);
        return root;
    }
    //后序遍历
    TreeNode* invertTree(TreeNode* root) {
        if(root == nullptr) return nullptr;
        TreeNode* left  = invertTree(root->left);
        TreeNode* right = invertTree(root->right);
        root->left = right;
        root->right = left;
        return root; 
    }
};
```

#### [257. Binary Tree Paths](https://leetcode-cn.com/problems/binary-tree-paths/)

```c++
class Solution {
public:
    //递归(dfs)
    vector<string> res;
    vector<string> binaryTreePaths(TreeNode* root) {
        if(root == nullptr) return res;
        traverse(root, "");
        return res;
    }
    void traverse(TreeNode* root, string path){
        if(root == nullptr) return;
        if(root->left == nullptr && root->right == nullptr){ //到达叶子结点
            path += to_string(root->val);
            res.push_back(path);
            return;
        }
        path = path + to_string(root->val) + "->";
        traverse(root->left, path);
        traverse(root->right, path);
    }
};
```

#### [404. Sum of Left Leaves](https://leetcode-cn.com/problems/sum-of-left-leaves/)

```c++
class Solution {
public:
    int sumLLeaves = 0;
    int sumOfLeftLeaves(TreeNode* root) {
        if(root == nullptr) return 0;
        if(root->left == nullptr && root->right == nullptr) return 0;
        traverse(root);
        return sumLLeaves;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        //左叶子节点
        if(root->left != nullptr && root->left->left == nullptr && root->left->right == nullptr)
            sumLLeaves += root->left->val;
        traverse(root->left);
        traverse(root->right);
    }
};
```

#### [543. Diameter of Binary Tree](https://leetcode-cn.com/problems/diameter-of-binary-tree/)

```c++
class Solution {
public:
    //二叉树直径，即左右子树的最大深度之和(或经过的结点数-1)
    int maxDiameter = 0;
    int diameterOfBinaryTree(TreeNode* root) {
        if(root == nullptr) return 0;
        maxDepth(root);
        return maxDiameter;
    }
    int maxDepth(TreeNode* root){
        if(root == nullptr) return 0;
        int lheight = maxDepth(root->left);
        int rheight = maxDepth(root->right);
        //后序遍历位置，找出最大直径
        maxDiameter = max(maxDiameter, (lheight+rheight)); 
        return 1 + max(lheight, rheight);
    }
};
```

#### [563. Binary Tree Tilt](https://leetcode-cn.com/problems/binary-tree-tilt/)

```c++
class Solution {
public:
    //return the sum of every tree node's tilt（倾斜度）
    int sumOfTilt = 0;
    int findTilt(TreeNode* root) {
        if(root == nullptr) return 0;
        traverse(root);
        return sumOfTilt;
    }
    //后序遍历二叉树，返回所有结点之和
    int traverse(TreeNode* root){
        if(root == nullptr) return 0;
        int lval = traverse(root->left);
        int rval = traverse(root->right);
        sumOfTilt += abs(lval - rval);
        return root->val + lval + rval;
    }
};
```

#### [572. Subtree of Another Tree](https://leetcode-cn.com/problems/subtree-of-another-tree/)

```c++
class Solution {
public:
    //暴力搜索，遍历root中每一个子树，并与subRoot相比较
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if(root == nullptr) return subRoot == nullptr;
        return isSametree(root, subRoot)
            || isSubtree(root->left, subRoot)
            || isSubtree(root->right, subRoot);
    }
    bool isSametree(TreeNode* p, TreeNode* q) {
        if(p == nullptr && q == nullptr) return true;
        if(p == nullptr || q == nullptr) return false;
        if(p->val != q->val) return false;
        return isSametree(p->left, q->left)
            && isSametree(p->right, q->right);
    }
};
```

#### [606. Construct String from Binary Tree](https://leetcode-cn.com/problems/construct-string-from-binary-tree/)

```c++
class Solution {
public:
    //先序遍历
    string tree2str(TreeNode* root) {
        if(root == nullptr) return "";
        if(root->left == nullptr && root->right == nullptr) //叶子结点，后面不用加()
            return to_string(root->val);
        if(root->right == nullptr) //右孩子为空，只用给左孩子两侧加()
            return to_string(root->val) + "(" + tree2str(root->left) + ")";
        //最后，左孩子为空(右孩子不为空) 或 左右孩子均不为空，需要先在左孩子位置上加()
        return to_string(root->val) + "(" + tree2str(root->left) + ")(" + tree2str(root->right) + ")";
    }
    //后序遍历 内存消耗比先序遍历多一倍
    string tree2str(TreeNode* root) {
        if(root == nullptr) return "";

        string lstr = tree2str(root->left);
        string rstr = tree2str(root->right);
        if(lstr == "" && rstr == "") 
            return to_string(root->val);
        if(rstr == "")
            return to_string(root->val) + "(" + lstr + ")";
        if(lstr == "")
            return to_string(root->val) + "()(" + rstr + ")";
        return to_string(root->val) + "(" + lstr + ")(" + rstr + ")";
    }
};
```

#### [617. Merge Two Binary Trees](https://leetcode-cn.com/problems/merge-two-binary-trees/)

```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if(root1 == nullptr) return root2;
        if(root2 == nullptr) return root1;
        TreeNode* res = new TreeNode(root1->val + root2->val);
        res->left = mergeTrees(root1->left, root2->left);
        res->right = mergeTrees(root1->right, root2->right);
        return res;
    }
};
```

#### [637. Average of Levels in Binary Tree](https://leetcode-cn.com/problems/average-of-levels-in-binary-tree/)

```c++
class Solution {
public:
    //层序遍历模板
    vector<double> averageOfLevels(TreeNode* root) {
        vector<double> res;
        if(root == nullptr) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            double sum = 0;
            for(int i = 0; i < size; i++){
                TreeNode* cur = q.front(); q.pop();
                sum += cur->val;
                if(cur->left != nullptr) q.push(cur->left);
                if(cur->right != nullptr) q.push(cur->right);
            }
            res.push_back(sum / size);
        }
        return res;
    }
};
```

#### [671. Second Minimum Node In a Binary Tree](https://leetcode-cn.com/problems/second-minimum-node-in-a-binary-tree/)

```c++
class Solution {
public:
    //根据题意，该二叉树的中序或先序遍历都是递增的
    set<int> inorder; //set没有重复元素且有序
    int findSecondMinimumValue(TreeNode* root) {
        if(root->left == nullptr && root->left == nullptr) return -1;
        traverse(root);
        inorder.erase(inorder.begin());
        return inorder.size() < 1 ? -1 : *inorder.begin();
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        inorder.insert(root->val);
        traverse(root->right);
    }
};
```

#### [872. Leaf-Similar Trees](https://leetcode-cn.com/problems/leaf-similar-trees/)

```C++
//叶子结点生成器
class LeafIterator {
private:
    //模拟递归过程
    stack<TreeNode*> stk;
public:
    LeafIterator(TreeNode* root) {
        stk.push(root);
    }

    bool hasNext() {
        return !stk.empty();
    }
    //返回下一个叶子结点
    TreeNode* next(){
        while(!stk.empty()){
            TreeNode* cur = stk.top(); stk.pop();
            if(cur->left == nullptr && cur->right == nullptr)
                return cur;
            if(cur->right != nullptr) stk.push(cur->right); //右孩子先入栈
            if(cur->left  != nullptr) stk.push(cur->left);
        }
        return nullptr;
    }
};

class Solution {
public:
    //简单思路，两次dfs找到两棵树所有叶结点存到vector中，
    //然后直接比较v1 == v2(vector可直接比较，不用逐一遍历，但必须为内置类型)
    bool leafSimilar(TreeNode* root1, TreeNode* root2) {
        LeafIterator it1 = LeafIterator(root1);
        LeafIterator it2 = LeafIterator(root2);
        while(it1.hasNext() && it2.hasNext()){
            if(it1.next()->val != it2.next()->val)
                return false;
        }
        return !it1.hasNext()
            && !it2.hasNext(); //最后都应完成遍历
        //return true; ??
    }
};
```

#### [965. Univalued Binary Tree](https://leetcode-cn.com/problems/univalued-binary-tree/)

```c++
class Solution {
public:
    //单值树
    int unival;
    bool isUnivalTree(TreeNode* root) {
        if(root == nullptr) return true;
        unival = root->val;
        return traverse(root);
    }
    bool traverse(TreeNode* root){
        if(root == nullptr) return true;
        return root->val == unival
            && traverse(root->left)
            && traverse(root->right);
    }
};
```

#### [993. Cousins in Binary Tree](https://leetcode-cn.com/problems/cousins-in-binary-tree/)

```c++
class Solution {
public:
    int dx, dy;
    TreeNode *fx, *fy; //dx为x所在深度，fx为x父结点值(值唯一)
    //堂兄弟结点(同深度，但父结点不同)，根结点深度为0
    bool isCousins(TreeNode* root, int x, int y) {
        traverse(root, x, y, nullptr, 0);
        if(dx == dy && fx != fy) return true;
        return false;
    }
    void traverse(TreeNode* root, int x, int y, TreeNode* parent, int depth){
        if(root == nullptr) return;
        if(root->val == x){
            dx = depth;
            fx = parent;
        }
        if(root->val == y){
            dy = depth;
            fy = parent;
        }
        traverse(root->left, x, y, root, depth+1);
        traverse(root->right, x, y, root, depth+1);
    }
};
```

#### [1022. 从根到叶的二进制数之和](https://leetcode-cn.com/problems/sum-of-root-to-leaf-binary-numbers/)

```c++
class Solution {
public:
    int sum = 0; //记录所有根到叶子路径表示数字的总和
    int sumRootToLeaf(TreeNode* root) {
        if(root == nullptr) return 0;
        traverse(root, 0);
        return sum;
    }
    void traverse(TreeNode* root, int num){
        if(root == nullptr) return;
        if(root->left == nullptr && root->right == nullptr){ //到叶子结点
            num = num << 1 | root->val;//num*2 + root->val;
            sum += num;
        }
        traverse(root->left, num << 1 | root->val);
        traverse(root->right, num << 1 | root->val);
    }
};
```

#### [LCP 44. 开幕式焰火](https://leetcode-cn.com/problems/sZ59z6/)

```c++
class Solution {
public:
    set<int> color;
    int numColor(TreeNode* root) {
        if(root == nullptr) return 0;
        traverse(root);
        return color.size();
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        color.insert(root->val);
        traverse(root->left);
        traverse(root->right);
    }
};
```

#### [剑指 Offer 27. 二叉树的镜像](https://leetcode-cn.com/problems/er-cha-shu-de-jing-xiang-lcof/)

同[226. Invert Binary Tree](https://leetcode-cn.com/problems/invert-binary-tree/)

```c++
class Solution {
public:
    //后序遍历，交换根结点的左右子树
    TreeNode* mirrorTree(TreeNode* root) {
        if(root == nullptr) return nullptr;
        TreeNode* left  = mirrorTree(root->left);
        TreeNode* right = mirrorTree(root->right);
        root->left  = right;
        root->right = left;
        return root;
    }
};
```

#### [剑指 Offer 28. 对称的二叉树](https://leetcode-cn.com/problems/dui-cheng-de-er-cha-shu-lcof/)

同[101. Symmetric Tree](https://leetcode-cn.com/problems/symmetric-tree/)

```c++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        if(root == nullptr) return true;
        return check(root->left, root->right);
    }
    bool check(TreeNode* p, TreeNode* q){
        if(p == nullptr && q == nullptr) return true;
        if(p == nullptr || q == nullptr) return false;
        return p->val == q->val
            && check(p->left, q->right)
            && check(p->right, q->left);
    }
};
```

#### [剑指 Offer 32 - II. 从上到下打印二叉树 II](https://leetcode-cn.com/problems/cong-shang-dao-xia-da-yin-er-cha-shu-ii-lcof/)

同[102. Binary Tree Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

#### [剑指 Offer 55 - I. 二叉树的深度](https://leetcode-cn.com/problems/er-cha-shu-de-shen-du-lcof/)

同[104. Maximum Depth of Binary Tree](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(root == nullptr) return 0;
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

#### [剑指 Offer 55 - II. 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

同[110. Balanced Binary Tree](https://leetcode-cn.com/problems/balanced-binary-tree/)

```c++
class Solution {
public:
    bool flag = true;
    bool isBalanced(TreeNode* root) {
        if(root == nullptr) return true;
        traverse(root);
        return flag;
    }
    int traverse(TreeNode* root){
        if(root == nullptr) return 0;
        int ldepth = traverse(root->left);
        int rdepth = traverse(root->right);
        if(abs(ldepth-rdepth) > 1){
            flag = false;
            return -1;
        }
        return 1 + max(ldepth, rdepth);
    }
};
```

#### [面试题 04.02. 最小高度树](https://leetcode-cn.com/problems/minimum-height-tree-lcci/)

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if(nums.empty()) return nullptr;
        return buildBST(nums, 0, nums.size()-1);
    }
    TreeNode* buildBST(vector<int>& nums, int low, int high){
        if(low > high) return nullptr;
        int mid = (high-low)/2 + low;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left  = buildBST(nums, low, mid-1);
        root->right = buildBST(nums, mid+1, high);
        return root;
    }
};
```

#### [面试题 04.04. 检查平衡性](https://leetcode-cn.com/problems/check-balance-lcci/)

同[110. Balanced Binary Tree](https://leetcode-cn.com/problems/balanced-binary-tree/)    [剑指 Offer 55 - II. 平衡二叉树](https://leetcode-cn.com/problems/ping-heng-er-cha-shu-lcof/)

### MEDIUM

#### [102. Binary Tree Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-level-order-traversal/)

```c++
class Solution {
public:
    //时间复杂度 O(n)    空间复杂度 O(n)
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(root != nullptr){
            queue<TreeNode*> q;
            q.push(root);
            while(!q.empty()){
                int size = q.size();//提前记录当前队列元素个数
                vector<int> temp; //存储每一层结点数据
                for(int i = 0; i < size; i++){ //遍历第k层结点
                    TreeNode* cur = q.front(); q.pop();
                    temp.push_back(cur->val); //中
                    if(cur->left != nullptr)  
                        q.push(cur->left); //左
                    if(cur->right != nullptr) 
                        q.push(cur->right);//右
                }
                res.push_back(temp); //将本层结点值加入结果
            }
        }
        return res;
    }
};
```

#### [103. Binary Tree Zigzag Level Order Traversal](https://leetcode-cn.com/problems/binary-tree-zigzag-level-order-traversal/)

```c++
//二叉树锯齿型层序遍历
class Solution {
public:
    //时间复杂度O(n)  空间复杂度O(n)
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> res;
        if(root != nullptr){
            bool flag = true; //为true，每次向temp的尾部添加元素；否则，相反
            queue<TreeNode*> q; //双端队列
            q.push(root);
            while(!q.empty()){
                int size = q.size();
                deque<int> temp;  //双端数组
                for(int i = 0; i < size; i++){
                    TreeNode* cur = q.front(); q.pop();
                    if(flag) 
                        temp.push_back(cur->val);
                    else 
                        temp.push_front(cur->val);

                    if(cur->left != nullptr)
                        q.push(cur->left);
                    if(cur->right != nullptr)
                        q.push(cur->right);
                }
                res.push_back(vector<int>(temp.begin(), temp.end()));
                flag = !flag;  //每层遍历完后，更改flag
            }
        }
        return res;
    }
};
```

#### [105. Construct Binary Tree from Preorder and Inorder Traversal](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

<img src="LeetCode 刷题笔记.assets/image-20220120181456289.png" alt="image-20220120181456289" style="zoom:50%;" />

```c++
class Solution {
public:
    //时间复杂度O(n) 空间复杂度O(n) (n+h, n>h)
    TreeNode* build(vector<int>& preorder, int preSt, int preEn, vector<int>& inorder, int inSt, int inEn){
        if(preSt > preEn) return nullptr;
    
        int rootVal = preorder[preSt];
        int index = 0; //rootVal在inorder[]中的下标
        for(int i = inSt; i <= inEn; i++){
            if(rootVal == inorder[i]) {
                index = i;
                break;
            }
        }
        int leftSize = index - inSt; //inorder中rootVal左侧元素个数(左子树) 
        TreeNode* root = new TreeNode(rootVal); //先序遍历的第一个结点为根节点
        root->left = build(preorder, preSt+1, preSt+leftSize, inorder, inSt, index-1);
        root->right = build(preorder, preSt+leftSize+1, preEn, inorder, index+1, inEn);
        return root;
    }

    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return build(preorder, 0, preorder.size()-1, inorder, 0, inorder.size()-1);
    }
};
```

#### [106. Construct Binary Tree from Inorder and Postorder Traversal](https://leetcode-cn.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal/)

<img src="LeetCode 刷题笔记.assets/image-20220120234308521.png" alt="image-20220120234308521" style="zoom:50%;" />

```c++
class Solution {
public:
    TreeNode* build(vector<int>& inorder, int inSt, int inEn, vector<int>& postorder, int poSt, int poEn){
        if(poSt > poEn) return nullptr;
        int rootVal = postorder[poEn]; //根结点值为后序遍历
        int index = 0;
        for(int i = inSt; i <= inEn; i++){
            if(rootVal == inorder[i]){
                index = i;
                break;
            }
        }
        int leftSize = index - inSt; //左子树结点个数
        TreeNode* root = new TreeNode(rootVal);
        root->left = build(inorder, inSt, index-1, postorder, poSt, poSt+leftSize-1);
        root->right = build(inorder, index+1, inEn, postorder, poSt+leftSize, poEn-1);
        return root;
    }

    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        int size = inorder.size();
        return build(inorder, 0, size-1, postorder, 0, size-1);
    }
};
```

#### [107. Binary Tree Level Order Traversal II](https://leetcode-cn.com/problems/binary-tree-level-order-traversal-ii/)

```c++
class Solution {
public:
    //时间复杂度O(n)  空间复杂度O(n)
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        vector<vector<int>> res;
        deque<vector<int>> tmp;
        if(root == nullptr) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            vector<int> temp;
            int size = q.size();
            for(int i = 0; i < size; i++){
                TreeNode* root = q.front(); q.pop();
                temp.push_back(root->val);
                if(root->left != nullptr) q.push(root->left);
                if(root->right != nullptr) q.push(root->right);
            }
            tmp.push_front(temp);
        }
        res = vector<vector<int>>(tmp.begin(), tmp.end());
        //reverse(res.begin(), res.end()); //不必使用tmp
        return res;
    }
};
```

#### [113. Path Sum II](https://leetcode-cn.com/problems/path-sum-ii/)

```c++
class Solution {
public:
    vector<vector<int>> res;
    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {
        if(root == nullptr) return res;
        vector<int> path;
        traverse(root, path, 0, targetSum);
        return res;
    }
    void traverse(TreeNode* root, vector<int> path, int sum, int targetSum){
        if(root == nullptr) return;
        path.push_back(root->val);
        sum += root->val;
        if(root->left == nullptr && root->right == nullptr && sum == targetSum){
                res.push_back(path);
        }
        traverse(root->left, path, sum, targetSum);
        traverse(root->right, path, sum, targetSum);
        //path.pop_back();
    }
};
```

#### [114. Flatten Binary Tree to Linked List](https://leetcode-cn.com/problems/flatten-binary-tree-to-linked-list/)

```c++
class Solution {
public:
    //时间复杂度O(n)  空间复杂度O(n)
    void flatten(TreeNode* root) {
        if(root == nullptr) return;
        //先递归拉平左右子树
        flatten(root->left);
        flatten(root->right);

        TreeNode *left = root->left;
        TreeNode *right = root->right;
        //将根的左子树置空，右子树变为左子树
        root->left = nullptr;
        root->right = left;
        //将原先右子树接到当前右子树末端
        TreeNode* temp = root; //确保root不变
        while(temp->right != nullptr) temp = temp->right;
        temp->right = right;
    }
};
```

#### [116. Populating Next Right Pointers in Each Node](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node/)

```c++
class Solution {
public:
    //BFS 时间复杂度O(n)  空间复杂度O(n)
    Node* connect(Node* root) {
        if(root == nullptr) return root;
        queue<Node*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            for(int i = 0; i < size; i++){
                Node* cur = q.front(); q.pop();
                if(i < size-1){
                    Node* next = q.front();
                    cur->next = next;
                }else{
                    cur->next = nullptr;
                }
                if(cur->left != nullptr) q.push(cur->left);
                if(cur->right != nullptr) q.push(cur->right);
            }
        }
        return root;
    }
    //递归 时间复杂度O(n)  空间复杂度O(h)
    Node* connect(Node* root) {
        if(root == nullptr) return root;
        connectTwoNode(root->left, root->right);
        return root;
    }
    void connectTwoNode(Node* n1, Node* n2){
        if(n1 == nullptr && n2 == nullptr) return;

        n1->next = n2;
        connectTwoNode(n1->left, n1->right);
        connectTwoNode(n1->right, n2->left);
        connectTwoNode(n2->left, n2->right);
    }
};
```

#### [117. Populating Next Right Pointers in Each Node II](https://leetcode-cn.com/problems/populating-next-right-pointers-in-each-node-ii/)

```c++
class Solution {
public:
    //不是完全二叉树
    Node* connect(Node* root) {
        if(root == nullptr) return root;
        queue<Node*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            Node* prev = nullptr;
            for(int i = 0; i < size; i++){
                Node* cur = q.front(); q.pop();
                if(prev != nullptr){
                    prev->next = cur;
                }
                prev = cur;
                if(cur->left != nullptr) q.push(cur->left);
                if(cur->right != nullptr) q.push(cur->right);
            }
        }
        return root;
    }
};
```

#### [129. Sum Root to Leaf Numbers](https://leetcode-cn.com/problems/sum-root-to-leaf-numbers/)

```c++
class Solution {
public:
    int sum = 0; //记录结果
    int sumNumbers(TreeNode* root) {
        if(root == nullptr) return 0;
        traverse(root, 0);
        return sum;
    }
    void traverse(TreeNode* root, int num){ 
        if(root == nullptr) return;
        num = num*10 + root->val; //num是root-to-leaf的值
        if(root->left == nullptr && root->right == nullptr){
            sum += num;
        }
        traverse(root->left, num);
        traverse(root->right, num);
    }
};
```

#### [199. Binary Tree Right Side View](https://leetcode-cn.com/problems/binary-tree-right-side-view/)

```C++
class Solution {
public:
    //DFS
    vector<int> res;
    int depth = 0;
    vector<int> rightSideView(TreeNode* root) {
        if(root == nullptr) return res;
        traverse(root);
        return res;
    }
    void traverse(TreeNode* root) {
        if(root == nullptr) return;
        depth++;
        if(res.size() < depth) 
            //当前层还没有记录值，root即为最右侧的结点
            res.push_back(root->val);
        traverse(root->right); //先遍历左子树    
        traverse(root->left);
        depth--;
    }
    //BFS
    vector<int> rightSideView(TreeNode* root) {
        vector<int> res;
        if(root == nullptr) return res;
        queue<TreeNode*> q;
        q.push(root);
        while(!q.empty()){
            int size = q.size();
            TreeNode* rightNode = q.front();
            for(int i = 0; i < size; i++){
                TreeNode* cur = q.front(); q.pop();
                //右侧结点先入队
                if(cur->right != nullptr) q.push(cur->right);
                if(cur->left  != nullptr) q.push(cur->left);
            }
            res.push_back(rightNode->val);
        }
        return res;
    }
};
```

#### [222. Count Complete Tree Nodes](https://leetcode-cn.com/problems/count-complete-tree-nodes/)

```c++
class Solution {
public:
    //一：递归，遍历模板 时间复杂度O(n) 空间复杂度O(n)
    int countNodes(TreeNode* root) {
        if(root == nullptr) return 0;
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
    //二：一棵完全二叉树的两棵子树，至少有一棵是满二叉树，此时知道树高就能求出子树的结点数
    int countNodes(TreeNode* root){
        if(root == nullptr) return 0;
        TreeNode *l = root, *r = root;
        int lh = 0, rh = 0; 
        while(l != nullptr){
            l = l->left;
            lh++;
        }
        while(r != nullptr){
            r = r->right;
            rh++;
        }
        if(lh == rh) return (1 << lh) - 1; //相当于2^lh - 1，位运算效率更高
        return 1 + countNodes(root->left) + countNodes(root->right);
    }
};
```

#### [236. Lowest Common Ancestor of a Binary Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-tree/)

```c++
/* 情况1 root为p，q中的一个，公共祖先为root
 * 情况2 p和q分别在root的左右子树上，公共祖先仍为root
 * 情况3 p和q同时在root的左/右子树上，继续向下遍历
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == nullptr) return nullptr;
        if(p == root || q == root) return root; //情况1

        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        //if(left == nullptr && right == nullptr) return nullptr; //没找到公共祖先
        if(left != nullptr && right != nullptr) return root;    //情况2
        return left ? left : right; //情况3
    }
};
```

#### [331. Verify Preorder Serialization of a Binary Tree](https://leetcode-cn.com/problems/verify-preorder-serialization-of-a-binary-tree/)

```c++
class Solution {
    //整个二叉树：入度=出度
    //除了根结点，每个非空结点会提供 1入度和2出度；
    //"#"空结点只提供1入度
    public boolean isValidSerialization(String preorder) {
        if(preorder == "") return true;
        int sub = 1; //sub = 出度-入度，在二叉树遍历完之前，sub > 0
        //sub = 1是为了根结点和其他非空结点统一
        for(String node : preorder.split(",")){
            sub--; //每个结点会提供1入度
            if(sub < 0)
                return false;
            if(!node.equals("#"))
                sub += 2; //非空结点提供2出度
        }
        return sub == 0;
    }
}
```

#### [437. Path Sum III](https://leetcode-cn.com/problems/path-sum-iii/)

```c++

```



#### [654. Maximum Binary Tree](https://leetcode-cn.com/problems/maximum-binary-tree/)

```c++
class Solution {
public:
    //时间复杂度O(n^2)   空间复杂度O(n)
    TreeNode* construct(vector<int>& nums, int start, int end){
        if(start > end) return nullptr;
        int index = -1;
        int rootVal = -0x3f3f3f3f;
        for(int i = start; i <= end; i++){ //找到[start,end]中的最大值作为根结点值
            if(nums[i] > rootVal){
                rootVal = nums[i];
                index = i;
            }
        }

        TreeNode* root = new TreeNode(rootVal);
        root->left = construct(nums, start, index-1);
        root->right = construct(nums, index+1, end);
        return root;
    }

    TreeNode* constructMaximumBinaryTree(vector<int>& nums) {
        return construct(nums, 0, nums.size()-1);
    }
};
```

#### [508. Most Frequent Subtree Sum](https://leetcode-cn.com/problems/most-frequent-subtree-sum/)

```java
class Solution {
    //key:子树和；value:子树和次数
    HashMap<Integer, Integer> sumToCnt = new HashMap<>();
    public int[] findFrequentTreeSum(TreeNode root) {
        sumToCnt(root);
        int maxCnt = Collections.max(sumToCnt.values()); //找出最大频次
        ArrayList<Integer> resList = new ArrayList<>();
        for(Integer key : sumToCnt.keySet()) {
            if(sumToCnt.get(key) == maxCnt)
                resList.add(key);
        }
        int[] res = new int[resList.size()];
        for(int i = 0; i < resList.size(); i++) 
            res[i] = resList.get(i);
        return res;
    }
	//后序遍历，统计各子树和的频次
    public int sumToCnt(TreeNode root) {
        if(root == null) return 0;
        int lSum = sumToCnt(root.left);
        int rSum = sumToCnt(root.right);
        int sum = root.val + lSum + rSum;
        sumToCnt.put(sum, sumToCnt.getOrDefault(sum, 0) + 1); 
        return sum;
    } 
}
```

#### [513. Find Bottom Left Tree Value](https://leetcode-cn.com/problems/find-bottom-left-tree-value/)

```java
class Solution {
    public int findBottomLeftValue(TreeNode root) {
        if(root.left == null && root.right == null) return root.val;
        //int depth = maxDepth(root);
        //int curLev = 1; //记录当前访问到的层数
        TreeNode res = root;
        ArrayDeque<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while(!q.isEmpty()){
            //if(curLev == depth) res = q.peek();
            
            //新思路，增加下面这行代码即可
            res = q.peek(); //leftmost node就是每次层序遍历时的队首结点

            int size = q.size();
            for(int i = 0; i < size; i++){
                TreeNode cur = q.poll();
                if(cur.left != null) q.offer(cur.left);
                if(cur.right != null) q.offer(cur.right);
            }
            //curLev++;
        }
        return res.val;
    }
    // 递归+层序遍历效率太低
    // public int maxDepth(TreeNode root) {
    //     if(root == null) return 0;
    //     int lDepth = maxDepth(root.left);
    //     int rDepth = maxDepth(root.right);
    //     return 1 + Math.max(lDepth, rDepth);
    // }
}
```

#### [515. Find Largest Value in Each Tree Row](https://leetcode-cn.com/problems/find-largest-value-in-each-tree-row/)

```java
class Solution {
    public List<Integer> largestValues(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        if(root == null) return res;
        ArrayDeque<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while(!q.isEmpty()){
            int size = q.size();
            int max = Integer.MIN_VALUE;
            for(int i = 0; i < size; i++){
                TreeNode cur = q.poll();
                max = Math.max(max, cur.val);
                if(cur.left != null) q.offer(cur.left);
                if(cur.right != null) q.offer(cur.right);
            }
            res.add(max);
        }
        return res;
    }
```

#### [623. Add One Row to Tree](https://leetcode-cn.com/problems/add-one-row-to-tree/)

```JAVA
class Solution {
    public TreeNode addOneRow(TreeNode root, int val, int depth) {
        if(depth == 1){
            TreeNode newRoot = new TreeNode(val);
            newRoot.left = root;
            return newRoot;
        }
        int curLev = 1; //当前遍历到的层次
        ArrayDeque<TreeNode> q = new ArrayDeque<>();
        q.offer(root);
        while(!q.isEmpty()){
            int size = q.size();
            for(int i = 0; i < size; i++) {
                TreeNode cur = q.poll();
                if(curLev != depth-1) {
                    if(cur.left != null) q.offer(cur.left);
                    if(cur.right != null) q.offer(cur.right);
                }else {
                    TreeNode newLRoot = new TreeNode(val); //cur结点的新左子树
                    TreeNode newRRoot = new TreeNode(val);
                    TreeNode oLTree = cur.left; //cur结点的原左子树
                    TreeNode oRTree = cur.right; 
                    cur.left = newLRoot;
                    newLRoot.left = oLTree;
                    cur.right = newRRoot;
                    newRRoot.right = oRTree;
                }
            }
            curLev++;
        }
        return root;
    }
}
```

#### [652. Find Duplicate Subtrees](https://leetcode-cn.com/problems/find-duplicate-subtrees/)

```java

```



### HARD

#### [297. Serialize and Deserialize Binary Tree](https://leetcode-cn.com/problems/serialize-and-deserialize-binary-tree/)  ***

```c++
class Codec {
public:
    //先序遍历进行序列化
    // Encodes a tree to a single string.
    string serialize(TreeNode* root) {
        if(root == nullptr) return "#";
        return to_string(root->val) + " " 
               + serialize(root->left) + " " + serialize(root->right);
    }

    // Decodes your encoded data to tree.
    TreeNode* deserialize(string data) {
        istringstream ss(data);
        return doDeser(ss);
    }

    TreeNode* doDeser(istringstream &ss ){
        string tmp; ss >> tmp;
        if(tmp=="#") return nullptr;

        TreeNode* root = new TreeNode(stoi(tmp));
        root->left = doDeser(ss);
        root->right = doDeser(ss);
        return root;
    }
};
```

## 二叉搜索树

### EASY

#### [108. Convert Sorted Array to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-array-to-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        if(nums.size() == 0) return nullptr;
        return buildBST(nums, 0, nums.size()-1);
    }
    TreeNode* buildBST(vector<int>& nums, int low, int high){
        if(low > high) return nullptr;
        int mid = (high - low) / 2 + low;
        TreeNode* root = new TreeNode(nums[mid]);
        root->left = buildBST(nums, low, mid-1);
        root->right = buildBST(nums, mid+1, high);
        return root;
    }
};
```

#### [235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        if(root == nullptr) return nullptr;
        if(p->val > q->val) //确保p结点的值小于q的，方便下面比较
            return lowestCommonAncestor(root, q, p); 
        if(root->val >= p->val && root->val <= q->val) //p、q在root两边(或值等于root)
            return root;
        if(root->val < p->val) //p、q均在root的右子树
            return lowestCommonAncestor(root->right, p, q);
        //p、q均在root的左子树
        return lowestCommonAncestor(root->left, p, q);
    }
};
```

#### [501. Find Mode in Binary Search Tree](https://leetcode-cn.com/problems/find-mode-in-binary-search-tree/)

```c++
class Solution {
public:
    //BST中序遍历有序
    vector<int> res;
    TreeNode* prev = nullptr; //上一次访问的结点
    int curNodeCnt = 0; //当前结点出现次数
    int maxNodeCnt = 0; //众数
    vector<int> findMode(TreeNode* root) {
        if(root == nullptr) return res;
        traverse(root);
        return res;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        if(prev == nullptr){ //初始化，访问的是根结点
            curNodeCnt = 1;
            maxNodeCnt = 1;
            res.push_back(root->val);
        }else{
            if(prev->val == root->val){ //有重复值
                curNodeCnt++;
                if(curNodeCnt == maxNodeCnt){
                    res.push_back(root->val);
                }else if(curNodeCnt > maxNodeCnt){ //更新众数
                    res.clear();
                    maxNodeCnt = curNodeCnt;
                    res.push_back(root->val);
                }
            }else{ //无重复值
                curNodeCnt = 1;
                if(curNodeCnt == maxNodeCnt)
                    res.push_back(root->val);
            }

        }
        prev = root; //更新上一次访问的结点
        traverse(root->right);
    }
};
```

#### [530. Minimum Absolute Difference in BST](https://leetcode-cn.com/problems/minimum-absolute-difference-in-bst/)

```c++
class Solution {
public:
    int minDif = INT_MAX;
    TreeNode* prev = nullptr;  
    int getMinimumDifference(TreeNode* root) {
        if(root == nullptr) return 0;
        traverse(root);
        return minDif;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        if(prev != nullptr){
            int sub = root->val - prev->val;
            minDif = sub < minDif ? sub : minDif;
        }
        prev = root;
        traverse(root->right);
    }
};
```

#### [653. Two Sum IV - Input is a BST](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

```c++
class Solution {
public:
    //中序遍历+数组双指针
    vector<int> inorder;
    bool findTarget(TreeNode* root, int k) {
        if(root == nullptr) return false;
        traverse(root);
        int i = 0, j = inorder.size()-1;
        while(i < j){
            int sum = inorder[i] + inorder[j];
            if(sum == k){
                return true;
            }else if(sum < k){
                i++;
            }else{
                j--;
            }
        }
        return false;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        inorder.push_back(root->val);
        traverse(root->right);
    }
    //哈希表
    set<int> s;
    bool findTarget(TreeNode* root, int k) {
        return traverse(root, k);
    }
    bool traverse(TreeNode* root, int k){
        if(root == nullptr) return false;
        if(s.find(k-root->val) != s.end()) 
            return true;
        s.insert(root->val); 
        return traverse(root->left, k) 
            || traverse(root->right, k);
    }
};

```

#### [700. Search in a Binary Search Tree](https://leetcode-cn.com/problems/search-in-a-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* searchBST(TreeNode* root, int val) {
        if(root == nullptr || root->val == val) return root;
        /*TreeNode* res;
        if(root->val > val) 
            res = searchBST(root->left, val);
        if(root->val < val) 
            res = searchBST(root->right, val);
        return res;*/
        return root->val>val 
             ? searchBST(root->left, val) 
             : searchBST(root->right, val);
    }
};
```

#### [783. Minimum Distance Between BST Nodes](https://leetcode-cn.com/problems/minimum-distance-between-bst-nodes/)

```c++
class Solution {
public:
    int minDiff = INT_MAX;
    TreeNode* prev = nullptr;
    int minDiffInBST(TreeNode* root) {
        traverse(root);
        return minDiff;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        if(prev != nullptr){
            minDiff = min(minDiff, root->val-prev->val);
        }
        prev = root;
        traverse(root->right);
    }
};
```

#### [897. Increasing Order Search Tree](https://leetcode-cn.com/problems/increasing-order-search-tree/)

```c++
class Solution {
public:
    //中序遍历+重新构造，简单但不太符合题目要求
    //可以采用 将二叉树展开为链表 的思路，对root直接进行操作
    TreeNode* increasingBST(TreeNode* root) {
        if(root == nullptr) return nullptr;
        //先递归的拉平左右子树
        TreeNode* left  = increasingBST(root->left);
        TreeNode* right = increasingBST(root->right);
        root->left = nullptr; 
        root->right = right;  //拉平左右子树
        if(left == nullptr)   //左子树为空，则不用处理
            return root;
        //左子树不为空，则要把根结点和右子树接到左子树右侧末尾
        TreeNode* temp = left;
        while(temp != nullptr && temp->right != nullptr)
            temp = temp->right;
        temp->right = root;
        return left;
    }
};
```

#### [938. Range Sum of BST](https://leetcode-cn.com/problems/range-sum-of-bst/)

```c++
class Solution {
public:
    int rangeSumBST(TreeNode* root, int low, int high) {
        if(root == nullptr) return 0;
        if(root->val > high) 
            return rangeSumBST(root->left, low, high);
        if(root->val < low)
            return rangeSumBST(root->right, low, high);

        return root->val
             + rangeSumBST(root->left, low, high)
             + rangeSumBST(root->right, low, high);
    }
};
```

#### [剑指 Offer 54. 二叉搜索树的第k大节点](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-di-kda-jie-dian-lcof/)

```c++
class Solution {
public:
    int kthNode = -1;
    int k;
    int kthLargest(TreeNode* root, int k) {
        if(root == nullptr || k == 0) return -1;
        this->k = k;
        traverse(root);
        return kthNode;
    }
    //第k大，所以先遍历右子树
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->right);
        if(--k == 0) {
            kthNode = root->val;
            return;
        }
        traverse(root->left);
    }
};
```

#### [剑指 Offer 68 - I. 二叉搜索树的最近公共祖先](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

同[235. Lowest Common Ancestor of a Binary Search Tree](https://leetcode-cn.com/problems/lowest-common-ancestor-of-a-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        int minVal = min(p->val, q->val);
        int maxVal = max(p->val, q->val);
        TreeNode* lca = root;
        while(root != nullptr){
            if(root->val >= minVal && root->val <= maxVal){
                lca = root;
                break;
            }else if(root->val > maxVal){
                root = root->left;
            }else{
                root = root->right;
            }
        }
        return lca;
    }
};
```

#### [剑指 Offer II 052. 展平二叉搜索树](https://leetcode-cn.com/problems/NYBBNL/)

同[897. Increasing Order Search Tree](https://leetcode-cn.com/problems/increasing-order-search-tree/)  [面试题 17.12. BiNode](https://leetcode-cn.com/problems/binode-lcci/)

```c++
class Solution {
public:
    //哨兵头结点法
    TreeNode* dHead = new TreeNode(-1);
    TreeNode* cur = dHead;
    TreeNode* increasingBST(TreeNode* root) {
        if(root == nullptr) return nullptr;
        if(root->left == nullptr && root->right == nullptr) return root;
        traverse(root);
        return dHead->right;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        root->left = nullptr;
        cur->right = root;
        cur = root;
        traverse(root->right);
    }
};
```

#### [剑指 Offer II 056. 二叉搜索树中两个节点之和](https://leetcode-cn.com/problems/opLdQZ/)

同[653. Two Sum IV - Input is a BST](https://leetcode-cn.com/problems/two-sum-iv-input-is-a-bst/)

```c++
class Solution {
public:
    //数组双指针
    vector<int> inorder;
    bool findTarget(TreeNode* root, int k) {
        if(root == nullptr) return false;
        if(root->left == root->right) return false;
        traverse(root);
        int i = 0, j = inorder.size()-1;
        while(i < j){
            int sum = inorder[i] + inorder[j];
            if(sum == k) return true;
            else if(sum > k) j--;
            else i++;
        }
        return false;
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        inorder.push_back(root->val);
        traverse(root->right);
    }
};
```



### MEDIUM

#### [95. Unique Binary Search Trees II](https://leetcode-cn.com/problems/unique-binary-search-trees-ii/)

```c++
class Solution {
public:
    vector<TreeNode*> generateTrees(int n) {
        if(n <= 0) return {nullptr};
        return buildBST(1, n);
    }
    //生成[low,high]闭区间内的所有BST
    vector<TreeNode*> buildBST(int low, int high){
        if(low > high) return {nullptr};
        vector<TreeNode*> res;
        //依次以i为根结点
        for(int i = low; i <= high; i++){
            vector<TreeNode*> lefts  = buildBST(low, i-1);
            vector<TreeNode*> rights = buildBST(i+1, high);
            //向根结点组合所有可能的左右子树
            for(TreeNode* left : lefts){
                for(TreeNode* right : rights){
                    TreeNode* root = new TreeNode(i);
                    root->left = left;
                    root->right = right;
                    res.push_back(root);
                }
            }
        }
        return res;
    }
};
```

#### [96. Unique Binary Search Trees](https://leetcode-cn.com/problems/unique-binary-search-trees/)

<img src="LeetCode 刷题笔记.assets/image-20220128102331170.png" alt="image-20220128102331170" style="zoom:67%;" />

```c++
//动态规划思路
//dp[i] 表示有i个结点是，共有dp[i]个不同的BST
//dp[0] = 1  dp[1] = 1
class Solution {
public:
    //自底向上
    int numTrees(int n) {
        vector<int> dp(n+1, 0);
        dp[0] = 1; dp[1] = 1;
        for(int i = 2; i <= n; i++){ //结点个数
            for(int j = i; j >0; j--){ 
                //以第j=i个结点为根，则左子树有j-1个结点，右子树有i-j个结点
                dp[i] += dp[j-1]*dp[i-j];
            }
        }
        return dp[n];
    }
};
```

#### [98. Validate Binary Search Tree](https://leetcode-cn.com/problems/validate-binary-search-tree/)

```C++
class Solution {
public:
    TreeNode* prev = nullptr;
    bool flag = true;
    bool isValidBST(TreeNode* root) {
        if(root == nullptr) return true;
        traverse(root);
        return flag;
    }
    //BST的中序遍历一定升序
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        if(prev != nullptr && root->val <= prev->val){ //根据测例，前驱结也不能等于当前结点点
            flag = false;
            return;
        }
        prev = root;
        traverse(root->right);
    }
};
```

#### [99. Recover Binary Search Tree](https://leetcode-cn.com/problems/recover-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* prev   = nullptr;
    TreeNode* first  = nullptr; //第一个需要交换的值
    TreeNode* second = nullptr;
    void recoverTree(TreeNode* root) {
        if(root == nullptr) return;
        traverse(root);
        int temp = first->val;
        first->val = second->val;
        second->val = temp;
        //swap(first->val, second->val);
    }
    void traverse(TreeNode* root){
        if(root == nullptr) return;
        traverse(root->left);
        if(prev != nullptr && root->val < prev->val){
            if(first == nullptr) first = prev; //第一个失序的较大值一定需要交换
            second = root; //而较小值则不一定，需要继续遍历
        }
        prev = root;
        traverse(root->right);
    }
};
```

#### [109. Convert Sorted List to Binary Search Tree](https://leetcode-cn.com/problems/convert-sorted-list-to-binary-search-tree/)

```c++
class Solution {
public:
    TreeNode* sortedListToBST(ListNode* head) {
        if(head == nullptr) return nullptr;
        return buildBST(head, nullptr);
    }
    TreeNode* buildBST(ListNode* head, ListNode* tail){
        if(head == tail) return nullptr; //注意base case
        ListNode* mid = findListMidNode(head, tail);
        TreeNode* root = new TreeNode(mid->val);
        root->left = buildBST(head, mid);
        root->right = buildBST(mid->next, tail);
        return root;
    }
    ListNode* findListMidNode(ListNode* head, ListNode* tail){
        if(head == nullptr) return nullptr;
        ListNode *slow = head; 
        ListNode *fast = head;
        while(fast != tail && fast->next != tail){
            slow = slow->next;
            fast = fast->next->next;
        }
        return slow;
    }
};
```

#### [173. Binary Search Tree Iterator](https://leetcode-cn.com/problems/binary-search-tree-iterator/)

```c++
class BSTIterator {
private:
    stack<TreeNode*> stk;
    //压入根以及左侧所有分支结点，模拟中序遍历
    void pushLeftBranch(TreeNode* root) {
        while(root != nullptr){
            stk.push(root);
            root = root->left;
        }
    }
public:
    BSTIterator(TreeNode* root) {
        pushLeftBranch(root);
    }
    
    int next() {
        TreeNode* node = stk.top(); stk.pop();
        pushLeftBranch(node->right); //右侧结点入栈
        return node->val;
    }
    
    bool hasNext() {
        return !stk.empty();
    }
};
```

#### [230. Kth Smallest Element in a BST](https://leetcode-cn.com/problems/kth-smallest-element-in-a-bst/)

```c++
class Solution {
public:
    int kthSmall = 0; 
    int cnt = 0; //记录访问到第几个结点
    int kthSmallest(TreeNode* root, int k) {
        traverse(root, k);
        return kthSmall;
    }
    void traverse(TreeNode* root, int k){
        if(root == nullptr) return;
        traverse(root->left, k);
        cnt++;
        if(cnt == k) {
            kthSmall = root->val;
            return;
        }
        traverse(root->right, k);
    }
};
```

#### [449. Serialize and Deserialize BST](https://leetcode-cn.com/problems/serialize-and-deserialize-bst/)

```java
ublic class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder ans = new StringBuilder();
        serialize(root, ans);
        return ans.toString();
    }
    private void serialize(TreeNode root, StringBuilder ans) {
        if(root == null) return;
        //先序遍历
        ans.append(root.val).append(",");
        serialize(root.left, ans);
        serialize(root.right, ans);
    }

    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        if(data.isEmpty()) return null;
        LinkedList<Integer> inorder = new LinkedList<>();
        for(String node : data.split(","))
            inorder.offer(Integer.parseInt(node));
        return deserialize(inorder, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    private TreeNode deserialize(LinkedList<Integer> inorder, int min, int max){
        if(inorder.isEmpty()) return null;
        int rootVal = inorder.getFirst();
        if(rootVal > max || rootVal < min)  return null;
        inorder.removeFirst();

        TreeNode root = new TreeNode(rootVal);
        root.left = deserialize(inorder, min, rootVal);
        root.right = deserialize(inorder, rootVal, max);

        return root;
    }
}
```

#### [450. Delete Node in a BST](https://leetcode-cn.com/problems/delete-node-in-a-bst/)

1. 被删除的结点是叶子结点，直接删除；
2. 该结点只有一个孩子结点，让这个孩子接替自己的位置；
3. 该结点有两个孩子结点，需要找到该结点的右子树中最小结点(或左子树中最大结点) 来接替自己的位置。

```java
class Solution {
    public TreeNode deleteNode(TreeNode root, int key) {
        if(root == null) return null;
        if(root.val == key) {
            //情况1、2
            if(root.left == null) return root.right;
            if(root.right == null) return root.left;
            //情况3 找到该结点的右子树中最小的结点接替自己的位置
            TreeNode minNode = root.right; 
            while(minNode.left != null) minNode = minNode.left; //右子树中的最小结点
            root.val = minNode.val; //替换该结点的值
            //向下递归，删除该最小结点(因为最小结点已经替换到该结点的位置)
            root.right = deleteNode(root.right, minNode.val);
        }else if(root.val > key) {
            root.left = deleteNode(root.left, key);
        }else {
            root.right = deleteNode(root.right, key);
        }
        return root;
    }
}
```

#### [538. Convert BST to Greater Tree](https://leetcode-cn.com/problems/convert-bst-to-greater-tree/)

```java
class Solution {
    //思路1：先递归求出整棵树的累加和，在中序遍历得到结果。效率很低
    int sum = 0;    //记录整棵BST的累加和
    int preSum = 0; //记录当前结点之前的所有结点累加和
    public TreeNode convertBST(TreeNode root) {
        if(root == null) return null;
        sum = sumOfTree(root);
        inOrder(root);
        return root;
    }
    public void inOrder(TreeNode root) {
        if(root == null) return;
        inOrder(root.left);
        int temp = preSum; //先保存preSum的值
        preSum += root.val;
        root.val = sum - temp;
        inOrder(root.right);
    }
    public int sumOfTree(TreeNode root) {
        if(root == null) return 0;
        return root.val 
             + sumOfTree(root.left)
             + sumOfTree(root.right);
    }
    //思路2：逆中序遍历
    int sum = 0;
    public TreeNode convertBST(TreeNode root) {
        if(root == null) return null;
        convertBST(root.right);
        sum += root.val;
        root.val = sum;
        convertBST(root.left);
        return root;
    }
}
```



### HARD

## 双指针



## 前缀和

## 差分数组

## 队列/栈

## 堆

#### [703. Kth Largest Element in a Stream](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

```c++

```

#### [剑指 Offer II 059. 数据流的第 K 大数值](https://leetcode-cn.com/problems/jBjn9C/)

同[703. Kth Largest Element in a Stream](https://leetcode-cn.com/problems/kth-largest-element-in-a-stream/)

```c++

```



## 数据结构设计

## 图论

## 暴力搜索算法

## 动态规划

### EASY

### MEDIUM

#### [96. Unique Binary Search Trees](https://leetcode-cn.com/problems/unique-binary-search-trees/)

<img src="LeetCode 刷题笔记.assets/image-20220128102331170.png" alt="image-20220128102331170" style="zoom:67%;" />

```c++
//动态规划思路
//dp[i] 表示有i个结点是，共有dp[i]个不同的BST
//dp[0] = 1  dp[1] = 1
class Solution {
public:
    //自底向上
    int numTrees(int n) {
        vector<int> dp(n+1, 0);
        dp[0] = 1; dp[1] = 1;
        for(int i = 2; i <= n; i++){ //结点个数
            for(int j = i; j >0; j--){ 
                //以第j=i个结点为根，则左子树有j-1个结点，右子树有i-j个结点
                dp[i] += dp[j-1]*dp[i-j];
            }
        }
        return dp[n];
    }
};
```

#### [337. House Robber III](https://leetcode-cn.com/problems/house-robber-iii/)

```java
class Solution {
    //mem备忘录，记录以当前结点为根，能偷的最大钱数
    Map<TreeNode, Integer> mem = new HashMap<>();
    public int rob(TreeNode root) {
        if(root == null) return 0;
        if(mem.containsKey(root)) //消除重叠子问题
            return mem.get(root);
        //抢这一层，然后去下下层
        int rob = root.val 
                + (root.left == null 
                  ? 0 : rob(root.left.left) + rob(root.left.right))
                + (root.right == null
                  ? 0 : rob(root.right.left) + rob(root.right.right));
        //不抢这一层，去下一层
        int not_rob = rob(root.left) + rob(root.right);
        int res = Math.max(rob, not_rob);
        mem.put(root, res);
        return res;
    }
}
```

#### [437. Path Sum III](https://leetcode-cn.com/problems/path-sum-iii/)

```java
class Solution {
    //dfs暴力搜索，遍历二叉树的每个结点，检测当前结点向下延申共有多少条路经
    public int pathSum(TreeNode root, int targetSum) {
        if(root == null) return 0;
        return dfs(root, targetSum)
             + pathSum(root.left, targetSum)
             + pathSum(root.right, targetSum);
    }
    //返回以root为根结点的 路径和等于val 的路径数目
    public int dfs(TreeNode root, int val) {
        if(root == null) return 0;
        int sum = 0;
        if(root.val == val) sum++;
        return sum 
             + dfs(root.left, val-root.val)
             + dfs(root.right, val-root.val);
    }
    //前缀和，消除重复运算
    //记录根节点root到当前节点p的路径上除当前节点以外所有节点的前缀和
    //(key=前缀和，value=该前缀和的个数)
    Map<Long, Integer> prefix = new HashMap<>();
    public int pathSum(TreeNode root, int targetSum) {
        if(root == null) return 0;
        prefix.put(0L, 1); //空路径
        return dfs(root, 0L, targetSum);
    }

    public int dfs(TreeNode root, Long curSum, int targetSum){
        if(root == null) return 0;
        
        curSum += root.val;
        int res = prefix.getOrDefault(curSum-targetSum, 0);
        prefix.put(curSum, prefix.getOrDefault(curSum, 0)+1);

        res += dfs(root.left, curSum, targetSum);
        res += dfs(root.right, curSum, targetSum);

        prefix.put(curSum, prefix.getOrDefault(curSum, 0)-1);
        curSum -= root.val;
        return res;
    }
}
```



### HARD

## 其他经典算法

## 附：c++常用容器&方法

#### 1. string

> string构造函数

```c++
//string是一个类，类内部封装了char\*，管理这个字符串
#include <string>
int main(){
    string s1;      //创建空字符串，调用无参构造函数string()
    
    const char* str = "hello world";
    string s2(str); //c_string转换成string,string(const char* s);
    
    string s3(s2);  //调用拷贝构造函数,string(const string& str);
    
    string s4(10, 'a'); //使用n个字符c初始化,string(int n,char c)
}
```

> string赋值

```c++
int main(){
    string str1 = "hello world";
    
    string str2 = str1;
    
    string str3 = 'a';
    
    string str4;
    str4.assign("hello c++");
    
    string str5;
    str5.assign("hello c++",5);//把字符串s的前n个字符赋给当前的字符串
    
    string str6;
    str6.assign(str5);
    
    string str7;
    str7.assign(5, 'x');//用n个字符c赋给当前字符串
}
```

> string字符串拼接

```c++
int main(){
    string str1 += '字符' / "字符串" / str; //使用+=操作符拼接
    
    str1.append('字符' / "字符串" / str); //连接到str1结尾，同+=
    str1.apend(str, n); //把字符串s的前n个字符连接到当前字符串结尾
    str1.apend(str, pos, n);//字符串s中从pos开始的n个字符连接到字符串结尾
}
```

> string查找和替换

```c++
int main(){
    //查找
    string str1 = "abcdefgde";
    int pos = str1.find("de"); //查找字符/字符串第一次出现的位置
    // pos == -1 ? 未找到 : pos(>=0)
    pos = str1.rfind("de");    //查找字符/字符串最后次出现的位置
    
    //替换
    str1.replace(1, 3, "abc");  //替换从pos开始n个字符为字符串“abc”
    //string& replace(int pos, int n, const string& str);
}
```

> string字符串比较

```c++
//字符串比较是按字符的ASCII码进行对比
/* =返回0    >返回1     <返回-1 */
string s1 = "hello";
string s2 = "aello";
int ret = s1.compare(s2);
```

> string字符存取

```c++
string str = "hello world";
cout << str[i] << " ";
cout << str.at(i) << " ";

//字符修改
str[0] = 'x';
str.at(1) = 'x';
```

> string插入和删除

```c++
//插入
str.insert(1, "111");  //在位置1处插入字符串
//string& insert(int pos, int n, char c);//在指定位置插入n个字符c

//删除
str.erase(1, 3);  // //删除从Pos开始的n个字符 
//string& erase(int pos, int n = npos);` 
```

> string子串

```c++
string email = "hello@sina.com";
int pos = email.find("@");
string username = email.substr(0, pos);
//string substr(int pos = 0, int n = npos) const;//返回由pos开始的n个字符组成的字符串
```

#### 2. vector

```
vector数据结构和数组非常相似，也称为单端数组；
不同之处在于数组是静态空间，而vector可以动态扩展，但动态扩展并不是在原空间之后续接新空间，而是找更大的内存空间，然后将原数据拷贝新空间，释放原空间；
vector容器的迭代器是支持随机访问的迭代器。
```

>vector构造函数

```c++
#include <deque>
vector<int> v1; //无参构造
vector<int> v2(v1.begin(), v1.end()); //将v[begin(), end())区间中的元素拷贝给本身
vector<int> v3(10, 100);//将n个elem拷贝给本身

void printVector(vector<int>& v) {
	for (vector<int>::iterator it = v.begin(); it != v.end(); it++) {
		cout << *it << " ";
	}
	cout << endl;
}
```

> vector赋值操作

```c++
vector<int>v2;
v2 = v1;

vector<int>v3;
v3.assign(v1.begin(), v1.end());//将[beg, end)区间中的数据拷贝赋值

vector<int>v4;
v4.assign(10, 100);//将n个elem拷贝赋值给本身
```

> vector容量和大小

```c++
v1.empty() == 1 ? 空 : 非空
v1.capacity(); //容量
v1.size();     //元素个数
v1.resize(10); //重新指定容器的长度，若容器变长，则以默认值填充新位置
v1.resize(10, 5); //若容器变长，则以指定值填充新位置；容器变短，则末尾超出容器长度的元素被删除
```

> vector插入和删除

```c++
v1.push_back(elem); //尾部插入元素elem
v1.pop_back();      //删除最后一个元素

v1.insert(v1.begin(), 100);   //迭代器指向位置pos插入元素elem
v1.insert(v1.begin(), 2, 100);//迭代器指向位置pos插入count个元素elem
//insert(const_iterator pos, int count, elem);

v1.erase(const_iterator pos); //删除迭代器指向的元素
v1.erase(const_iterator start, const_iterator end);//删除迭代器从start到end之间的元素

v1.clear(); //清空
```

> vector数据存取

```c++
v1[i];       //返回索引i所指的数据
v1.at(i);
v1.front();  //返回容器中第一个数据元素
v1.back();   //返回容器中最后一个数据元素
```

> vector互换容器

```c++
v1.swap(v2); //将v1与v2的元素互换
```

> vector预留空间

```c++
//减少vector在动态扩展容量时的扩展次数
v1.reserve(int len);
//容器预留len个元素长度，预留位置不初始化，元素不可访问。
```

#### 3. deque

```
双端数组，可以对头端和尾端进行插入删除操作
但vector访问元素时的速度会比deque快,这和两者内部实现有关
```

> deque构造函数

```c++
deque<int> d1; //无参构造函数
deque<int> d2(d1.begin(),d1.end());
deque<int> d3(10,100); //deque(n, elem)
deque<int> d4 = d3;
```

> deque赋值操作

```c++
deque<int>d2;
d2 = d1;
d3.assign(d1.begin(), d1.end());
d4.assign(10, 100); //assign(n, elem);
```

> deque大小操作

```c++
d1.empty() == 1 ? 空 : 非空
d1.capacity(); //容量
d1.size();     //元素个数
d1.resize(10); //重新指定容器的长度，若容器变长，则以默认值填充新位置
d1.resize(10, 5); //若容器变长，则以指定值填充新位置；容器变短，则末尾超出容器长度的元素被删除
```

> deque插入和删除

```c++
d1.push_front(elem); //头部插入元素elem
d1.pop_front();      //删除第一个元素
d1.push_back(elem);  //尾部插入元素elem
d1.pop_back();       //删除最后一个元素

d1.insert(v1.begin(), 100);   //迭代器指向位置pos插入元素elem
d1.insert(v1.begin(), 2, 100);//迭代器指向位置pos插入count个元素elem
//insert(const_iterator pos, int count, elem);

d1.erase(const_iterator pos); //删除迭代器指向的元素
d1.erase(const_iterator start, const_iterator end);//删除迭代器从start到end之间的元素

d1.clear(); //清空
```

> deque数据存取

```c++
d1[i];
d1.at(i);
d1.front();  //返回容器中第一个数据元素
d1.back();   //返回容器中最后一个数据元素
```

> deque排序

```c++
#include <algorithm>
d1.sort(iterator beg, iterator end); //对beg和end区间内元素进行排序
```

#### 4. stack

```
栈(FILO)中只有顶端的元素才可以被外界使用，因此栈不允许有遍历行为
栈中进入数据称为  --- 入栈 push
栈中弹出数据称为  --- 出栈  pop
```

```c++
//常用操作
//1.构造函数
stack<T> stk;
stack(const stack &stk);
//2.数据存取
stk.push(elem); //向栈顶添加元素
stk.pop();      //从栈顶移除第一个元素
stk.top();      //返回栈顶元素
//3.大小操作
stk.empty() == 1 ? 栈空 ：非空 ;
stk.size();       //栈中元素个数
```

#### 5. queue

```
队列(FIFO)容器允许从一端新增元素，从另一端移除元素
队列中只有队头和队尾才可以被外界使用，因此队列不允许有遍历行为
队列中进数据称为 --- 入队    push
队列中出数据称为 --- 出队    pop
```

```c++
//常用操作
//1.构造函数
queue<T> q;
queue(const queue &q);
//2.数据存取
q.push(elem); //向队尾添加元素
q.pop();      //从队头移除第一个元素
q.back();     //返回队尾元素
q.front();    //返回队首元素
//3.大小操作
q.empty() == 1 ? 栈空 ：非空 ;
q.size();       //栈中元素个数
```

#### 6. list

```
链表（list）是一种物理存储单元上非连续的存储结构，数据元素的逻辑顺序是通过链表中的指针链接实现的。
STL中的链表是一个双向循环链表，链表list中的迭代器只支持前移和后移，属于双向迭代器。
```

> list构造函数

```c++
#include <list>
list<int> L1;
list<int> L2(L1.begin(),L1.end()); //[beg, end)
list<int> L3(L2);
list<int> L4(10, 1000); //list(n,elem);
```

> list复制和交换

```c++
list<int>L2;  L2 = L1;
list<int>L3;  L3.assign(L2.begin(), L2.end());
list<int>L4;  L4.assign(10, 100); //assign(n, elem);
//容器元素交换
L1.swap(L2);
```

> list大小操作

```c++
L1.size();
L1.empty()==1 ? 链表空 : 非空;
L1.resize(num);//重新指定容器的长度为num,若容器变长,以默认值填充新位置;
L1.resize(num, elem); //若容器变长，则以elem值填充新位置;如果容器变短，则末尾超出容器长度的元素被删除
```

> list插入和删除

```c++
//插入
L.push_back(elem);  //尾部添加一个元素
L.pop_back();       //删除尾部元素
L.push_front(elem); //头部添加一个元素
L.push_front();     //删除头部元素

list<int>::iterator it = L.begin();
L.insert(++it, 1000);
//删除
L.erase(begin, end); //删除[beg,end)区间的数据，返回下一个数据的位置
L.erase(pos);   //删除pos位置的数据，返回下一个数据的位置
                //参数均为迭代器
L.remove(elem); //删除容器中所有与elem值匹配的元素
L.clear();      //清空容器
```

> list数据存器

```c++
//不能随机访问
L.front(); //返回第一个元素
L.back();  //返回最后一个元素
```

> list反转和排序

```c++
list.reverse(); //反转链表
list.sort();    //链表排序，默认从小到大
```

#### 7. set / multiset

```
set/multiset属于关联式容器，底层结构是用二叉树实现，所有元素都会在插入时自动被排序。
set不允许容器中有重复的元素
multiset允许容器中有重复的元素
```

> set构造和赋值

```c++
#include <set>
set<int> s1;
set<int> s2(s1);  //拷贝构造
set<int>s3;  s3 = s2;  //赋值
```

> set大小和交换

```c++
s1.size();   //大小
s1.empty()==1 ? 空 ：非空;
s1.swap(s2); //交换
```

> set插入和删除

```c++
s.insert(elem);  //插入元素
s.erase(pos);
s.erase(begin, end);
s.erase(elem);   //删除容器中值为elem的元素
s.clear();       //清空容器
```

> set查找和统计

```c++
//查找
set<int>::iterator pos = s1.find(30);
//查找key是否存在.若存在,返回该键的元素的迭代器;若不存在,返回set.end()
pos != s1.end() ? *pos : 未找到
    
int num = s1.count(30); //统计key的元素个数
```

> set和multiset区别

```
set不可以插入重复数据，而multiset可以
set插入数据的同时会返回插入结果，表示插入是否成功
multiset不会检测数据，因此可以插入重复数据
```

> set排序

```c++
//set默认按从小到大对插入元素进行排序，可利用仿函数改变排序规则
class MyCompare 
{
public:
	bool operator()(int v1, int v2) {
		return v1 > v2;
	}
};

set<int, MyCompare> s;
```

#### 8. map / multimap

```c++
map中所有元素都是pair;
pair中第一个元素为key（键值），起到索引作用，第二个元素为value（实值）;
所有元素都会根据元素的键值自动排序。
map/multimap属于**关联式容器**，底层结构是用二叉树实现：
    map不允许容器中有重复key值元素
    multimap允许容器中有重复key值元素
```

>pair对组创建

```c++
//两种创建方式
pair<type, type> p ( value1, value2 );
pair<type, type> p = make_pair( value1, value2 );
value1 = p.first;
value2 = p.second;
```

> map构造和赋值

```c++
#include <map>
map<T1, T2> m;    map<int, int> m2(m); //拷贝构造
map<int, int>m3;  m3 = m2; //赋值

void printMap(map<int,int>&m)
{
	for (map<int, int>::iterator it = m.begin(); it != m.end(); it++)
	{
		cout << "key = " << it->first << " value = " << it->second << endl;
	}
	cout << endl;
}
```

> map大小和交换

```c++
m.size();   //大小
m.empty()==1 ? 空 ：非空;
m.swap(m1); //交换
```

> map插入和删除

```c++
m.insert(pair<int, int>(1, 2));  //插入元素
m.insert(make_pair(2, 20));

m.erase(pos);
m.erase(begin, end);
m.erase(key);   //删除容器中键为key的元素
m.clear();      //清空容器
```

> map查找和统计

```c++
//查找
map<int, int>::iterator pos = m.find(3);
pos != m.end() ? (*pos).first / pos->second : 未找到
    
int num = m.count(3); //统计键值为3的元素个数
```

#### 9. STL常用算法



## 附：java常用容器&方法

[java常用容器&方法](./java常用容器&方法.md)

