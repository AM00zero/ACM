## 牛客训练赛第五场 SUMMARY 07-25

### code

#### 牛客5_B 完全图的mst

https://ac.nowcoder.com/acm/contest/5670/B

是道没学过的板子题。。这几天学学trie树，板子理解还不是很清楚，姑且先把能想通的内容写下来，以后有新想法再补。

可以看作给一张完全图求最小生成树，点权与边权的关系是 边权 = val[a] ^ val[b]，dis[i]定义为0点到i点边权的异或和，这样以来连接两点间的异或和就是dis[a]^dis[b]，把每一项dis插入trie树，**左0右1**，从树顶对trie树dfs，相当于从低位向高位遍历所有dis值，要求mst，则高位0应该尽可能多，尽可能确保走的是同一侧（异或相同为0）就可以做到。每次find返回值理解为连通两侧子树集合所需的最小权值。

```c++
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

#### 牛客5_D  LIS

给一个数组，操作1是把第n-1个数放在第一个，操作2是把第一个数放在最后，把数组看作环，则操作2只是转动了环，操作1则是对调第n和第n-1项。

操作2不计数，连续多次操作1只算做一次（分析样例才看出来了。。），求要多少次操作1后才能使数组整体有序（小到大）。

读懂题就很好想，把最长上升子序列求出来，剩下的数各需要一次操作1即可。

```c++
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

#### 牛客5_E 大数

给出一个序列b，原数组a按序列改变顺序，比如b[5]是 2 ，那么a[2] 就移动到第5位，求对于这个序列，移动几次能使a有序。

举例能发现每个值的移动范围是一个环（不一定遍历整个范围），求每个值需要次数的最小公倍数即答案。

```c++
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