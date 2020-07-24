##多校补题_from_lsr

#杭电2场1001__ 并查集 [Total Eclipse\] 

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





