---
title: Leetcode-array-X数之和
key: 4
author: ZE
tags: leetcode array two-pointer C++
---
<!--more-->

## [1]两数之和
### 题目描述
> 给定一个整数数组 nums 和一个整数目标值 target，请你在该数组中找出和为目标值 target  的那 两个 整数，并返回它们的数组下标。
> 你可以假设每种输入只会对应一个答案。但是，数组中同一个元素在答案里不能重复出现。
> 你可以按任意顺序返回答案。

### 解题思路
遍历数组nums，用哈希表存储数组中出现过的数字。
对于每个数字，检查哈希表中是否存在与其和为target的数，如果存在，直接返回这两个数字的下标，否则将当前数字存入哈希表。

### 代码实现

~~~cpp
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        int len = nums.size();
        unordered_map<int, int> hashtable;
        for(int i =0;i<len;i++){
            auto it = hashtable.find(target-nums[i]); // 返回一个迭代器 unordered_map<int,int>::iterator
            if(it != hashtable.end()){ // 如果哈希表中有我们查询的数
                return {it->second, i}; // it->second表示取值，在这里是数组中元素的下标；it->first则表示取键，在这里是数组中元素的值。
            }
            hashtable[nums[i]] = i; // 注意键是数值，值是下标
        }
        return {}; // 返回空数组
    }
};
~~~

`Tips`{:.info}

**map与unordered_map的区别**

map的内部实现是一个红黑树，红黑树的每个节点代表了map中的一个元素。map内的元素是按照二叉搜索树存储的，是有序的，使用中序遍历可以将**键值**按从小到大的顺序遍历。map的查增删查操作都是对红黑树进行操作。

unordered_map的内部实现是一个哈希表，元素排列是无序的，查找的时间复杂度是O(1)。

内部实现不同，但外部使用几乎相同，e.g. size, count等操作。

## [15]三数之和
### 问题描述
>给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有和为 0 且不重复的三元组。
>注意：答案中不可以包含重复的三元组。

### 解题思路
**排序+双指针**

首先对数组nums进行排序，然后遍历数组，获取第一个数，剩下的两个数用双指针选择。具体来说，第一个数从左往右遍历，将第一个数（例如下标为i)的右侧看成一个新数组(从下标i+1开始)，两个指针初始时分别指向这个新数组的两端，若此时三数之和大于0，则向左移动右端的指针；若小于0，则向右移动左端的指针；若等于0，则返回此时的和……直到两个指针重合，继续第一个数的遍历。

需要注意对重复的数的处理。

### 代码实现
~~~cpp
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        int len = nums.size();
        sort(nums.begin(), nums.end());
        vector<vector<int>> res;
        for(int i = 0;i<len;i++){  // 遍历第一个数
            if(nums[i]>0) // 最小的数大于0，后面所有的数都大于0，不存在和为0的三个数
                return res;
            if(i>0 && nums[i]==nums[i-1]) // 跳过重复的第一个数
                continue;
            // 转化为两数之和问题,target=-nums[i]
            int second = i+1; 
            int third = len-1;
            while(second<third){
                int sum = nums[i]+nums[second]+nums[third];
                if(sum==0){
                    res.push_back({nums[i],nums[second++],nums[third--]});
                    while(second < third && nums[second]==nums[second-1])
                        second++; // 跳过重复的第二个数
                    while(second < third && nums[third]==nums[third+1])
                        third--; // 跳过重复的第三个数
                }
                else if(sum<0)
                    second++;
                else
                    third--;
            }
        }
        return res;
    }
};
~~~

## [16]最接近的三数之和
### 问题描述
>给你一个长度为 n 的整数数组 nums 和 一个目标值 target。请你从 nums 中选出三个整数，使它们的和与 target 最接近。
>返回这三个数的和。
>假定每组输入只存在恰好一个解。

### 解题思路
思路与三数之和类似，只是判定解的条件不同，需要计算一下target和sum之间的值，取差值最小的sum。

注意返回的是sum不是插值！

### 代码实现
~~~cpp
class Solution {
public:
    int threeSumClosest(vector<int>& nums, int target) {
        int len = nums.size();
        sort(nums.begin(),nums.end());
        int res = 1e7; // 将三数之和初始化为很大的数，或者用不同的变量来表示三数之和跟差值
        for(int i=0;i<len;i++){
            int second = i+1;
            int third = len-1;
            while(second<third){
                int sum = nums[i]+nums[second]+nums[third];
                if(sum==target)
                    return sum;
                else if(sum<target)
                    second++;
                else
                    third--;
                res = abs(target-sum)<abs(target-res)?sum:res; // 判定解的条件
            }
        }
        return res;
    }
};
~~~

## [18]四数之和
### 问题描述
>给你一个由 n 个整数组成的数组 nums ，和一个目标值 target 。请你找出并返回满足下述全部条件且不重复的四元组 [nums[a], nums[b], nums[c], nums[d]] （若两个四元组元素一一对应，则认为两个四元组重复）
> 你可以按 任意顺序 返回答案 。

### 解题思路
**思路一：排序+双指针**

思路跟三数之和类似，只是比三数之和多了一层循环。

使用两层循环遍历前两个数，用双指针选择后两个数。

**思路二：DFS回溯**

[题解](https://leetcode.cn/problems/4sum/solution/mei-kan-dao-ji-ge-hui-su-de-ti-jie-na-wo-lai-xie-y/)

### 代码实现
排序+双指针

~~~cpp
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        int len = nums.size();
        sort(nums.begin(),nums.end());
        vector<vector<int>> res;
        for(int i=0;i<len-3;i++){
            if(i>0 && nums[i]==nums[i-1]) // 跳过重复的第一个数
                continue;
            for(int j=i+1;j<len-2;j++){
                if(j>i+1 && nums[j]==nums[j-1]) // 跳过重复的第二个数
                    continue;
                int new_target = target-nums[i]-nums[j]; // 不直接求四数之和，避免溢出
                int left = j+1, right = len-1;
                while(left<right){
                    int sum = nums[left]+nums[right];
                    if(sum==new_target){
                        res.push_back({nums[i],nums[j],nums[left++],nums[right--]});
                        while(left<right && nums[left]==nums[left-1]) 
                            left++; // 跳过重复的第三个数
                        while(left<right && nums[right]==nums[right+1])
                            right--; // 跳过重复的第四个数
                    }else if(sum<new_target)
                        left++;
                    else
                        right--;
                }
            }
        }
        return res;
    }
};
~~~

## 相关题目
[[11]盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)