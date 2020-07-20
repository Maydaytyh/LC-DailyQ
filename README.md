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