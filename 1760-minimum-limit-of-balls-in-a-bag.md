# LeetCode 1760： 袋子里最少数目的球



### 题目描述

给定一个整数数组，数组中的元素表示袋子里的球数。同时给定一个整数maxOperations，表示可以至多做如下操作maxOperations次：

- 选择一个袋子，并将袋子中的球分到两个新的袋子中，每个袋子中都有正整数的球

开销是单个袋子里球数目的**最大值**，想要最小化开销，请返回进行上述操作后的**最小**开销。



**示例**

​	**输入**：nums = [2, 4, 8, 2], maxOperations = 4

​	**输出**：2

​		- [2, 4, **8**, 2] -> [2, 4, **4**, **4**, 2]

​		- [2, **4**, 4, 4, 2] ->  [2, **2, 2**, 4, 4, 2] 

​		- [2, 2, 2, **4**, 4, 2] -> [2, 2, 2, **2**, **2**, 4, 2]   

​		- [2, 2, 2, 2, 2, **4**, 2]   -> [2, 2, 2, 2, 2, 2, 2, 2]



**数据范围**

1  <= nums.length <= $10^5$

1 <= maxOperations, nums[i] <= $10^9$



### 分析

首先，看到示例的分析，当时想到了贪心+模拟，但是maxOperations最大到$10^9$，明显模拟会超时，并且如果模拟的话，需要考虑的情况比较多，比较复杂。

再重新看问题描述，简化一下其实就是“最大值的最小值问题”， 接着就能联想到Binary Search，进一步确认是否可以用二分法：

​	- 左右边界（解空间范围） ？

​	- 解空间是否单调？

​	- 如何检查满足条件？

只要把问题转化为，是否可以在操作数 <= maxOperations次时，得到的最小开销为x，这样，问题左右边界为1-10^9、单调，检查满足条件即为开销为x时。操作次数 <= maxOperations。

代码如下

```c++
class Solution {
public:
    int minimumSize(vector<int>& nums, int b) {
      	// bounary
        int l = 1, r = 1000000001;
        int ans = 0;
        while (l < r) {
            int mid = (l+r) >> 1;
            int oper = 0;
            for (auto num:nums) {
                if (num <= mid)
                    continue;
                if (num % mid == 0)
                    oper += num/mid-1;
                else
                    oper += num/mid;
            }
            // check 
            if (oper <= b) {
                ans = mid;
                r = mid;
            } else
                l = mid+1;
        }
        return ans;
    }
};
```



**复杂度**：

- 时间复杂度 *O*($nlogM$), M 为nums最大长度10^9，n为nums.length。
- 空间复杂度 *O*(1)。

 







