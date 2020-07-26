#多校补题_from_lsr

##杭电2场1001__ 并查集 [Total Eclipse\] 

http://acm.hdu.edu.cn/showproblem.php?pid=6763

给一张图n点m边，每个点有一个权值，每次操作都是：找k个处于同一连通块的点，令他们权值减一，（k尽可能大）问最少要多少次操作能让全部点权值清零。

每次都是找最大的连通分量，减到割点权值清零，连通分量分裂后，，重复上述步骤。

题解的思路是倒过来想，越靠后被清零的必定是权值越大的点，按权值**从小到大删点**  反过来就是按权值**从大到小合并**。假设对于这张图

![map_1.png](https://i.loli.net/2020/07/24/YHUAByPnpmIvzGt.png)

顺着题目思路就是先val[u]次操作使u权值清零，再分别val [v<sub>i</sub>]-val[u]次操作结束，共计val[u]+val[v1]+val[v2]+val[v3]-3*val[u]次。

关于合并：每次遍历到点u连接的子节点v时，如果val[v] < val[u] (或val[v] == vla[u] && v<u)，则可以将两点合并，权值小的在上，权值相同时编号小的在上，这样按题目流程，先删除的就是权值较小的根节点。

关于结果：按代码里的流程，按权值从大到小每遍历到一个点u，就先加一次他的权值val[u]，当uv合并时再减一次val[u]，因为每个v点**单独需要的次数里**都有一项-val[u]



```c++
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
#define fi first
#define se second
#define pii pair<int,int>
#define ms(x,y) memset(x,y,sizeof(x))
const int mxn  = 2e6+10;
int f[mxn];
struct edge
{
	int v,nx;
}e[mxn<<1];
int head[mxn],b[mxn],id[mxn],cnt = 0;
void add(int u,int v)
{
	e[++cnt].v=v;
	e[cnt].nx=head[u];
	head[u] = cnt;
}

int find(int u)
{
	return u == f[u] ? u : f[u] = find(f[u]);
}
bool cmpp(int i,int j){
    if(b[i]==b[j]) return i<j;
    return b[i]>b[j];
}
int main()
{
	int T,tmp,n,m,x,y;cin>>T;
	while(T--)
	{
		scanf("%d%d",&n,&m);
		ms(head,-1);cnt=0;
		for(int i = 1;i<=n;i++)
		{
			f[i] = i;id[i]=i;
			scanf("%d",b+i);
		}
		for(int i = 1;i<=m;i++)
		{
			scanf("%d%d",&x,&y);
			add(x,y);add(y,x);
		}
		sort(id+1,id+1+n,cmpp);
		ll ans = 0;
		for(int i = 1;i<=n;i++)
		{
			//cout<<"--"<<b[i].fi<<" "<<b[i].se<<endl;
			int u = id[i]; 
			ans += b[i];
			for(int j = head[u];j!=-1;j=e[j].nx)
			{
				int v = e[j].v;
				
				if(b[v]<b[u])continue;
				if(b[u] == b[v] && v>u)continue;
				int uu = find(u);
				int vv = find(v);
				if(uu!=vv)
				{
					f[vv] =uu;
					ans -= b[u];
				}
			}
		
		
		}
		printf("%lld\n",ans);
	}
	return 0;
 } 
```


##牛客3场 _g题  并查集+链表

https://ac.nowcoder.com/acm/contest/5668/G

给一张图n点m边，一开始每个点颜色不同，共n种颜色。

后接q次染色操作，每次给出一个点（给出一种颜色），如果该点（集合）颜色没被改变过，由该点（集合）出发把与其相连的点（集合）染成相同颜色，求q次操作后结果。**开始时当然按点理解，当开始合并后，两个集合相遇是要把整个集合都染色的。**

难点在于对每个点都维护一个链表，存储i号颜色集合里**尚未对周围染色的点**。对于每次操作的节点u，如果该点已被染色（find(x)!=x）则跳过。否则遍历u的链表，从链表中节点t开始bfs（这一步看作是让颜色u**集合中每个点都尝试向外染色**），对于与t相连的v点，如果v还未被染色，用并查集合并进u的集合，再把v的链表拼接到u下，这样下次再对颜色u操作时，就可以**从原本颜色v的点开始**向外染色了。

```
#include<bits/stdc++.h>
using namespace std;
#define ms(x,y) memset(x,y,sizeof(x))
#define pii pair<int,int>

#define bug(x) cout<<"x"<<endl
typedef long long ll ;
const int mxn =  8e5+20;
const int  inf = 0x3f3f3f3f;
int n,m,x,y,z;
int f[mxn];
bool vis[mxn];
vector<int> g[mxn];
list<int> lis[mxn];
inline int read()
{
    int x=0,f=1;char c=getchar();
    while(c<'0'||c>'9'){if(c=='-') f=-1;c=getchar();}
    while(c>='0'&&c<='9') x=(x<<3)+(x<<1)+(c^48),c=getchar();
    return x*f;
}
void init()
{
	for(int i = 0;i<=n+1;i++)
	{
		vis[i]=false;
		f[i]=i;
		g[i].clear();
		lis[i].clear();
		lis[i].push_back(i); 
	}
}

int find(int x)
{
	return f[x] == x ? x : f[x] = find(f[x]);
}
void unit(int x,int y)
{
	int xx = find(x),yy=find(y);
	if(xx != yy)
	{
		f[xx] =yy;
		lis[yy].splice(lis[yy].end(),lis[xx]);//把yy链表接到xx后面 
	}
}
void bfs(int u)
{
	vis[u]=1; 
	if(find(u)!=u)return ;
	int sz = lis[u].size();
	for(int i = 0;i<sz;i++)
	{
		int idx = lis[u].front();//遍历链表方式
		for(int j=0;j<g[idx].size();j++)
		{
			int v = g[idx][j];
			unit(v,u);
			if(vis[v])continue;
			
			lis[u].push_back(v); vis[v] = true;
		}
		lis[u].pop_front();//遍历链表方式 
	}
}
int main()
{
	int T,q;cin>>T;
	while(T--)
	{
		cin>>n>>m;
	
		init();
		for(int i = 1;i<=m;i++)
		{
			cin>>x>>y;
			g[x].push_back(y);
			g[y].push_back(x);
		}
		cin>>q;
		while(q--)
		{
			cin>>x;
			bfs(x);
		}
		for(int i = 0;i<n-1;i++)
		{
			int ans = find(i);
			printf("%d ",ans);
		}
		printf("%d\n",find(n-1));
	}
	return 0;
}
```

## 牛客二场_F  gcd+二维单调队列

https://ac.nowcoder.com/acm/contest/5667/F

给一个nxm矩阵，A[i] [j] = lcm(i,j)，求最大kxk大小的子区间和。补题正好复习下二维单调队列，需要注意正常遍历得出A数组会超时。

```
#include <bits/stdc++.h>
#define fi first
#define se second
#define pii pair<int,int>
#define ll long long
using namespace std;
const int maxn=5005;
int a[maxn][maxn];
void init(int n,int m){
    for(int i=1;i<=n;++i)
        for(int j=1;j<=m;++j)
            if(!a[i][j])
                for(int k=1;k*i<=n&&k*j<=m;++k)
                    a[i*k][j*k]=i*j*k;
}
int ma[maxn][maxn];

int main(){
    int n,m,k;cin>>n>>m>>k;
    init(n,m);
	//fi是权值，se是编号/顺序 
    for(int j=1;j<=m;++j){
        deque<pii> q;
        for(int i=1;i<=n;++i){
            while(!q.empty()&&(q.front().fi<a[i][j]||i-q.front().se+1>k)) q.pop_front();
            while(!q.empty()&&q.back().fi<a[i][j]) q.pop_back();
            q.push_back({a[i][j],i});
            ma[i][j]=q.front().fi;
        }
    }
    ll ans=0;
    for(int i=k;i<=n;++i){
        deque<pii> q;
        for(int j=1;j<=m;++j){
            while(!q.empty()&&(q.front().fi<ma[i][j]||j-q.front().se+1>k)) q.pop_front();
            while(!q.empty()&&q.back().fi<ma[i][j]) q.pop_back();
            q.push_back({ma[i][j],j});
            if(j>=k)
            ans+=q.front().fi;
        }
    }
    cout<<ans<<endl;
}
```

## 牛客5_B 完全图的mst

https://ac.nowcoder.com/acm/contest/5670/B

是道没学过的板子题。。这几天学学trie树，板子理解还不是很清楚，姑且先把能想通的内容写下来，以后有新想法再补。

可以看作给一张完全图求最小生成树，点权与边权的关系是 边权 = val[a] ^ val[b]，dis[i]定义为0点到i点边权的异或和，这样以来连接两点间的异或和就是dis[a]^dis[b]，把每一项dis插入trie树，**左0右1**，从树顶对trie树dfs，相当于从低位向高位遍历所有dis值，要求mst，则高位0应该尽可能多，尽可能确保走的是同一侧（异或相同为0）就可以做到。每次find返回值理解为连通两侧子树集合所需的最小权值。

```
#include<cstdio>
#include<cstring>
int dis[200001],n;
int node[6000060],dep[6000060];
int son[6000060][2],num,mul[30];
long long ans=0;
int tot=0,head[100005];
struct edge
{
    int v,nxt,w;
}e[200005];
void add(int a,int b,int c)
{
    tot++;
    e[tot].v=b;
    e[tot].w=c;
    e[tot].nxt=head[a];
    head[a]=tot;
}
long long min(long long a,long long b)
{
    return a<b?a:b;
}
void clear()
{
    memset(node,0,sizeof(0));
    memset(son,0,sizeof(0));
    memset(dep,0,sizeof(dep));
    num=1;
    node[1]=1;
}
long long find(int x,int y)
{
	
    if (dep[x]==30) return dis[node[x]]^dis[node[y]];
    bool flag=0;
    long long ret=2e9;
    //先考虑同一侧能否合并，异或是同一bit位上相同为0
	//这样能确保从x位开始到最高位有尽可能多的零 
    for (int i=0;i<=1;i++) if (son[x][i]&&son[y][i]) ret=min(ret,find(son[x][i],son[y][i])),flag=1;
    if (flag==0)
    {
        if (son[x][0]&&son[y][1]) ret=min(ret,find(son[x][0],son[y][1]));
		else if (son[x][1]&&son[y][0])ret=min(ret,find(son[x][1],son[y][0]));
    }
    return ret;
}
void dfs(int x)
{
    if (x==0) return;
    for (int i=0;i<=1;i++) dfs(son[x][i]);
    if (son[x][0]&&son[x][1]) ans+=find(son[x][0],son[x][1]);
}
void insert(int k)//把每一个点的dis插入trie树 
{
    int x=1;
    for (int i=29;i>=0;i--)
    {
        int p=1<<i;
        bool tmp=p&dis[k];//编号0点-k点路径异或和，如果第i位为0进入左子树 
        if (son[x][tmp]==0) son[x][tmp]=++num,dep[num]=29-i+1,node[num]=n+1;
        x=son[x][tmp];
    }
    node[x]=k;
}
void get(int x,int fa)
{
    for (int i=head[x];i;i=e[i].nxt) if (e[i].v!=fa)
    {
        dis[e[i].v]=dis[x]^e[i].w;
        get(e[i].v,x);
    }
}
int main()
{
    clear();
    mul[0]=1;
    for (int i=1;i<=29;i++) mul[i]=mul[i-1]<<1;
    scanf("%d",&n);
    for (int i=1;i<n;i++)
    {
        int a,b;
        long long c;
        scanf("%d%d%d",&a,&b,&c);
        a++;
        b++;
        add(a,b,c);
        add(b,a,c);
    }
    dis[1]=0;
    get(1,0);
    for (int i=1;i<=n;i++) insert(i);
    dfs(1);
    printf("%lld",ans);
}
```

