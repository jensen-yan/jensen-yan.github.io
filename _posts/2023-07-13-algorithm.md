---
layout: default
title:  "算法套路总结"
date:   2023-07-13 
---



前不久为了秋招笔试做准备，做完了一次[labuladong的算法小抄](https://labuladong.gitee.io/algo//)

最近在复习一遍，边看边写一些算法套路总结，方便之后回顾和查阅。



## 框架思维

1. 数据结构存储只有  顺序存储（数组），链式存储（链表）

| 结构    | 顺序                   | 链式                                |
| ------- | ---------------------- | ----------------------------------- |
| 数组    | 数组                   | 链表                                |
| 队列/栈 | 顺序队列/栈            | 链式队列/栈                         |
| 图      | 邻接矩阵               | 邻接表                              |
| 树      | 堆（完全二叉树顺序存） | 普通树（二叉搜索树，AVL树，红黑树） |

|      | 顺序存储       | 链式存储     |
| ---- | -------------- | ------------ |
| 优点 | 查找O(1)       | 插入删除O(n) |
| 缺点 | 插入，删除O(n) | 查找O(1)     |

2. 对任何数据，只有增删改查，如何高效遍历+访问！

   1. 线性：优先 for/while 遍历；   递归
   2. 链式：优先  递归； for/while 遍历

   ```c++
   // 数组： 迭代 + 递归
   void traverse(vector<int> &arr) {
       for (int i = 0; i < arr.size(); i++) {
           // 线性迭代访问
       }
   }
   
   void traverse(vector<int> &arr, int i) {
       if (i == arr.size()) return;
       // 前序访问
       traverse(arr, i+1);
   	// 后序访问
   }
   ```

   ```c++
   // 链表； 迭代 + 递归
   void traverse(ListNode *head) {
       for (ListNode *p = head; p != nullptr; p = p->next) {
           // 访问 p->val
       }
   }
   
   void traverse(ListNode *head) {
       if (head == nullptr) return;
       // 前序访问head->val
       traverse(head->next);
       // 后序访问，可以倒着打印链表值
   }
   ```


   很多递归问题 == 树的问题！

3. 算法的本质就是穷举！计算机最大优点就是快
   1. 无重复
   2. 无遗漏



## 链表

1. 双指针：
   1. 以不同速度向后（不能向前）判断成环，slow+fast
   2. p1先走k步， p2再从头一起走

2. 合并、分解：需要有新链表的，加一个dummy虚拟头节点！
3. 删除节点curr, 保证pre, next 都存在



## 数组

1. 双指针：

   1. 不同速度向后：slow+fast
   2. 一前一后向中间：判断回文串
   3. 从中间向两边：最长回文子串

2. 二分查找（左右指针）：有序数组上，使用[a, b] 双闭合区间

3. 滑动窗口（快慢指针）：解决子串问题

4. 前缀和： 经常对子数组求和

5. 差分数组：经常对子数组增减

   

## 二叉树

1. 对于链式存储，只能使用递归，但分成两类
   1. 递归**遍历**一次：回溯法/DFS：void traverse()/ void backtrace(), 使用外部变量更新+辅助traverse函数
      1. 回溯：关注节点间的树枝，节点间移动过程
      2. DFS: 关注单个节点
   2. **分解**成子问题：动态规划： int dp(), 使用返回值更新，关注子树（相同结构）
      1. 一般需要：最优子结构+重叠子问题（用memo消除）
      2. 目的：求最值
2. 对于某一个节点，要做什么？在什么时候做？（前/中/后序）
   ```c++
   // 二叉树：递归
   void traverse(TreeNode *root) {
       if (root == nullptr) return;
       // 前序访问：普通访问
       traverse(root->left);
       // 中序访问: BST（二叉查找树）
       traverse(root->right);
       // 后序访问： 需要子树传递回来的数据
   }
   ```

```c++
// 1. 回溯法   
	// 记录最大深度
    int res = 0;
    int depth = 0;
    int maxDepth(TreeNode* root) {
        traverse(root);
        return res;
    }
    // 二叉树遍历框架
    void traverse(TreeNode* root) {
        if (root == NULL) {
            res = max(res, depth);// 到达叶子节点
            return;
        }
        // 前序遍历位置
        depth++;
        traverse(root->left);
        traverse(root->right);
        // 后序遍历位置
        depth--;
    }
};
```

```c++
// 2. 动规法， 只是没有使用memo来存储重复结果！
// 定义：输入根节点，返回这棵二叉树的最大深度
int maxDepth(TreeNode* root) {
    if (root == nullptr) {
        return 0;
    }
    // 递归计算左右子树的最大深度
    int leftMax = maxDepth(root->left);
    int rightMax = maxDepth(root->right);
    // 整棵树的最大深度
    int res = max(leftMax, rightMax) + 1;

    return res;
}
```

3. 其他：BFS/层序遍历

```c++
int bfs(root) {
    queue<TreeNode*> q;  q.push(root);
    while (!q.empty()) {
        int sz = q.size();
        for (i = 0; i < sz; i++)  {
            curr = q.front(); q.pop();
            q.push(curr->left);
            q.push(curr->right);
        }
        depth++;
    }
}
```



## 动态规划

1. 求最值，核心是穷举所有解：无重复（），无遗漏
2. 三个要求
   1. 有正确的状态转移方程
   2. 具备最优子结构
   3. 存在重叠子问题：memo
3. 步骤：
   1. 找出所有状态
   2. 找出所有选择
   3. 列出base case
   4. 定义好dp 的定义，用自然语言描述

```python
def dp(state1, state2):
	for choice in choices:
        res = 最值(res, dp(s1, s2))
        
dp[0][0] = base
for s1 in s1s:
    for s2 in s2s:
        for ...
        	dp[s1][s2][..] = 最值（s1, s2）
```



## 回溯

```python
result = []
def backtrace(path, choices):
    if end:
        result.add(path)
        return
    for choice in choices:
        做选择
        backtrace(path, choices)
        撤销选择

```



## 二分查找

1. 默认都使用[left, right] 左右闭合区间
2. while (left <= right), 只有当left > right = right+1, [right+1, right] = 空，才结束判断！
3. 查左侧边界：
   1. if (nums[mid] == target) right = mid-1; 右区间左边缩； 
   2. if (left >= n) return -1; 注意判断索引越界情况。
   3. return (nums[left] == target) ? left : -1;  右边界不断左移，左边界不变，就直接返回



## 滑动窗口

1. 默认使用[left, right) ，保证[0, 0) 为空

   ```c++
   unordered_map<char, int> window;
   while(left < right) {
       // 右边扩大window
       window.add(s[right]); right++;
       // do sth
       
       printf("window:[%d, %d)\n", left, right);
       while (需要shrink) {
           // 终止条件
           // 左边缩小
           window.remove(s[left]); left++;
           // 数据更新
       }
   }
   ```

   
