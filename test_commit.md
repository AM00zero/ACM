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

##牛客5_D  LIS

给一个数组，操作1是把第n-1个数放在第一个，操作2是把第一个数放在最后，把数组看作环，则操作2只是转动了环，操作1则是对调第n和第n-1项。

操作2不计数，连续多次操作1只算做一次（分析样例才看出来了。。），求要多少次操作1后才能使数组整体有序（小到大）。

读懂题就很好想，把最长上升子序列求出来，剩下的数各需要一次操作1即可。

```
#include <bits/stdc++.h>

using namespace std;
const int mxn=505;
int a[mxn],tmp[mxn];
int main()
{
    int n,ans=-1;
    scanf("%d",&n);
    for(int i=1;i<=n;i++)scanf("%d",a+i);
        
    for(int i=1;i<=n;i++)
    {
        int cnt=1,ct=1;
        tmp[ct]=a[i];//枚举起点求环里的LIS 
        while(++cnt<=n)
        {
            int t=(i+cnt-1);
            if(t>n)t%=n;
            if(tmp[ct]<=a[t]) tmp[++ct]=a[t];
            else
            {
                int x=lower_bound(tmp+1,tmp+1+ct,a[t])-tmp;
                tmp[x]=a[t];
            }
        }
        ans=max(ans,ct);
    }
    printf("%d\n",n-ans);
    return 0;
}
```

## 牛客5_E 大数

给出一个序列b，原数组a按序列改变顺序，比如b[5]是 2 ，那么a[2] 就移动到第5位，求对于这个序列，移动几次能使a有序。

举例能发现每个值的移动范围是一个环（不一定遍历整个范围），求每个值需要次数的最小公倍数即答案。

```
def gcd(a, b):
    if a % b == 0:
        return b
    else:
        return gcd(b, a % b)
n=int(input())
a=[0]+list(map(int,input().split()))
vis=[0]*(n+5)
ans=1
for i in range(1,n+1):
    t=i;cnt=0
    while vis[a[t]] != 1:
        vis[a[t]] = 1
        t=a[t]
        cnt=cnt+1
    if(cnt!=0):
        ans = ans * cnt // gcd(ans,cnt)#这里求的是最小公倍数
print(ans)

```

## 牛客7_B gcd思维

给nxm个口罩，要求分成尽可能少的份数，且能均分给n或m个医院

先看分给n个医院的方案，此时n个医院各需要共计m口罩，确保n>m，然后先分出(n/m)份每份m个口罩，这样还剩n%m个医院各需要共计m个口罩，此时转化为子问题，递归解决。

```
#include<bits/stdc++.h>
using namespace std;
vector<int>v;
void _gcd(int n,int m)
{
	if(n<m)swap(n,m);
	if(m==0)return ;
	int t = (n/m)*m;
	while(t--)v.push_back(m);
	_gcd(n%m,m);
}

int main()
{
	int T,n,m;
	cin>>T;
	while(T--)
	{
		v.clear(); 
		scanf("%d%d",&n,&m);
		_gcd(n,m);
        printf("%d\n",v.size());
		for(int i=0;i<v.size()-1;i++)printf("%d ",v[i]);
		printf("%d\n",v[v.size()-1]);
	}
	return 0;
 } 
```

## 牛客7_H 整除分块

给出参数n,k，(1,k)符合条件，当(n,k)符合时(n+k,k)和(nk,k)也符合，问有多少种符合条件的组合。

由1+ i  *  k <= N && i * k<=N可知结果公式![awnlad.png](https://s1.ax1x.com/2020/08/04/awnlad.png)

```
#include <bits/stdc++.h>
typedef long long ll;
using namespace std;
const ll mod=1e9+7;

  
ll solve(ll n,ll k)
{
    ll i, j,ans = 0;
    for(i = 2; i <= n && i <= k; i = j+1)
	{
        j = min(n/(n/i), k);
        ans =(ans + (j-i+1)%mod*(n/i)%mod) %mod;
    }
    return ans ;
}
  
int main()
{
	ll n,k,ans;
    scanf("%lld%lld", &n, &k);
    ans = (solve(n,k)+solve(n-1,k)) %mod;
    printf("%lld\n", (ans+n+k-1)%mod);
    return 0;
}
```


## 牛客8_I 并查集

n个回合，每回合给两个数，取过的点不能再取，问最后最多能取多少个点

把每个数离散化，对于同一回合的两个数，把他们并起来。对于一个联通块而言，如果有环则必定可以选中联通块内的全部点（不仅仅是环内，与环相连的也可以全部取到）例：1 2,2 3,3 4,4 1,1 5,1 6,1 7，如果没环，则该块内无论怎么取都有一个点取不到。

```
#include<bits/stdc++.h>
using namespace std;
#define ms(x,y) memset(x,y,sizeof(x))
const int mxn = 2e5+10;
const int inf = 0x3f3f3f3f;
int num[mxn],a[mxn],b[mxn], f[mxn],vis[mxn],cnt[mxn],ct,n;
map<int,int>id;
int find(int x)
{
	return f[x] == x ? x : f[x] = find(f[x]);
}
int main()
{
   
    int T,ca=0;
    cin>>T;
    while(T--)
    {
        id.clear();
        ct=0;
        scanf("%d",&n);
       
        for(int i =1;i<=n;i++)
        {
            scanf("%d%d",a+i,b+i);
             
            if(!id[a[i]])id[a[i]]=++ct;
             
            if(!id[b[i]])id[b[i]]=++ct;
        }
        for(int i = 1;i<=ct;i++)
		{
			f[i]=i;
			vis[i]=0;
		} 
        for(int i = 1;i<=n;i++)
        {
        	int aa = find(id[a[i]]),bb=find(id[b[i]]);
        	
        	if(aa!=bb)
        	{
        		f[bb]=aa;
        		if(vis[aa]||vis[bb])
        		{
        			vis[aa]=1;
        			vis[bb]=0;
				}
			}
			else vis[aa]=1;//vis表示以该点为根的联通块内有环 
		}
		
        int ans = ct;
        for(int i=1;i<=ct;i++)if(!vis[i]&&f[i]==i)ans--;
        printf("Case #%d: %d\n",++ca,ans);
    }
    return 0;
 }
```

## 牛客9_k 最短路+思维

给一棵树，边长为1，一开始1点有个人a，n点有个人b，a沿着最短路向b以1m/s走t秒后，开始逃离b，此时b以2m/s开始追a，问最晚要多久才能追上

比赛里是想着处理出 a能跑的最远距离d1 和 t秒时ab相对距离d2，以此来求结果的，不过应该是对结果的处理出了问题于是wa了（现在也没想懂orz），这里写下题解思路。

找到t时刻a处在的位置x，以x为起点求最短路dis[1]，以n点为起点求最短路dis[0]（由于速度是2m/s，耗时是dis/2向上取整），然后以n为起点遍历树，对每个dis[1]>=dis[0]的点，都记录下当前dis[0],找最大的那个dis[0]就是答案。

```
#include<bits/stdc++.h>
using namespace std;
#define ms(x,y) memset(x,y,sizeof(x))
#define pii pair<int,int>
#define bug(x) cout<<"x"<<endl
typedef long long ll ;
const int mxn =  100010;
const int mxm =  200010;
const int  inf = 0x3f3f3f3f;
int n,m,a,b,c;
double dis[mxn][3];
int head[mxn],cnt,vis[mxn],pre[mxn],ans = -1,ct;
struct ed
{
	int v,w,nx;
	ed()	{	}
}e[mxm];
inline int read()
{
    int x=0,f=1;char c=getchar();
    while(c<'0'||c>'9'){if(c=='-') f=-1;c=getchar();}
    while(c>='0'&&c<='9') x=(x<<3)+(x<<1)+(c^48),c=getchar();
    return x*f;
}
void inti(){ms(head,-1);cnt =  0;ms(pre,0);}
void add(int x,int y,int z){e[cnt].v = y;e[cnt].w = z;e[cnt].nx  =head[x] ;head[x] = cnt++; }
void dijk(int x,int ty,int tyy)
{
	for(int i = 0;i<=n;i++)dis[i][ty] = inf;
	priority_queue<pii,vector<pii>,greater<pii> >q;
	dis[x][ty] = 0;
	q.push(make_pair(0,x));
	while(!q.empty())
	{
		pii tmp = q.top();
		q.pop();
		
		int ww = tmp.first,u = tmp.second; 
		if(dis[u][ty] < ww)continue;
		for(int i = head[u];~i;i= e[i].nx)
		{
			int v = e[i].v,w = e[i].w;
			
			if(dis[v][ty] > dis[u][ty] + w)
			{
				pre[v] =u;
				dis[v][ty]  =dis[u][ty] + w;
				
				q.push(make_pair(dis[v][ty] , v)  );
			}
		}
	}
	if(tyy == 1)
		for(int i = 1;i<=n;i++)dis[i][0] = ceil(dis[i][0]/2);
}

void dfs(int x,int f)
{
	int son = 0;
	for(int i = head[x];~i;i=e[i].nx)
	{
		int v = e[i].v;
		if(v==f)continue;
		son=1;
		if(dis[v][0] <= dis[v][1])
		{
			ans =max(ans,(int)dis[v][0]);
			continue;
		}
		dfs(v,x);
	}
	if(!son)ans = max(ans,(int)dis[x][0]);
}
int main()
{
	cin>>n>>m;
	inti();
	for(int i = 1;i<n;i++)
	{
		scanf("%d%d",&a,&b);
		add(a,b,1);
		add(b,a,1);
	}
	dijk(1,0,0);

	int ct=dis[n][0]-m,now = n;
	if(ct<=0)
	{
		puts("0");
		return 0;
	}
	while(ct>0 )
	{
		ct--;
		now = pre[now];
	}
	dijk(n,0,1);

	dijk(now,1,0);
	
	dfs(now,-1);
	printf("%d\n",ans);
	return 0;
}
```


## 签到题记录

### 19牛客1_F  计算几何（？求期望

参考了https://blog.csdn.net/weixin_43350051/article/details/97139683

多组输入三角形各个顶点坐标p1,p2,p3，在三角形中任取一点p，计算 期望E=max(S(p,p1,p2),max(S(p,p1,p3),S(p,p2,p3)));

写一个储存坐标系的模板，用向量叉乘的方式能通过三点坐标确定三角形面积

![akHi1P.jpg](https://s1.ax1x.com/2020/07/28/akHi1P.jpg)

```
随机数找三角形中的点，平均求期望
#include<bits/stdc++.h>
using namespace std;
struct point{
    double x;
    double y;
};
double S(point p1,point p2,point p3) ///向量叉乘求三角形面积可以看上面链接
{
    return fabs((p1.x-p3.x)*(p1.y-p2.y)-(p1.x-p2.x)*(p1.y-p3.y));
}
 
bool judge(point p,point p1,point p2,point p3) ///判断生成的随机点是不是在三角中 原理是：三个 
                                         ///小三角形面积之和是不是等于原来大的三角形面积
{
    if(S(p1,p2,p3)==(S(p1,p2,p)+S(p,p1,p3)+S(p2,p3,p))){
    
        return true;
    }
    return false;
}
int main()
{
    double x1,x2,x3,y1,y2,y3;
    while(~scanf("%lf%lf%lf%lf%lf%lf",&x1,&y1,&x2,&y2,&x3,&y3)){
        double cnt=0;
        struct point p1,p2,p3,p;
        p1.x=x1,p1.y=y1;
        p2.x=x2,p2.y=y2;
        p3.x=x3,p3.y=y3;
        srand(time(0));
        double sum=0;
        for(int i=0;i<100000000;i++){
            int x=rand();
            int y=rand();
            p.x=x*1.0,p.y=y*1.0;
            if(judge(p,p1,p2,p3)){
                cnt++;
                sum+=(max(S(p1,p2,p),max(S(p,p1,p3),S(p2,p3,p))));
            }
            //printf("%.3lf\n",sum);
        }
        double S1=S(p1,p2,p3);
        double q=((sum/cnt)*36.0)/S1;
        printf("%.3lf\n",q);
    }
    return 0;
}
```

