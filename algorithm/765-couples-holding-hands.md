# LetCode 765. 情侣牵手 

## 题目描述

N 对情侣坐在连续排列的 2N 个座位上，想要牵到对方的手。 计算最少交换座位的次数，以便每对情侣可以并肩坐在一起。 一次交换可选择任意两人，让他们站起来交换座位。

人和座位用 0 到 2N-1 的整数表示，情侣们按顺序编号，第一对是 (0, 1)，第二对是 (2, 3)，以此类推，最后一对是 (2N-2, 2N-1)。

这些情侣的初始座位  row[i] 是由最初始坐在第 i 个座位上的人决定的。



**示例**

​	 **输入**： row = [0, 2, 1, 3, 4, 7, 6, 8, 5, 10, 9, 11]

​	 **输出**：4

​	 **解释**：2 -> 1, 7 -> 5, 7 -> 8, 9 -> 10



**说明**

	1. len(row)是偶数且数值在[4, 60] 范围。
 	2. row是序列0 ... len(row)-1的一个全排列。
 	3. 难度： hard



## 分析

**方法一**： Greedy + HashTable 贪心+哈希表

固定偶数位置的人，将ta的情侣换到ta相应的奇数位置上

```c++
class Solution {
public:
    int minSwapsCouples(vector<int>& row) {
        int ans = 0;
        vector <int> mp(row.size(), 0);
        for (int i = 0; i < row.size(); i++) 
            mp[row[i]] = i;
        for (int i = 0; i < row.size(); i += 2) {
            int l = row[i], r;
            if (l%2 == 0) r = l+1;
            else r = l-1;
            if (row[i+1] == r)
                continue;
            else {
                int tmp = row[i+1];
                swap(row[mp[r]], row[i+1]);
                swap(mp[tmp], mp[r]);
                ans++;
            } 
        }
        return ans;
    }
};
```

****

**复杂度**

​	时间复杂度 *O*(n), 空间复杂度*O*(n)。



**方法二**：Disjoint-set 并查集

将row[i]/2看作节点，连接偶数位置和相应奇数位置的节点，组成闭环的边的数量-1即为所需要交换的次数

```c++
class Solution {
public:
    int find(vector<int> &fa, int x) {
        return fa[x] == x? x: find(fa, fa[x]);
    }
    void Union(vector<int> &fa, int x, int y) {
        x = find(fa, x);
        y = find(fa, y);
        fa[y] = x;
    }
    int minSwapsCouples(vector<int>& row) {
        int n = row.size();
        int node = n/2;
        vector<int> fa(node, 0);
        for (int i = 0; i < node; i++)
            fa[i] = i;
        for (int i = 0; i < n; i += 2) {
            int l = row[i]/2;
            int r = row[i+1]/2;
            Union(fa, l, r);
        }
        vector<int> mp(node, 0); // 已经形成闭环的，用一个node来代表，相应闭环边的大小
        for (int i = 0; i < node; i++) {
            mp[find(fa, i)]++;
        }
        int ret = 0;
        for (auto sz: mp) {
            if (sz > 0)
                ret += sz-1;
        }
        return ret;
    }
};
```

**复杂度**

​	时间复杂度 *O*(n), 空间复杂度*O*(n)。



进一步优化空间，闭环的边的数量，就等于并查以后，节点的祖先不是自己的数量。

```c++
class Solution {
public:
    int find(vector<int> &fa, int x) {
        return fa[x] == x? x: find(fa, fa[x]);
    }
    void Union(vector<int> &fa, int x, int y) {
        x = find(fa, x);
        y = find(fa, y);
        fa[y] = x;
    }
    int minSwapsCouples(vector<int>& row) {
        int n = row.size();
        int node = n/2;
        vector<int> fa(node, 0);
        for (int i = 0; i < node; i++)
            fa[i] = i;
        for (int i = 0; i < n; i += 2) {
            int l = row[i]/2;
            int r = row[i+1]/2;
            Union(fa, l, r);
        }
        int ret = 0;
        for (int i = 0; i < node; i++) {
            ret += find(fa, i) != i?1:0;
        }
        
        return ret;
    }
};
```



