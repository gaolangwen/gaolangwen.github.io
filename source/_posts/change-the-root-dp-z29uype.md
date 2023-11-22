<h1>换根DP</h1>
<blockquote>
<p>又称<strong>二次扫描</strong>。</p>
</blockquote>
<p>特征：</p>
<ol>
<li>树中没有指定根节点。</li>
<li>采用不同的节点作为根，答案可能不一样。</li>
</ol>
<h2>模板</h2>
<h3>P3478 [POI2008] STA-Station</h3>
<p>暴力解法：枚举根节点，求以该节点作为根时，所有节点的深度之和，时间复杂度O(n^2)<br />
优化：直接通过父节点的深度之和，得到子节点的深之和：子节点变为根节点之后，子节点及其子树深度+1，其他节点深度-1。<br />
解法：</p>
<ol>
<li>先指定任意节点作根节点</li>
<li>搜索完成指定根的答案计算，得到指定根的解。</li>
<li>二次扫描，利用原来根的解推出每个节点作为根的解。</li>
</ol>
<h4>定义状态</h4>
<p>dp[i]表示以1为总根节点，i为根的子树所有节点的深度之和<br />
f[i]表示以i为总根节点，其他节点的深度之和。<br />
答案：1~n中f[i]的最大值</p>
<h4>求状态转移方程</h4>
<p>dp[cur]+=dp[nxt]<br />
f[cur]=f[fa]-siz[cur]+(n-siz[cur])<br />
这个子树的深度全部-1，即减去这个子树的大小；其余节点深度+1（siz[i]为i的子树节点数量）</p>
<h4>初始状态</h4>
<p>dp[cur]=dep[cur]（dep[i]为节点i的深度）<br />
f[root]=dp[root]</p>
<h4>验证转移顺序</h4>
<p>dp是先递归后转移<br />
f是先转移再递归（f[nxt]=f[cur]-siz[nxt]+(n-siz[nxt])）</p>
<h4>代码</h4>
<pre><code class="language-cpp">#include&lt;bits/stdc++.h&gt;
using namespace std;
#define int long long

const int N=1e6+5;
int n,dp[N],f[N],dep[N],siz[N];
vector&lt;int&gt;nbr[N];

void dfs(int cur,int fa)
{
	dp[cur]=dep[cur]=dep[fa]+1;
	siz[cur]=1;
	for(int i=0;i&lt;nbr[cur].size();i++)
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
	for(int i=0;i&lt;nbr[cur].size();i++)
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
	cin&gt;&gt;n;
	for(int i=1;i&lt;=n-1;i++)
	{
		int x,y;
		cin&gt;&gt;x&gt;&gt;y;
		nbr[x].push_back(y);
		nbr[y].push_back(x);
	}
	dfs(1,0);
	f[1]=dp[1];
	second_dfs(1,0);
	int maxi=-1e9,pos;
	for(int i=1;i&lt;=n;i++)
		if(f[i]&gt;maxi)
		{
			maxi=f[i];
			pos=i;
		}
	cout&lt;&lt;pos;
	return 0;
}
</code></pre>
<p>时间复杂度O(n)</p>
<h4>CF1187E Tree Painting(双倍经验)</h4>
<p>只需将dep初值设为<code>0</code>即可</p>
<h3>P2986 [USACO10MAR] Great Cow Gatrering G</h3>
<p>本题加了边权和边权，只要稍微改动即可。<br />
<code>dfs</code>与<code>second_dfs</code>代码：</p>
<pre><code class="language-cpp">void dfs(int cur,int fa)
{
	siz[cur]=val[cur];
	for(int i=0;i&lt;nbr[cur].size();i++)
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
	for(int i=0;i&lt;nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(fa==nxt)
			continue;
		f[nxt]=f[cur]-siz[nxt]*w+(tot-siz[nxt])*w;
		second_dfs(nxt,cur);
	}
	return ;
}
</code></pre>
<h3>CF219D Choosing Capital for Treeland</h3>
<h4>定义状态</h4>
<p>dp[i]表示以1为总根节点，i为根的子树需要反转方向的次数<br />
f[i]表示以i为总根节点，需要反转方向的次数<br />
答案：f[i]的最小值</p>
<h4>求状态转移方程</h4>
<p>dp[cur]+=dp[nxt]+w<br />
w==0 -&gt; f[nxt]=f[cur]+1<br />
w==1 -&gt; f[nxt]=f[cur]-1</p>
<h4>初始状态</h4>
<p>f[1]=dp[1]</p>
<h4>验证转移顺序</h4>
<p>dp是先递归后转移<br />
f是先转移再递归</p>
<h4>代码</h4>
<pre><code class="language-cpp">#include&lt;bits/stdc++.h&gt;
using namespace std;

const int N=2e5+5;
struct Node
{
	int to;
	int w;
};
int n,dp[N],f[N];
vector&lt;Node&gt;nbr[N];

void dfs(int cur,int fa)
{
	for(int i=0;i&lt;nbr[cur].size();i++)
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
	for(int i=0;i&lt;nbr[cur].size();i++)
	{
		int nxt=nbr[cur][i].to,w=nbr[cur][i].w;
		if(nxt&lt;span style=&quot;font-weight: bold;&quot; class=&quot;mark&quot;&gt;fa)
			continue;
		if(w&lt;/span&gt;0)
			f[nxt]=f[cur]+1;
		else
			f[nxt]=f[cur]-1;
		second_dfs(nxt,cur);
	}
	return ;
}

int main()
{
	cin&gt;&gt;n;
	for(int i=1;i&lt;=n-1;i++)
	{
		int x,y;
		cin&gt;&gt;x&gt;&gt;y;
		nbr[x].push_back((Node){y,0});
		nbr[y].push_back((Node){x,1});
	}
	dfs(1,0);
	f[1]=dp[1];
	second_dfs(1,0);
	int mini=1e9;
	for(int i=1;i&lt;=n;i++)
		mini=min(mini,f[i]);
	cout&lt;&lt;mini&lt;&lt;&quot;\n&quot;;
	for(int i=1;i&lt;=n;i++)
		if(f[i]==mini)
			cout&lt;&lt;i&lt;&lt;&quot; &quot;;
	return 0;
}
</code></pre>
