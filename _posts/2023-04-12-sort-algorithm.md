# 排序算法
- [排序算法](#排序算法)
  - [快速排序](#快速排序)
  - [冒泡排序](#冒泡排序)
  - [选择排序](#选择排序)
  - [堆排序](#堆排序)
  - [插入排序](#插入排序)
  - [希尔排序](#希尔排序)
  - [归并排序](#归并排序)
## 快速排序
不稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(nlogn)  | 0(n^2) | O(nlogn)|

当数组本身有序，且我们每次都从最左边或最右边选择基准时，时间复杂度O(n^2).为了避免出现这种情况，可以随机选取基准。
```cpp
void QuickSort(vector<int>& nums, int l, int r){
    if(l>=r) return;

    // select a random base
    int rnd = l+rand()%(r-l+1);
    int tmp = nums[l];
    nums[l] = nums[rnd];
    nums[rnd] = tmp;

    int base = nums[l], i=l,j=r;
    while(i<j){
        while(i<j && nums[j]>=base){
            j--;
        }
        while(i<j &&　nums[i]<=base){
            i++;
        }
        tmp = nums[i];
        nums[i] =nums[j];
        nums[j] = tmp;
    }

    nums[l] = nums[i];
    nums[i] = base;
    QuickSort(nums, l, i-1);
    QuickSort(nums, j+1, r);
}
```
## 冒泡排序
稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(n)  | 0(n^2) | 0(n^2) |

如果一次遍历中没有发生过交换，说明数组已经有序，直接结束。数组已经有序的情况下达到最好时间复杂度O(n)。

```cpp
vector<int> BubldeSort(vector<int>& nums){
    int n = nums.size();
    bool flag = false;
    for(int i=0;i<n;i++){
        for(int j=0;j<n-i;j++){
            if(nums[j]>nums[j+1]){
                int tmp=nums[j];
                nums[j] =nums[j+1];
                nums[j+1] = tmp;
                flag=true;
            }
        }
        if(!flag) return nums;
    }
    return nums;
}
```
## 选择排序
不稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(n^2)  | 0(n^2) | 0(n^2) |

在待排序的数组中选择最大/最小的数，添加到已排序的末尾

```cpp
vector<int> SelectSort(vector<int>& nums){
    int n=nums.size(), minIndex=0;
    for(int i=0;i<n-1;i++){
        minIndex = i;
        for(int j =i+1;i<n;j++){
            if(nums[j]<nums[minIndex]){
                minIndex = j;
            }
        }
        if(minIndex==i) continue;
        int tmp = nums[i];
        nums[i] = nums[minIndex];
        nums[minIndex] = tmp;
    }
    return nums;
}
```
## 堆排序
不稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(nlogn)  | 0(nlogn) | 0(nlogn) |

维护一个大根堆，每次交换堆顶节点和最后一个节点，此时最后一个节点是最大的元素，调整大根堆，得到第二大的数，以此类推。
```cpp
void buildHeap(vector<int>& nums){
    int n= nums.size();
    if(n<2) return;
    for(int i = n/2-1;i>=0;i--){ // 从最后一个非叶子节点开始向前遍历
        adjust(nums, i, n);
    }
}

void adjust(vector<int>& nums, int i, int n){
    int left = 2*i+1, right=2*i+2, largest = i;
    if(left<n && nums[left]>nums[largest]) largest = left;
    if(right<n && nums[right]>nums[largest]) largest = right;
    if(largest==i) return;
    int tmp = nums[i];
    nums[i] = nums[largest];
    nums[largest] = tmp;
    adjust(nums, largest, n);
}

void HeapSort(vector<int>& nums){
    int n = nums.size();
    buildHead(nums);
    for(int i = n-1;i>0;i--){
        int tmp = nums[i];
        nums[i] = nums[0];
        nums[0] = tmp;
        adjust(nums, 0, i);
    }

}
```
## 插入排序
稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(n)  | 0(n^2) | 0(n^2) |
```cpp
vector<int> InsertSort(vector<int>& nums){
    int n=nums.size();
    if(n<2) return nums;
    for(int i=1;i<n;i++){
        int j=i, tmp=nums[i];
        for(j;j>0;j--){
            if(nums[j-1]>tmp){
                nums[j] = nums[j-1];
            }else{
                break;
            }
        }
        nums[j] = tmp;
    }
    return nums;
}
```

## 希尔排序
不稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(n)  | 0(n^2) | 0(n^1.3) |

可以看成分组的插入排序

```cpp
vector<int> ShellSort(vector<int>& nums){
    int n=nums.size();
    for(int gap = n/2;gap>0;gap/=2){
        for(int i=gap;i<n;i++){
            int tmp = nums[i];
            int j = i
            for(j;j-gap>=0;j-=gap){
                if(nums[j-gap]>tmp){
                    nums[j] = nums[j-gap];
                }else{
                    break;
                }
            }
            nums[j] = tmp;
        }       
    }
    return nums;
}
```
## 归并排序
稳定
|  最好   | 最坏  | 平均 |
|  ----  | ----  |----|
| O(nlogn)  | 0(nlogn) | 0(nlogn) |
```cpp
vector<int> merge(vector<int>& vec1, vector<int>& vec2){
    vector<int> res;
    int i=0,j=0, n1=vec1.size(), n2=vec2.size();
    while(i<n1 && j<n2){
        if(vec1[i]<vec2[j]){
            res.push_back(vec1[i++]);
        }else{
            res.push_back(vec2[j++]);
        }
    }
    while(i<n1){
        res.push_back(vec1[i++]);
    }
    while(j<n2){
        res.push_back(vec2[j++]);
    }
    return res;
}

vector<int> MergeSort(vector<int>& nums){
    int n=nums.size();
    if(n<2){
        return nums;
    }
    vector<int> vec1 = MergeSort(vector<int>(nums.begin(),nums.begin()+n/2));
    vector<int> vec2 = MergeSort(vector<int>(nums.begin()+n/2, nums.end()));
    return merge(vec1,vec2);
}
```