# Leetcode 995. K连续位的最小翻转次数

### 题目描述

在仅包含 0 和 1 的数组 A 中，一次 K 位翻转包括选择一个长度为 K 的（连续）子数组，同时将子数组中的每个 0 更改为 1，而每个 1 更改为 0。

返回所需的 K 位翻转的最小次数，以便数组没有值为 0 的元素。如果不可能，返回 -1。

**示例1**

  	输入：A = [0, 1, 0], K = 1
    输出：2
    解释：先翻转A[0]，再翻转A[2]
    
**示例2**

  	输入：A = [0, 1, 1], K = 2
    输出：-1
    解释：无论怎么翻转，都不能使得数组变为[1,1,1]

**范围**
  1. 1 <= A.length <= 30000
  2. 1 <= K <= A.length

### 分析
如何满足**最小**翻转次数？对于同一个长度为K的子数组，最多执行一次翻转操作。
由于先后顺序不影响最后翻转结果，且当前区间的翻转，不会影响前面的元素。所以可以用到<a href = "https://github.com/jianglq6/leetcode/blob/master/classification/Greedy.md" target="_blank"> 贪心的思想 <a>。
从左到右翻转。

最直接的想法是模拟：从前到后遍历，遇到为0的数字，翻转以该数字起始的K个数字。
```c++
class Solution {
public:
    int minKBitFlips(vector<int>& A, int K) {
        int n = A.size(), ans = 0;        
        for (int i = 0; i <= n - K; i++) {
            if(A[i] == 0) {
                for (int j = i; j < i+K; j++)
                    A[j] = A[j] == 1?0:1;
                ans++;
            }
        }
        
        for (int i = n-K; i < n; i++)
            if (A[i] == 0)
                return -1;
        return ans;
    }
};
```
- 时间复杂度 *O*(n*K)，
- 空间复杂度 *O*(1)。
由于A.length最大到30000，会超时（实际上92/110）。对于这样大小的数据量，希望可以在*O*(n) ~ *O*(nlogn)

由于真实的进行了翻转，使得计算量增大。由于，当前位置i的翻转状态，是[i-K+1, i-1]元素翻转次数的奇偶性有关，且当前元素在下面两种情况下需要翻转：
- i位置被翻转了偶数次，且A[i] = 0；
- i位置被翻转了奇数次，且A[i] = 1;
我们可以利用一个队列来模拟滑动窗口，这个队列的用处是存储从[i-K+1, i-1]区间中，进行翻转了的元素的下标。则由于以上两点分析，当len(que)%2 == A[i]时，当前元素需要翻转，且如果 i + K > n时，说明剩余的元素不到K个，则无法翻转， 返回-1。
```c++
class Solution {
public:
    int minKBitFlips(vector<int>& A, int K) {
        int n = A.size(), ans = 0;
        deque<int> dq;
        for (int i = 0; i < n; i++) {
            while(!dq.empty() && i-dq.front() >= K)
                dq.pop_front();
            if (dq.size()%2 == A[i]) {
                if (i+K > n) return -1;
                ans++;
                dq.push_back(i);
            }
        }
        return ans;    
    }
};
```
- 时间复杂度 *O*(n)，
- 空间复杂度 *O*(K)。
