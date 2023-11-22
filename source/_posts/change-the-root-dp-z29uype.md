---
title: 换根DP
date: '2023-11-22 21:57:23'
updated: '2023-11-22 22:02:33'
permalink: /post/change-the-root-dp-z29uype.html
comments: true
toc: true
---

# 换根DP

> 又称**二次扫描**。

特征：

1. 树中没有指定根节点。
2. 采用不同的节点作为根，答案可能不一样。

## 模板

### P3478 [POI2008] STA-Station

暴力解法：枚举根节点，求以该节点作为根时，所有节点的深度之和，时间复杂度O(n^2)
优化：直接通过父节点的深度之和，得到子节点的深之和：子节点变为根节点之后，子节点及其子树深度+1，其他节点深度-1。
解法：

1. 先指定任意节点作根节点
2. 搜索完成指定根的答案计算，得到指定根的解。
3. 二次扫描，利用原来根的解推出每个节点作为根的解。

#### 定义状态

dp[i]表示以1为总根节点，i为根的子树所有节点的深度之和
f[i]表示以i为总根节点，其他节点的深度之和。
答案：1~n中f[i]的最大值

#### 求状态转移方程

dp[cur]+=dp[nxt]
f[cur]=f[fa]-siz[cur]+(n-siz[cur])
这个子树的深度全部-1，即减去这个子树的大小；其余节点深度+1（siz[i]为i的子树节点数量）

#### 初始状态

dp[cur]=dep[cur]（dep[i]为节点i的深度）
f[root]=dp[root]

#### 验证转移顺序

dp是先递归后转移
f是先转移再递归（f[nxt]=f[cur]-siz[nxt]+(n-siz[nxt])）

#### 代码

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long

const int N=1e6+5;
int n,dp[N],f[N],dep[N],siz[N];
vector<int>nbr[N];

void dfs(int cur,int fa)
{
	dp[cur]=dep[cur]=dep[fa]+1;
	siz[cur]=1;
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i];
		if(nxt==fa)
			continue;
		dfs(nxt,cur);
		dp[cur]+=dp[nxt];
		siz[cur]+=siz[nxt];
	}
	return ;
}

void second_dfs(int cur,int fa)
{
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i];
		if(nxt==fa)
			continue;
		f[nxt]=f[cur]-siz[nxt]+(n-siz[nxt]);
		second_dfs(nxt,cur);
	}
	return ;
}

signed main()
{
	cin>>n;
	for(int i=1;i<=n-1;i++)
	{
		int x,y;
		cin>>x>>y;
		nbr[x].push_back(y);
		nbr[y].push_back(x);
	}
	dfs(1,0);
	f[1]=dp[1];
	second_dfs(1,0);
	int maxi=-1e9,pos;
	for(int i=1;i<=n;i++)
		if(f[i]>maxi)
		{
			maxi=f[i];
			pos=i;
		}
	cout<<pos;
	return 0;
}
```

时间复杂度O(n)

#### CF1187E Tree Painting(双倍经验)

只需将dep初值设为`0`即可

### P2986 [USACO10MAR] Great Cow Gatrering G

本题加了边权和边权，只要稍微改动即可。
`dfs`与`second_dfs`代码：

```cpp
void dfs(int cur,int fa)
{
	siz[cur]=val[cur];
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(fa==nxt)
			continue;
		dfs(nxt,cur);
		dp[cur]+=dp[nxt]+siz[nxt]*w;
		siz[cur]+=siz[nxt];
	}
	return ;
}
void second_dfs(int cur,int fa)
{
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(fa==nxt)
			continue;
		f[nxt]=f[cur]-siz[nxt]*w+(tot-siz[nxt])*w;
		second_dfs(nxt,cur);
	}
	return ;
}
```

### CF219D Choosing Capital for Treeland

#### 定义状态

dp[i]表示以1为总根节点，i为根的子树需要反转方向的次数
f[i]表示以i为总根节点，需要反转方向的次数
答案：f[i]的最小值

#### 求状态转移方程

dp[cur]+=dp[nxt]+w
w==0 -> f[nxt]=f[cur]+1
w==1 -> f[nxt]=f[cur]-1

#### 初始状态

f[1]=dp[1]

#### 验证转移顺序

dp是先递归后转移
f是先转移再递归

#### 代码

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N=2e5+5;
struct Node
{
	int to;
	int w;
};
int n,dp[N],f[N];
vector<Node>nbr[N];

void dfs(int cur,int fa)
{
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(fa==nxt)
			continue;
		dfs(nxt,cur);
		dp[cur]+=dp[nxt]+w;
	}
	return ;
}

void second_dfs(int cur,int fa)
{
	for(int i=0;i<nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(nxt<span style="font-weight: bold;" class="mark">fa)
			continue;
		if(w</span>0)
			f[nxt]=f[cur]+1;
		else
			f[nxt]=f[cur]-1;
		second_dfs(nxt,cur);
	}
	return ;
}

int main()
{
	cin>>n;
	for(int i=1;i<=n-1;i++)
	{
		int x,y;
		cin>>x>>y;
		nbr[x].push_back((Node){y,0});
		nbr[y].push_back((Node){x,1});
	}
	dfs(1,0);
	f[1]=dp[1];
	second_dfs(1,0);
	int mini=1e9;
	for(int i=1;i<=n;i++)
		mini=min(mini,f[i]);
	cout<<mini<<"\n";
	for(int i=1;i<=n;i++)
		if(f[i]==mini)
			cout<<i<<" ";
	return 0;
}
```
