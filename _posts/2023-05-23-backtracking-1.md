---
title: 回溯算法-排列组合问题
key: 13
author: ZE
tags: algorithm leetcode
---
如何用回溯算法解决数组的排列/组合问题

<!--more-->

## 组合问题
组合问题是给定一组元素和可选的元素数，找到不同的选取方式。例如给定[1,2,3]，从中选取两个元素，不同的选取方式有[1,2] [1,3] [2,3]

### <a id="subtitle1">元素无重复且不可复选</a>
元素无重复且不可复选是排列组合问题最基础的情况，不需要考虑去重

[Leetcode 77.组合 Mid](https://leetcode.cn/problems/combinations/submissions/)

![Q2](/assets/images/post13-q2.jpg)


```cpp
vector<vector<int>> combine(int n, int k) {
    vector<vector<int>> res;
    vector<int> cur;
    backtrack(n, k, 1, res, cur);
    return res;
}
void backtrack(int n, int k, int index, vector<vector<int>>& res, vector<int>& cur){
    if(cur.size()==k){
        res.emplace_back(cur);
        return ;
    }
    for(int i=index;i<=n;++i){
        cur.emplace_back(i); // 选择
        backtrack(n, k, i+1, res, cur); // 已经选过的元素不能再选，所以i+1
        cur.pop_back(); // 撤销
    }
}

```

### 元素重复不可复选
在元素可能重复的情况下，解决排列组合问题主要的思路跟[元素无重复且不可复选](#subtitle1)类似，关键在于如何处理重复元素，即如何**去重**。

去重的方法也很简单：先对数组进行排序，使得相同的元素挨在一起，遍历时遇到和前一个元素相同的元素直接跳过。注意，去重是对同一个位置上相同的元素去重，不同位置上的元素值可以相同。例如[1,2,2,3]大小为3的组合有[1,2,3],[1,2,2],[2,2,3]，第二个2用2’来表示的话，去重操作是去除了[1,2,3]和[1,2',3]，[1,2,2']和[1,2',2]，以及[2,2',3]和[2',2,3]的重复。如果用树的形式来表示的话，去重去除的是同一层选择里值相同的元素。

[Leetcode 40. 组合总和II Mid](https://leetcode.cn/problems/combination-sum-ii/)

![Q3](/assets/images/post13-q3.jpg)

```cpp
vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
    sort(candidates.begin(),candidates.end()); // 去重+剪枝
    vector<vector<int>> res;
    vector<int> cur;
    int sum=0;
    backtrack(candidates, 0, sum, target, res,cur);
    return res;
}

void backtrack(vector<int>& nums, int index, int sum, int target, vector<vector<int>>& res, vector<int>& cur){
    if(sum==target){
        res.emplace_back(cur);
        return ;
    }
    if(sum>target){
        return ;
    }
    for(int i=index;i<nums.size();++i){
        if(i>index && nums[i]==nums[i-1]){ // 去重
            continue ;
        }
        sum+=nums[i];
        cur.emplace_back(nums[i]);
        bakctrack(nums,i+1,sum,target,res,cur);
        cur.pop_back();
        sum-=nums[i];
    }
}

```
### 元素无重复可复选

[Leetcode 39. 组合总和 Mid](https://leetcode.cn/problems/combination-sum/)

![Q4](/assets/images/post13-q4.jpg)

```cpp
vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
    sort(candidates.begin(), candidates.end()); // 排序是为了剪枝：总和超过target直接返回
    vector<vector<int>> res;
    vector<int> cur;
    int sum=0;
    backtrack(candidates, 0, sum, target, res, cur);
    return res;
}

void backtrack(vector<int>& candidates, int index, int sum, int target, vector<vector<int>>& res, vector<int>& cur){
    if(sum==target){
        res.emplace_back(cur);
        return;
    }
    if(sum>target){
        return ; // 剪枝
    }
    for(int i=index;i<candidates.size();++i){
        sum += candidates[i];
        cur.emplace_back(candidates[i]);
        backtrack(candidates,i,sum,target,res,cur); // 因为可重复选，所以这里index=i，这样下一个元素还可以选当前元素。
        cur.pop_back();
        sum-=candidates[i];
    }
}

```

## 子集
子集问题是组合问题的扩展，一个集合S的所有子集就是S中大小为0~S.size()的所有组合。

### 元素无重复且不可复选
[Leetcode 78.子集 Mid](https://leetcode.cn/problems/subsets/submissions/)

![Q1](/assets/images/post13-q1.jpg)

```cpp
vector<vector<int>> subsets(vector<int>& nums) {
    vector<vector<int>> res;
    vector<int> cur;
    backtrack(nums, 0, res, cur);
    return res;
}

void backtrack(vector<int>& nums, int index, vector<vector<int>>& res, vector<int>& cur){
    res.emplace_back(cur); // 不需要判断cur的大小，所有cur都是子集

    for(int i=index;i<nums.size();++i){
        cur.emplace_back(nums[i]); // 选择
        backtrack(nums, i+1, res, cur); // 已经选过的元素不能再选，所以i+1
        cur.pop_back(); // 撤销
    }
}

```
### 元素重复不可复选
[Leetcode 90. 子集II](https://leetcode.cn/problems/subsets-ii/)

![Q5](/assets/images/post13-q5.jpg)

集合中应该不包含重复的元素，但按照这个题目给出的示例来看，允许一个子集里包含重复元素。思路跟上述组合问题一样，且不需要额外判断元素和。

```cpp
vector<vector<int>> subsetsWithDup(vector<int>& nums) {
    sort(nums.begin(),nums.end());
    vector<vector<int>> res;
    vector<int> cur;
    backtrack(nums, 0, res, cur);
    return res;
}

void backtrack(vector<int>& nums, int index, vector<vector<int>>& res ,vector<int>& cur){
    res.emplace_back(cur);

    for(int i=index;i<nums.size();++i){
        if(i>index && nums[i]==nums[i-1]){
            continue;
        }
        cur.emplace_back(nums[i]);
        backtrack(nums,i+1,res,cur);
        cur.pop_back();
    }
}
```
## 排列
排列问题是给定所有元素，找到这些元素顺序不同的排列。例如给定[1,2,3]，它的不同排列有[1,2,3] [1,3,2] [2,1,3] [2,3,1] [3,1,2] [3,2,1]。

排列与组合的区别在于，排列中前一层选过元素这一层还可以作为候选，只要同一个排列中不重复选择即可，例如第一层有1，2，3候选元素，在第二层中对于1来说，可以选2和3，对于2来说，依然可以选1和3，（1，2）和（2，1）是不重复的。但在组合问题中（1，2）和（2，1）是重复的。所以在组合问题中，回溯算法是从当前已选的元素往后遍历的，而排列问题中每次都是从头遍历，然后利用visited数组排除访问过的元素。

### 元素无重复且不可复选
[Leetcode 46. 全排列](https://leetcode.cn/problems/permutations/submissions/)

![Q6](/assets/images/post13-q6.jpg)

```cpp
 vector<vector<int>> permute(vector<int>& nums) {
    int n=nums.size();
    vector<vector<int>> res;
    vector<int> cur;
    vector<bool> visited(n,false);
    backtrack(nums,visited,res,cur);
    return res;
 }

 void backtrack(vector<int>& nums, vector<bool>& visited, vector<vector<int>>& res, vector<int>& cur){
    if(cur.size()==nums.size()){ //找到一个排列
        res.emplace_back(cur);
        return ;
    }

    for(int i=0;i<nums.size();++i){
        if(visited[i]){
            continue;
        }
        visited[i] = true;
        cur.emplace_back(nums[i]);
        bakctrack(nums, visited,res, cur);
        cur.pop_back();
        visited[i] = false;
    }
 }
```

### 元素重复不可复选
[Leetcode 47. 全排列II](https://leetcode.cn/problems/permutations-ii/)

![Q7](/assets/images/post13-q7.jpg)

需要固定相同元素在排列中的顺序，对结果去重

```cpp
vector<vector<int>> permuteUnique(vector<int>& nums) {
    sort(nums.begin(),nums.end());
    vector<vector<int>> res;
    vector<int> cur;
    vector<bool> visited(nums.size(),false);
    backtrack(nums, visited,res,cur);
    return res;
}

void backtrack(vector<int>& nums,vector<bool>& visited, vector<vector<int>>& res, vector<int>& cur){
    if(cur.size()==nums.size()){
        res.emplace_back(cur);
        return;
    }

    for(int i=0;i<nums.size();++i){
        if(visited[i] || i>0 && nums[i]==nums[i-1] && !visited[i-1]){
            continue;
        }
        visited[i]=true;
        cur.emplace_back(nums[i]);
        backtrack(nums,visited,res,cur);
        cur.pop_back();
        visited[i] = false;
    }
}
```
## 总结
用回溯算法解决排列组合问题的总体框架是一样的。

```cpp
vector<vector<int>> res; //也可以以参数的方式传入到函数中
vector<int> cur;
// int sum=0;

vector<vector<int>> solution(vector<int>& nums,...){
    // 元素有重复时需要排序，无重但需要求和时也需要排序（用于剪枝）
    // sort(...)
    backtrack(...)
}

void backtrack(vector<int>& nums,[int index,vector<bool>& visited],...){
    if(...){
        // 子集问题加入res条件：无
        // 组合问题加入res条件：cur.size()==k （根据题目，可变换）
        // 排列问题加入res条件：cur.size()==nusm.size()
        res.emplace_back(cur);
        return ;
    }

    // 组合/子集问题从index往后遍历
    // 排列问题不需要index,每次都从头遍历，然后用visited防止重复访问同一个元素
    for(int i=index;i<nums.size();++i){
        // 元素可重复时用于去重
        // if(i>index && nums[i]==nums[i-1]){ 
        //      continue;
        // }

        // 排列问题防止重复访问
        // if(visited[i]) continue;

        // visited[i] = true;
        cur.emplace_back(nums[i]); // 选择
        // 元素可复选
        // backtrack(nums,i,...);
        // 元素不可复选 
        backtrack(nums,i+1,...);  
        cur.pop_back() // 撤销
        // visited[i] = false;
    }
}
```
## 参考连接

https://labuladong.gitee.io/algo/di-ling-zh-bfe1b/hui-su-sua-56e11/