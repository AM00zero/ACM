## 牛客训练赛第八场 SUMMARY 08-03

### code

#### 牛客8_I 并查集

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