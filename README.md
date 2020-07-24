# LC-DailyQ

### 2020.7.16
#### 题目 ：[785判断二分图](https://leetcode-cn.com/problems/is-graph-bipartite/)
#### 思路
DFS/BFS染色、并查集
#### 代码
``` C++
class Solution {
public:
//并查集版本
    vector<int> father;
    bool isBipartite(vector<vector<int>>& graph) {
        int n=graph.size();
        father.assign(n,0);
        for(int i=0;i<n;++i) father[i]=i;
        for(int i=0;i<n;++i)
        {
            // if(father[i]!=i) continue;
            if(graph[i].size()==0) continue;
            int x=getfather(i);
            int y=getfather(graph[i][0]);
            if(x==y) return false;
            for(int j=1;j<graph[i].size();++j)
            {
                int tt=getfather(graph[i][j]);
                if(tt==x) return false;
                father[tt]=y;
            }
        }
        return true;
    }
    int getfather(int x)
    {
        return (father[x]==x)?x:getfather(father[x]);
    }
};
//DFS版本
class Solution {
public:
    vector<int> v;
    bool dfs(vector<vector<int>>& graph,int i,int c)
    {
        if(v[i]!=-1) return v[i]==c;
        v[i]=c;
        for(int node:graph[i])
        {
            if(v[node]!=-1&&v[node]==c) return false;
            if(!dfs(graph,node,!c))
            {
                return false;
            }
        }
        return true;
    }
    bool isBipartite(vector<vector<int>>& graph) {
        int n=graph.size();
        v.assign(n,-1);
        for(int i=0;i<n;++i)
        {
            if(v[i]==-1)
            {
                if(!dfs(graph,i,1))
                return false;
            }
        }
        return true;
    }
};
```
### 2020.7.17
#### 题目： [35.搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)
#### 思路
二分、lower_bound
#### 代码
``` C++
//STL
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        return lower_bound(nums.begin(),nums.end(),target)-nums.begin();
    }
};
//手写二分
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int n=nums.size();
        if(n==0) return 0;
        int left=0,right=n-1;
        while(left<right){
            int mid=left+(right-left)/2;
            if(nums[mid]<target) left=mid+1;
            else if(nums[mid]==target) return mid;
            else right=mid;
        }
        if(nums[left]<target) return n;
        return left;
    }
};
```

### 2020.7.18
#### 题目： [97.交错字符串](https://leetcode-cn.com/problems/interleaving-string/)
#### 思路
dp、思维

难点在于关系式的书写，用dp(i,j)表示用s1的前i个字符和s2的前j个字符能否表示s3的前i+j个字符，这其实是一种思维的培养吧。

关于空间优化，滚动数组其实也是下意识的，因为第i行只和第i行和第i-1行有关，所以可以进行优化。

#### 代码

``` C++
//未优化空间
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if(s3.length()!=s1.length()+s2.length()) return false;
        int l1=s1.length(),l2=s2.length();
        vector<vector<int> > dp(l1+1,vector<int>(l2+1,0));
        dp[0][0]=1;
        for(int i=0;i<=l1;++i)
         for(int j=0;j<=l2;++j)
         {
             int pos=i+j-1;
             if(i>0&&s1[i-1]==s3[pos]&&dp[i-1][j])
                dp[i][j]=1;
             else if(j>0&&s2[j-1]==s3[pos]&&dp[i][j-1])
                dp[i][j]=1;
            
         }
         if(dp[l1][l2]) return true;
         return false;
    }
};
//滚动数组优化解法
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        if(s3.length()!=s1.length()+s2.length()) return false;
        int l1=s1.length(),l2=s2.length();
        vector<int> dp(l2+1,0);
        dp[0]=1;
        for(int i=0;i<=l1;++i)
            for(int j=0;j<=l2;++j)
            {
                if(i>0) dp[j]=(s1[i-1]==s3[i+j-1]&&dp[j]);
                if(j>0) dp[j]|=(s2[j-1]==s3[i+j-1]&&dp[j-1]);
            }
            if(dp[l2]) return true;
            return false;
    }
};
```

### 2020.7.20

#### 题目：[167.两数之和Ⅱ-输入有序数组](https://leetcode-cn.com/problems/two-sum-ii-input-array-is-sorted/)

#### 思路

二分查找、双指针、哈希表

#### 代码

``` c++
//二分
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        // vector<int> ans;
        for(int i=0;i<numbers.size();++i)
        {
            int left=i+1;int right=numbers.size()-1;
            int x=target-numbers[i];
            while(left<=right)
            {
                int mid=(left+right)>>1;
                if(numbers[mid]>x) right=mid-1;
                else if(numbers[mid]<x) left=mid+1;
                else 
                {
                    return {i + 1, mid + 1};
                }
            }
        }
        return {-1,-1};
    }
};
//双指针
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
       int left=0,right=numbers.size()-1;
       while(left<right)
       {
           int sum=numbers[left]+numbers[right];
           if(sum==target) return {left+1,right+1};
           else if(sum>target) right--;
           else left++;
       }
       return {-1,-1};
    }
};
//哈希表
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
      map<int,int> mp;
      for(int i=numbers.size()-1;i>=0;--i)
      {
          mp[numbers[i]]=i+1;
      }
      for(int i=0;i<numbers.size();++i)
      {
          int x=target-numbers[i];
          if(mp[x]>0)
          {
              if(x==numbers[i]) 
                return {mp[numbers[i]],mp[x]+1};
            else return {mp[numbers[i]],mp[x]};
          }
      }
      return {-1,-1};
    }
};
```

#### 今日感悟

二分查找一般还是加上等号吧，遇到其他情况再学习。

关于重复元素用哈希表来存储第一个元素出现的位置也很有灵性。

### 2020.7.22

#### 题目：[剑指offer11.旋转数组的最小数字](https://leetcode-cn.com/problems/xuan-zhuan-shu-zu-de-zui-xiao-shu-zi-lcof/)

#### 思路

- 暴力，判断哪儿下降了即可，单独判断是不是a[0]<a[n-1]，是的话说明没有进行旋转变换。

- 二分，可能我对二分理解还是有偏差。。根本没想到，不过确实这里可以用。

#### 代码

``` c++
//二分
class Solution {
public:
    int minArray(vector<int>& numbers) {
        int n=numbers.size();
        if(numbers[0]<numbers[n-1])
        {
            return numbers[0];
        }
        int l=0,r=n-1;
        while(l<r)
        {
            if(r-l==1) return numbers[l]<numbers[r]?numbers[l]:numbers[r];//两个元素中较小的一个直接返回
            int mid=l+(r-l)/2;
            if(numbers[mid]>numbers[r]) l=mid;//如果中间大于r，说明最小元素在右侧区间
            else if(numbers[mid]==numbers[r]) r--;//如果等于的话，则不好判断，挨个减一进行判断，把握住缩小区间即可。
            else r=mid;//如果小于r，说明最小元素还在前面
        }
        return numbers[l];
    }
};
//暴力
class Solution {
public:
    int minArray(vector<int>& numbers) {
        if(numbers[0]<numbers[numbers.size()-1]) return numbers[0];
        for(int i=0;i<numbers.size()-1;++i)
        {
            if(numbers[i]>numbers[i+1]) return numbers[i+1];
        }
        return numbers[0];
    }
};
```
### 2020.7.23
#### 题目：[64.最小路径和](https://leetcode-cn.com/problems/minimum-path-sum/)
#### 思路
- 动态规划，当前状态只和上方、左方的状态有关系，关系式子很好写。
- 空间压缩，注意到只用了两行数据，从而可以只使用一维数组来达到空间优化
#### 代码：
``` C++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m=grid.size();
        int n=grid[0].size();
        // cout<<"m="<<m<<"n="<<n<<endl;
        // vector<vector<int>> dp(m+1,vector<int>(n+1,0));
        // dp[0][0]=grid[0][0];
        // if(m>1){
        
        // dp[1][0]=dp[0][0]+grid[1][0];
        // }
        // if(n>1)
        // {
        //     dp[0][1]=dp[0][0]+grid[0][1];
        // }
        // for(int i=0;i<m;++i)
        //     for(int j=0;j<n;++j)
        //     {
        //         if(i==0&&j==0) continue;
        //         else if(i==0)
        //         {
        //             dp[i][j]=dp[i][j-1]+grid[i][j];
        //         }
        //         else if(j==0)
        //         {
        //             dp[i][j]=dp[i-1][j]+grid[i][j];
        //         }
        //         else
        //             dp[i][j]=min(dp[i-1][j],dp[i][j-1])+grid[i][j];
        //     }
        // return dp[m-1][n-1];
        vector<int> dp(n+1,0);
        dp[0]=grid[0][0];
        
        // for(int i=1;i<n;++i) dp[i]=dp[i-1]+grid[0][i];
        for(int i=0;i<m;++i)
            for(int j=0;j<n;++j)
            {
                if(i==0&&j==0) continue;
                else if(i==0)
                {
                    dp[j]=dp[j-1]+grid[i][j];
                } 
                else if(j==0) dp[j]=dp[0]+grid[i][j];
                else{
                    dp[j]=min(dp[j],dp[j-1])+grid[i][j];
                }
                // cout<<"j="<<j<<"ans="<<dp[j]<<endl;
            }
        return dp[n-1];
    }
};
```
#### 今日感悟
动态规划越来越有以前的感觉了，但是不知道为什么，没有优化空间的时候，运行时间反而更短，这一点没想明白。

### 2020.7.24
#### 题目：[1025.除数博弈](https://leetcode-cn.com/problems/divisor-game/)
#### 思路
- 博弈论，其实判断一下1的状态为必败，且从2可以转移到1，同理，从n可以转移到n-1，而最后1的状态为必败，所以可推出奇数为必败，偶数为必胜。
#### 代码
``` C++
class Solution {
public:
    bool divisorGame(int N) {
        if(N%2==0) return true;
        return false;
    }
};
```
