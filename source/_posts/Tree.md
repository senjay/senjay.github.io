---
title: LeetCodeのTree
date:  2020-05-26 23:18:21
categories:
- C++
tags:
- 习题
---
# 关于树

## 简单

### 100 [相同的树](https://leetcode-cn.com/problems/same-tree/)

给定两个二叉树，编写一个函数来检验它们是否相同。

如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的。

```c++
class Solution {
public:
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if(!p&&!q)return true; //都为空返回true
        else if(p&&q&&p->val==q->val)//相等往下做判断
        return isSameTree(p->left,q->left)&&isSameTree(p->right,q->right);
        return false;
    }
};
```



### 101 [对称二叉树](https://leetcode-cn.com/problems/symmetric-tree/)

给定一个二叉树，检查它是否是镜像对称的。

上题的引申

```c++
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        return judge(root,root);
    }
    bool judge(TreeNode* p,TreeNode* q)
    {
            if(!p&&!q)return true;
            else if(p&&q&&p->val==q->val)
            return judge(p->left,q->right)&&judge(p->right,q->left);
            return false;
    }
};
```



### 104 [二叉树的最大深度](https://leetcode-cn.com/problems/maximum-depth-of-binary-tree/)

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if(!root)return 0;
        return max(maxDepth(root->left),maxDepth(root->right))+1;//返回左右最大值    
    }
};
```



## 中等

### 94 [二叉树的中序遍历](https://leetcode-cn.com/problems/binary-tree-inorder-traversal/)

非递归方法

```c++
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        stack<TreeNode*>s;
        vector<int> ans;
        while(!s.empty()||root)
        {
            if(root)
            {
                s.push(root);
                root=root->left;
            }
            else
            {
                TreeNode* temp=s.top();
                s.pop();
                ans.push_back(temp->val);
                root=temp->right;
            }
        }
        return ans;
    }
};
```



###  105 [从前序与中序遍历序列构造二叉树](https://leetcode-cn.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

递归构造，常规。

```c++
class Solution {
public:
    TreeNode* build(vector<int>& pre,int ps,int pe, vector<int>& inorder,int is,int ie)
    {
        if(ps>pe||is>ie)return NULL;
        TreeNode* t=new TreeNode();
        t->val=pre[ps];
        int k=is;
        while(is<ie&&inorder[k]!=t->val)k++;//寻找中序中的头节点
        t->left=build(pre,ps+1,ps+k-is,inorder,is,k-1);//构造左子树
        t->right=build(pre,ps+k-is+1,pe,inorder,k+1,ie);//构造右子树
        return t;
    }
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return build(preorder,0,preorder.size()-1,inorder,0,inorder.size()-1);
    }
};
```



## 困难



