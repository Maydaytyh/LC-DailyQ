# LC-DailyQ

### 2020.7.16
#### 题目：[785判断二分图](https://leetcode-cn.com/problems/is-graph-bipartite/)
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
