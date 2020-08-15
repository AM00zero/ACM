## 牛客训练赛第九场 SUMMARY 08-08

### code

#### 牛客9_k 最短路+思维

给一棵树，边长为1，一开始1点有个人a，n点有个人b，a沿着最短路向b以1m/s走t秒后，开始逃离b，此时b以2m/s开始追a，问最晚要多久才能追上

比赛里是想着处理出 a能跑的最远距离d1 和 t秒时ab相对距离d2，以此来求结果的，不过应该是对结果的处理出了问题于是wa了（现在也没想懂orz），这里写下题解思路。

找到t时刻a处在的位置x，以x为起点求最短路dis[1]，以n点为起点求最短路dis[0]（由于速度是2m/s，耗时是dis/2向上取整），然后以n为起点遍历树，对每个dis[1]>=dis[0]的点，都记录下当前dis[0],找最大的那个dis[0]就是答案。

update：检查下原本wa的代码，改了两个地方过了（血亏，放两份代码记录下

1."dfs(now,0,-1)"从now开始dfs时，初始f节点不能写成pre[now]，这会让我们找“离t时刻所在点尽可能远的点”时，不去考虑1~n最短路的延长线；

2."ans = d1 + ((d2-d1+1)>>1)" 位运算注意括号

```
//mycode
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
int head[mxn],cnt,vis[mxn],dis[mxn],pre[mxn],nxt,ed,d1=0;
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
void dijk(int x)
{
	for(int i = 0;i<=n;i++)dis[i] = inf;
	priority_queue<pii,vector<pii>,greater<pii> >q;
	dis[x] = 0;
	q.push(make_pair(0,x));
	while(!q.empty())
	{
		pii tmp = q.top();
		q.pop();
		
		int ww = tmp.first,u = tmp.second;
		if(dis[u] < ww)continue;
		for(int i = head[u];~i;i= e[i].nx)
		{
			int v = e[i].v,w = e[i].w;
			if(dis[v] > dis[u] + w)
			{
				pre[v] =u;
				dis[v]  =dis[u] + w;
				q.push(make_pair(dis[v] , v)  );
			}
		}
	}
}
void dfs(int x,int d,int f)
{
	if(d1 < d)
	{
		d1 = d;
		ed = x;
	}
	for(int i = head[x];~i;i=e[i].nx)
	{
		int v = e[i].v;
		if(v==f)continue;
		if(nxt == v)continue;
		dfs(v,d+1,x);
	}
}
int main()
{
//	freopen("tst.txt","r",stdin);
//freopen("mans.txt","w",stdout);
	cin>>n>>m;
	inti();
	for(int i = 1;i<n;i++)
	{
		scanf("%d%d",&a,&b);
		add(a,b,1);
		add(b,a,1);
	}
	dijk(1);
	if(dis[n] <=m )
	{
		puts("0");
		return 0;
	}
	int ct = dis[n]-m,now=  n;
	if(ct==1)
	{
		puts("1");
		return 0;
	}
	nxt = -1;
	int d2 = ct;//n离t时刻1距离 ,此时d2至少是2 
	while(ct>0 )
	{
		ct--;
		//cout<<tmp<<"--";
		nxt = now;
		now = pre[now];
	}
	dfs(now,0,-1);
//	cout<<pre[now]<<" "<<	d1<<" "<<d2<<endl;
	if(d2<d1)printf("%d\n",d2);
	else
	{
		int ans ;
		ans = d1 + ((d2-d1+1)>>1);
		printf("%d\n",ans);
	}
	return 0;
}
```



```
//题解思路代码
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

