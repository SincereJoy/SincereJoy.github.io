---
title: 贪心算法
key: 6
author: ZE
tags: algorithm C++ leetcode
---
贪心算法是动态规划的一种特殊情况，需要满足”局部最优选择会导向全局最优解“的条件。
这里主要总结了区间相关的问题（例如合并区间，区间去重，安排会议室等）。
<!--more-->

## 重叠区间
重叠区间相关的题目一般会给出一个区间列表（例如，[[1,2],[2,3],[3,4],[1,3]])，要求删除被其它区间包含的区间，或者找出不重叠的区间。以下是三个比较有代表性的重叠区间题目。

### [Leetcode 435. 无重叠区间 Mid](https://leetcode.cn/problems/non-overlapping-intervals/)
> 给定一个区间的集合 intervals ，其中 intervals[i] = [starti, endi] 。返回 需要移除区间的最小数量，使剩余区间互不重叠 。
> 
> 示例：
> 输入: intervals = [[1,2],[2,3],[3,4],[1,3]]
> 
> 输出: 1
> 
> 解释: 移除 [1,3] 后，剩下的区间没有重叠。
> 
**思路**：
1. 将区间列表按照右边界(endi)从小到大排序；
2. 取最小的右边界（curEnd），依次检查后面的区间是否跟它重叠（check if starti < curEnd），若重叠，则计数count++；
3. 当遇到第一个不重叠的区间时，更新curEnd，重复第二步，直到遍历完所有区间。

**代码**
```cpp

```

### [Leetcode 452. 引爆气球 Mid](https://leetcode.cn/problems/minimum-number-of-arrows-to-burst-balloons/)
>有一些球形气球贴在一堵用 XY 平面表示的墙面上。墙面上的气球记录在整数数组 points ，其中points[i] = [xstart, xend] 表示水平直径在 xstart 和 xend之间的气球。你不知道气球的确切 y 坐标。
>
>一支弓箭可以沿着 x 轴从不同点 完全垂直 地射出。在坐标 x 处射出一支箭，若有一个气球的直径的开始和结束坐标为 xstart，xend， 且满足  xstart ≤ x ≤ xend，则该气球会被 引爆 。可以射出的弓箭的数量 没有限制 。 弓箭一旦被射出之后，可以无限地前进。
>
>给你一个数组 points ，返回引爆所有气球所必须射出的 最小 弓箭数 。

这个题目本质上跟[无重复区间](https://leetcode.cn/problems/non-overlapping-intervals/)一样，只不过上个题求解的是把当前列表变成无重复区间列表需要删除的区间数，而打气球是求无重复区间列表包含的区间数。

**代码**
```cpp

```

### [Leetcode 1288. 删除被覆盖区间 Mid](https://leetcode.cn/problems/remove-covered-intervals/)
> 给你一个区间列表，请你删除列表中被其他区间所覆盖的区间。
>
>只有当 c <= a 且 b <= d 时，我们才认为区间 [a,b) 被区间 [c,d) 覆盖。
>
>在完成所有删除操作后，请你返回列表中剩余区间的数目。

注意覆盖区间与重叠区间的判定方式不同，覆盖区间是指start1 <= start2 且 end2 <= end1，而重叠区间只需要判定start2 < end1。

**思路**
1. 先根据左边界升序排序，左边界相等的区间按照右边界降序排序；
2. 以第一个区间为基准，遍历区间列表，遇到左边界相等的区间直接跳过（肯定覆盖），遇到左边界不相等的，判断右边界是否满足包含条件，满足则继续，不满足则计数+1，将当前区间设为新的基准，继续遍历。

**代码**
```cpp

```
## 合并区间
合并区间问题跟重叠区间的思路差不多，不过重叠区间只要求返回区间的个数，而合并区间问题会要求返回处理后的区间列表。
### [Leetcode 56. 合并区间 Mid](https://leetcode.cn/problems/merge-intervals/)
>以数组 intervals 表示若干个区间的集合，其中单个区间为 intervals[i] = [starti, endi] 。请你合并所有重叠的区间，并返回 一个不重叠的区间数组，该数组需恰好覆盖输入中的所有区间。

**思路**：
跟无重复区间问题的思路一样，为了合并重叠区间，在遍历过程中需要记录重叠区间的最大右边界，作为合并后区间的右边界。

1. 按左边界升序排序，左边界相等的按右边界降序排序；
2. 以第一个区间为基准，遍历列表，检查区间是否重叠（starti <= curEnd，注意这里取等号，因为首尾相接的区间也可以合并 )，并记录最大右边界；
3. 遇到第一个不重叠的区间时，用当前基准的左边界和记录的最大右边界组成合并区间加入到结果列表里，然后将当前区间设为新的基准，继续遍历。

**代码**
```cpp

```
### [Leetcode 1024. 视频拼接 mid](https://leetcode.cn/problems/video-stitching/)
> 你将会获得一系列视频片段，这些片段来自于一项持续时长为 time 秒的体育赛事。这些片段可能有所重叠，也可能长度不一。
>
>使用数组 clips 描述所有的视频片段，其中 clips[i] = [starti, endi] 表示：某个视频片段开始于 starti 并于 endi 结束。
>
>甚至可以对这些片段自由地再剪辑：
>
>例如，片段 [0, 7] 可以剪切成 [0, 1] + [1, 3] + [3, 7] 三部分。
>我们需要将这些片段进行再剪辑，并将剪辑后的内容拼接成覆盖整个运动过程的片段（[0, time]）。返回所需片段的最小数目，如果无法完成该任务，则返回 -1 。

实际上也是合并区间问题，不过需要找到所需的**最少片段数**，利用贪心的思想，每次都选择重叠片段中结束时间最晚的视频片段进行拼接，以达到最少片段的要求。

**思路**：
1. 按左边界升序排序，左边界相等的按右边界降序排序；
2. 若第一个区间不是从0开始，直接返回-1；
3. 以第一个区间为基准，遍历列表，遇到左边界相等的片段直接跳过，遇到左边界不相等的，判断是否重叠（starti <= curEnd )，记录重叠片段的最大右边界，直到找到第一个不与基准区间重叠的区间；
4. 将当前最大右边界作为新的基准（curEnd)，拼接片段计数+1，重复第三步，直到当前的最大右边界超过目标片段的右边界。

**代码**
```cpp

```

### [Leetcode 986.区间列表的交集 Hard](https://leetcode.cn/problems/interval-list-intersections/)
>给定两个由一些**闭区间**组成的列表，firstList 和 secondList，其中 firstList[i] = [starti, endi] 而 secondList[j] = [startj, endj] 。每个区间列表都是成对**不相交**的，并且**已经排序**。
>
>返回这两个区间列表的交集 。
>
>形式上，闭区间 [a, b]（其中 a <= b）表示实数 x 的集合，而 a <= x <= b 。
>
>两个闭区间的 交集 是一组实数，要么为空集，要么为闭区间。例如，[1, 3] 和 [2, 4] 的交集为 [2, 3] 。

该问题是[合并区间](https://leetcode.cn/problems/merge-intervals/)的扩展，需要合并两个区间列表。

**思路：**
大体思路跟合并单个区间列表一样，需要用**双指针**处理双列表。题目给出的列表已经排序，所以不需要再进行排序操作。
1. 两个指针p1,p2分别指向两个列表的开头，对指针指向的区间进行合并，左边界取二者之中较大的，右边界取二者之中较小的，组成新区间加入到结果列表中；
2. 判断双指针的移动：对于刚才合并的两个区间，如果secondList的右边界较大，说明这个区间可能会跟firstList中的下一个区间继续相交，所以p2不变，p1后移；
3. 重复上述步骤，直到遍历完其中一个列表。

**代码**
```cpp

```
## 会议室问题

### [会议室](https://www.nowcoder.com/questionTerminal/5378da648df74047885ae1a94e2e4969)
> 一些项目要占用一个会议室宣讲，会议室不能同时容纳两个项目的宣讲。 给你每一个项目开始的时间和结束的时间(给你一个数 组，里面是一个个具体 的项目)，你来安排宣讲的日程，要求会议室进行的宣讲的场次最多。 返回这个最多的宣讲场次。

跟无重复区间问题一样

**代码**
```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    int n;
    cin>>n;
    vector<vector<int>> times(n,vector<int>(2));
    for(int i=0;i<n;++i){
        cin>>times[i][0]>>times[i][1];
    }
    sort(times.begin(), times.end(), [](vector<int>& a,vector<int>& b){
        if(a[1]==b[1]) return a[0]<b[0]; //这里需要对右边界相同的区间按左边界升序排序，不然无法通过所有测试用例
        return a[1]<b[1];
    });
    int end = times[0][1], count=1;
    for(int i=1;i<n;++i){
        if(times[i][0]<end){
            continue;
        }
        count++;
        end=times[i][1];
    }
    cout<<count<<endl;
}
```
### [会议室II](https://leetcode.cn/problems/meeting-rooms-ii/)
> 给定一个会议时间安排的数组，每个会议时间都会包括开始和结束的时间 [[s1,e1],[s2,e2],…] (si < ei)，为避免会议冲突，同时要考虑充分利用会议室资源，请你计算至少需要多少间会议室，才能满足这些会议安排。

如果有两个会议的时间重叠，就需要多开一间会议室，本质上还是重叠区间问题，不过需要记录多个会议室的空闲时间。

**思路1**：
最直接的思路是维护一个优先队列，保存各个会议室的会议结束时间，按照查找重叠区间的方法遍历会议时间列表，当遇到重叠区间时就新开一间会议室，加入到优先队列中，如果当前会议开始时间不小于队头会议室的会议结束时间，就更新这个会议室的结束时间。

**代码**
```cpp
class Solution {
   
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        sort(intervals.begin(),intervals.end(),[](vector<int>& a,vector<int>& b){
            return a[1]<b[1];
        });
        priority_queue<int> pq;
        for(int i=0;i<intervals.size();++i){
            if(pq.empty()){
                pq.push(intervals[i][1]);
                continue;
            }else if(intervals[i][0]>=pq.top()){
                pq.pop();
            }
            pq.push(intervals[i][1]);
        }
        return pq.size();
    }

```

**思路2**：
更巧妙的方法是**扫描线解法**
1. 把开会时间和会议结束时间分别存到两个列表中，并按升序排序；
2. 从最早的开会时间开始往后扫描，每当遇到一个开会时间点，会议室数+1，每当遇到一个结束时间点，会议室数-1，直到扫描完成，记录整个过程中最大的会议室数。

**代码**
```cpp
class Solution {
   
public:
    int minMeetingRooms(vector<vector<int>>& intervals) {
        vector<int> starts, ends;
        for(auto& i:intervals){
            starts.emplace_back(i[0]);
            ends.emplace_back(i[1]);
        }
        sort(starts.begin(),starts.end());
        sort(ends.begin(),ends.end());
        int i=0,j=0, count=0, res=0, n=intervals.size();
        while(i<n && j<n){
            if(begin[i]<end[j]){ //经过一个开会时间点
                count++;
                i++;
            }else{  //经过一个结束时间点
                count--;
                j++;
            }
            res = max(res, count); //记录最大的会议室数
        }
        return res;

    }
```


