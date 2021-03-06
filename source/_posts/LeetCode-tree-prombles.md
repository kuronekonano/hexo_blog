---
layout: pos
title: leetcode在线编程【树专题】
cover: true
date: 2020-01-08 17:59:35
tags:
---
**二叉树的最小深度**
递归遍历每个节点并计数深度，遍历到叶子节点时更新最小深度，并返回，非叶子节点取返回值的最小深度返回

```c
class Solution {
public:
    int ans=9999999;
    int dfs(TreeNode *root,int dep)
    {
        dep++;
        if(root->left==NULL&&root->right==NULL)return min(ans,dep);
        int ldep=ans,rdep=ans;
        if(root->right)rdep=dfs(root->right,dep);
        if(root->left)ldep=dfs(root->left,dep);
        return min(ldep,rdep);
        
    }
    int run(TreeNode *root) {
        if(root==NULL)return 0;
        return dfs(root,0);
    }
};
```

---
**二叉树的后序遍历（非递归）**
用栈实现，基本上就是模拟输出的过程。首先放入根节点，最外层循环保证整棵树的正常输出，第一个内层循环用于模拟递归左孩子，即只要有左孩子就一直往栈里塞。 第二个内层循环模拟右孩子插入和递归回溯到根节点的过程。当回溯出栈时，判断栈顶是否与上一个输出的节点有父子关系【此处需要一个标记记录，即代码中lastnode】，若上一个输出节点是当前栈顶右孩子，说明回溯到了某个根节点，直接出栈。亦或是当栈顶元素没有右孩子时，那也该轮到当前栈顶输出了。若两种情况都不属于那就是该遍历右孩子了，直接右孩子进栈，并且退出第二个内层循环，因为此时遇到了一个新子树，按照顺序应模拟递归遍历左孩子的过程。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> postorderTraversal(TreeNode *root) {
        vector<int>ans;
        stack<TreeNode*>q;
        if(root==NULL)return ans;
        TreeNode *rt=root;
        q.push(rt);
        TreeNode *lastnode=NULL;
        while(!q.empty())
        {
            while(q.top()->left)q.push(q.top()->left);
            while(!q.empty())
            {
                if(lastnode==q.top()->right||q.top()->right==NULL)
                {
                    ans.push_back(q.top()->val);
                    lastnode=q.top();
                    q.pop();
                }
                else if(q.top()->right!=NULL)
                {
                    q.push(q.top()->right);
                    break;
                }
            }
        }
        return ans;
    }
};
```
---
**前序遍历二叉树（非递归）**
这个要比后序遍历简单得多，直接模拟根节点进栈，出栈输出时将**右孩子**入栈，再把**左孩子**入栈。因为栈的顺序是**先进后出**。跑一层循环即可。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> preorderTraversal(TreeNode *root) {
        vector<int>ans;
        if(root==NULL)return ans;
        stack<TreeNode *>q;
        q.push(root);
        while(!q.empty())
        {
            TreeNode *tmp=q.top();
            q.pop();
            ans.push_back(tmp->val);
            if(tmp->right)q.push(tmp->right);
            if(tmp->left)q.push(tmp->left);
        }
        return ans;
    }
};
```
---
Given a binary tree containing digits from0-9only, each root-to-leaf path could represent a number.
An example is the root-to-leaf path1->2->3which represents the number123.
Find the total sum of all root-to-leaf numbers.
For example,
```
    1
   / \
  2   3
```
The root-to-leaf path1->2represents the number12.
The root-to-leaf path1->3represents the number13.

Return the sum = 12 + 13 =25.
**输入一棵树，每个节点是一个一位数，一条路径的数是一个十进制数，输出所有路径的十进制数求和结果**

递归遍历，每次 ×10+val得到十进制数，叶子节点对ans求和即可。其实就是一个裸的遍历树过程。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int ans=0;
    void dfs(TreeNode *rt,int sum)
    {
        sum=sum*10+rt->val;
        if(rt->left==NULL&&rt->right==NULL)
        {
            ans+=sum;
            return ;
        }
        else 
        {
            if(rt->left!=NULL)dfs(rt->left,sum);
            if(rt->right!=NULL)dfs(rt->right,sum);
        }
        return ;
    }
    int sumNumbers(TreeNode *root) {
        TreeNode *tmp=root;
        if(tmp!=NULL)dfs(tmp,0);
        return ans;
    }
};
```
---
Given a binary tree, find the maximum path sum.
The path may start and end at any node in the tree.
For example:
Given the below binary tree,
```
       1
      / \
     2   3
```
Return6.
**输出该树最大路径和**
节点值存在负数，因此结果初始化是负数最大值。
递归遍历，叶子节点返回当前值大小。
非叶子节点计算，若某一条路径经过当前结点的时的路径最大和，并更新ans，而其左右两子树回溯的是经过子节点的最大和。设想，若当前节点作为最大路径和的一部分，那么肯定选择左子树中的最大和或右子树的最大和，即**max(leftsum,rightsum)**，以此回溯给父节点让其继续做筛选和判断。回溯的过程只提供子树最大和，用于给父节点搜集两段路径最大和，然后合并成为完整的路径和，并更新ans。
需要注意的是，因为存在负数，并且最大路径和的路径并不一定必须到大叶子节点，那么就有些负数节点的取舍问题。这就像树上的最大子段和。其实并没有那么复杂，最大子段和的DP算法是当求和小于0时舍去之前的结果，并初始化结果为0，那么此处也是，当左右子链的最大和甚至比只取当前结点的值还小时，那么只返回当前节点的值，这样就直接舍去了损耗求和的负数节点。
```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int ans=-999999;
    int dfs(TreeNode *rt)
    {
        if(rt->left==NULL&&rt->right==NULL) return rt->val;
        else 
        {
            int lsum=0,rsum=0;
            if(rt->left!=NULL) lsum=dfs(rt->left),ans=ans<lsum?lsum:ans;
            if(rt->right!=NULL) rsum=dfs(rt->right),ans=ans<rsum?rsum:ans;
            int tmp=lsum+rt->val+rsum;
            if(tmp>ans) ans=tmp;
            if(rt->val>ans) ans=rt->val;
            return max(rt->val+max(lsum,rsum),rt->val);
        }
    }
    int maxPathSum(TreeNode *root) {
        int tmp=dfs(root);
        if(ans<tmp)ans=tmp;
        return ans;
    }
};
```
---
Follow up for problem "Populating Next Right Pointers in Each Node".
What if the given tree could be any binary tree? Would your previous solution still work?
Note:
You may only use constant extra space.
For example,
Given the following binary tree,
```
         1
       /  \
      2    3
     / \    \
    4   5    7
```
After calling your function, the tree should look like:
```
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \    \
    4-> 5 -> 7 -> NULL
 ```
**给二叉树的每层加上指针变成链表**
首先这是一个层序遍历，然后为了区分每个节点是哪一层的，可以这样处理，之前不区分的广搜是while内直接对队首节点顺序遍历，在将子节点投入队列。此处可以用一个for循环将队列内现有值全部弹出，然后加上指针组成链表，顺便将其子节点插入队列。很明显因为每次弹出队列的都是一整层节点，放入队列的也是下一层节点，不会出现串层的情况，以此区分每个节点属于哪一层。

```c
/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if(root==NULL)return;
        queue<TreeLinkNode*>q;
        q.push(root);
        while(!q.empty())
        {
            TreeLinkNode *top=q.front();
            int len=q.size();
            q.pop();
            if(top->left)q.push(top->left);
            if(top->right)q.push(top->right);
            for(int i=1;i<len;i++)
            {
                TreeLinkNode *nxt=q.front();
                top->next=nxt;
                top=nxt;
                q.pop();
                if(top->left)q.push(top->left);
                if(top->right)q.push(top->right);
            }
            top->next=NULL;
        }
    }
};
```
---
Given a binary tree and a sum, find all root-to-leaf paths where each path's sum equals the given sum.
For example:
Given the below binary tree andsum = 22,
```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
 ```
return
```
[
   [5,4,11,2],
   [5,8,4,5]
]
```
**给棵树，给个数，输出路径和等于给的数的所有路径**
递归遍历，同时求和，叶子节点时判断路径和是否符合条件，符合条件塞入ans数组中，否则忽略。临时数组记录路径，作为参数递归下去。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int> >ans;
    void dfs(TreeNode *rt,vector<int>tmp,int ts,int sum)
    {
        tmp.push_back(rt->val);
        ts+=rt->val;
        if(rt->left==NULL&&rt->right==NULL)
        {
            if(ts==sum) ans.push_back(tmp);
            return;
        }
        else 
        {
            if(rt->left)dfs(rt->left,tmp,ts,sum);
            if(rt->right)dfs(rt->right,tmp,ts,sum);
        }
    }
    vector<vector<int> > pathSum(TreeNode *root, int sum) {
        if(root==NULL)return ans;
        vector<int>x;
        int ts=0;
        dfs(root,x,ts,sum);
        return ans;
    }
};
```
---
**输出树上是否存在某条路径和等于给出的数**

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int ans=0;
    bool hasPathSum(TreeNode *root, int sum) {
        if(root==NULL)return false;
        if(root->left==NULL&&root->right==NULL)
        {
            ans+=root->val;
            return ans==sum?true:false;
        }
        else
        {
            bool l=false,r=false; 
            ans+=root->val;
            if(root->left) l=hasPathSum(root->left,sum),ans-=root->left->val;
            if(root->right) r=hasPathSum(root->right,sum),ans-=root->right->val;
            return l||r;
        }
    }
};
```
---
Given a binary tree, determine if it is height-balanced.

For this problem, a height-balanced binary tree is defined as a binary tree in which the depth of the two subtrees of every node never differ by more than 1.
**判断所给树是否是平衡二叉树，不需要判断是否是搜索树**
根据定义平衡二叉树是一 棵**空树**或**它的左右两个子树的高度差的绝对值不超过1**，并且**左右两个子树都是一棵平衡二叉树**。

因此递归回溯的过程中返回左右两棵子树的**最大深度**，若子树的最大深度之差大于1则不符合条件。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool ans=true;
    int dfs(TreeNode *rt,int dep)
    {
        dep++;
        if(rt->left==NULL&&rt->right==NULL) return dep;
        else
        {
            int depl = dep, depr = dep;
            if(rt->left) depl = dfs(rt->left,dep);
            if(rt->right) depr = dfs(rt->right,dep);
            if(abs(depl-depr)>1) ans=false;
            return max(depl,depr);
        }
    }
    bool isBalanced(TreeNode *root) {
        int dep=0;
        if(root==NULL)return true;;
        dfs(root,dep);
        return ans?true:false;
    }
};
```
---
**递归层序遍历，并倒叙输出每层元素**。
用到了vector构造函数，其实就是先计算最大深度然后利用每层的递归深度来确定ans的vector中存在第几行。因为树的遍历顺序是普通的前序遍历，每层遍历顺序是层序的。

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int dfs(TreeNode *root,int dep)
    {
        if(root==NULL)return dep;
        dep++;
        int l=dep,r=dep;
        if(root->left==NULL&&root->right==NULL)return dep;
        if(root->left)l=dfs(root->left,dep);
        if(root->right)r=dfs(root->right,dep);
        return max(l,r);
    }
    void LOB(vector<vector<int> >&ans,TreeNode *root,int num)
    {
        num--;
        ans[num].push_back(root->val);
        if(root->left==NULL&&root->right==NULL)return ;
        if(root->left)LOB(ans,root->left,num);
        if(root->right)LOB(ans,root->right,num);
    }
    vector<vector<int> > levelOrderBottom(TreeNode *root) {
        int num=dfs(root,0);
        vector<vector<int> >ans(num,vector<int>());
        if(root==NULL)return ans;
        LOB(ans,root,num);
        return ans;
    }
};
```
---
**根据后序遍历和中序遍历建树，返回树根节点**
跟取中序遍历和前序遍历恢复树结构/输出后序遍历的题一样，记住阿里面试官跟我说的，层序遍历只需记录每个节点是否有子节点就可以恢复树结构。

恢复树结构【建树】只是将原题中，当前结点放入ans数组的操作变成return当前节点指针。递归回溯每个节点指针，并赋值左右孩子指针。
```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int find(vector<int>inorder,int tmp,int l,int r)
    {
        for(int i=l;i<r;i++)
            if(tmp==inorder[i])return i;
        return 0;
    }
    TreeNode *halfpath(vector<int>inorder,vector<int>postorder,int bl,int br,int al,int ar)
    {
        TreeNode *tmp=new TreeNode(postorder[br-1]);
        if(al==ar)return NULL;
        int root = find(inorder,postorder[br-1],al,ar);
        int len=ar-1-root;
        tmp->right=halfpath(inorder,postorder,br-len-1,br-1,root+1,ar);
        tmp->left=halfpath(inorder,postorder,bl,br-len-1,al,root);
        return tmp;
    }
    TreeNode *buildTree(vector<int> &inorder, vector<int> &postorder) {
       if(inorder.size()==0)return NULL;
       return halfpath(inorder,postorder,0,inorder.size(),0,inorder.size());
    }
};
```
---
**给中序和前序，构造树**，方法同上，注意下加减递归范围的边界就好了

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int find(vector<int>inorder,int tmp,int l,int r)
    {
        for(int i=l;i<r;i++)
            if(tmp==inorder[i])return i;
        return 0;
    }
    TreeNode *halfpath(vector<int>&preorder,vector<int>&inorder,int bl,int br,int al,int ar)
    {
        TreeNode *tmp=new TreeNode(preorder[bl]);
        if(al==ar)return NULL;
        int root=find(inorder,preorder[bl],al,ar);
        int len=root-al;
        tmp->left=halfpath(preorder,inorder,bl+1,bl+1+len,al,root);
        tmp->right=halfpath(preorder,inorder,bl+1+len,br,root+1,ar);
        return tmp;
    }
    TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder) {
        if(preorder.size()==0)return NULL;
        return halfpath(preorder,inorder,0,preorder.size(),0,preorder.size());
    }
};
```
---
**Z字形输出树结构**
基本上就是层序遍历的变形，跟层序遍历分层一样放到vector里，偶数层反转即可

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<vector<int> > zigzagLevelOrder(TreeNode *root) {
        vector<vector<int> >ans;
        if(root==NULL)return ans;
        queue<TreeNode*>q;
        q.push(root);
        int dep=0;
        while(!q.empty())
        {
            dep++;
            vector<int>tmp;
            int len=q.size();
            for(int i=1;i<=len;i++)
            {
                tmp.push_back(q.front()->val);
                if(q.front()->left)q.push(q.front()->left);
                if(q.front()->right)q.push(q.front()->right);
                q.pop();
            }
            if(!(dep&1))reverse(tmp.begin(),tmp.end());
            ans.push_back(tmp);
        }
        return ans;
    }
};
```
----
**判断一棵二叉树是否是镜像二叉树**
Given a binary tree, check whether it is a mirror of itself (ie, symmetric around its center).

For example, this binary tree is symmetric:
```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```
But the following is not:
```
    1
   / \
  2   2
   \   \
   3    3
```
Note: 
Bonus points if you could solve it both recursively and iteratively.

confused what"{1,#,2,3}"means? > read more on how binary tree is serialized on OJ.


OJ's Binary Tree Serialization:
The serialization of a binary tree follows a level order traversal, where '#' signifies a path terminator where no node exists below.

Here's an example:
```
   1
  / \
 2   3
    /
   4
    \
     5
```
The above binary tree is serialized as"{1,2,3,#,#,4,#,#,5}".

参数传递两个指针同时遍历移动，并且左指针按照正常前序遍历顺序走动，右指针按照左指针反方向递归移动。随时判断值是否相等即可。最后注意两者同时无孩子时也判定为true

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool dfs(TreeNode *left,TreeNode *right)
    {
        if(left->val==right->val)
        {
            bool l=false,r=false;
            if(left->left&&right->right)l=dfs(left->left,right->right);
            else if(left->left==NULL&&right->right==NULL)l=true;
            if(left->right&&right->left)r=dfs(left->right,right->left);
            else if(left->right==NULL&&right->left==NULL)r=true;
            return l&&r;
        }
        return false;
    }
    bool isSymmetric(TreeNode *root) {
        if(root==NULL)return true;
        if(root->left&&root->right)
            return dfs(root->left,root->right);
        else if(root->left==NULL&&root->right==NULL)return true;
        return false;
    }
};
```
---
 **检查两棵树是否结构和值都相同**
 两指针同时遍历对比即可。
 

```c
/**
 * Definition for binary tree
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    bool isSameTree(TreeNode *p, TreeNode *q) {
        if(p==NULL&&q==NULL)return true;
        if(p&&q)
        {
            if(p->val==q->val)
            {
                bool l=false,r=false;
                if(p->left&&q->left)l=isSameTree(p->left,q->left);
                else if(p->left==NULL&&q->left==NULL)l=true;
                if(p->right&&q->right)r=isSameTree(p->right,q->right);
                else if(p->right==NULL&&q->right==NULL)r=true;
                return l&&r;
            }
            return false;
        }
        return false;
    }
};
```
**给定两个非空二叉树 s 和 t，检验 s 中是否包含和 t 具有相同结构和节点值的子树。s 的一个子树包括 s 的一个节点和这个节点的所有子孙。s 也可以看做它自身的一棵子树。**

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/subtree-of-another-tree
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, x):
#         self.val = x
#         self.left = None
#         self.right = None

class Solution:
    def check (self, s, t):
        if t==None or s==None:
            return t==s
        ans = False
        if s.val == t.val:
            ans = True
        else:
            return False
        return ans and self.check(s.left,t.left) and self.check(s.right,t.right)
    def isSubtree(self, s: TreeNode, t: TreeNode) -> bool:
        ans = False
        if t == None or s==None:
            return False
        if s.val == t.val:
            ans = self.check(s,t)
        return ans or self.isSubtree(s.left,t) or self.isSubtree(s.right,t)
```
