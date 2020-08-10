## 牛客训练赛第六场 SUMMARY 07-27

### code

#### 牛客6-K K-bag 离散化、枚举/DP/hash散列(各种方法欢迎尝试)
链接：https://ac.nowcoder.com/acm/contest/5671/K

A sequence is called kk-bag, if and only if it is put in order by some (maybe one) permutations of 11 to kk. For example, 1,2,3,2,1,3,3,2,11,2,3,2,1,3,3,2,1 is a valid 33-bag sequence.
Roundgod is not satisfied with kk-bag, so she put forward part-kk-bag, which is a  contiguous subsequence of kk-bag.
Wcy wants to know if the sequence of length nn is a part-kk-bag sequence.
方法很多值得尝试

```c++
# 离散化+枚举
#pragma warning (disable:4996)
#include <iostream>
#include <algorithm>
#include <iomanip>
#include <cstring>
#include <string>
#include <cstdio>
#include <cmath>
#include <vector>
#include <queue>
#include <list>
#include <set>
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

const int maxn=5e5+5;
int T,n,k,a[maxn],b[maxn],pre[maxn],len[maxn];
int main()
{
	scanf("%d",&T);
	while(T--)
	{
		memset(pre,0,sizeof(pre));
		scanf("%d%d",&n,&k);
		int flag=1;
		for(int i=1;i<=n;i++)
		{
			scanf("%d",&a[i]);
			b[i]=a[i];
			if(a[i]<1||a[i]>k)flag=0;
		}
		if(!flag)
		{
			printf("NO\n");
			continue;
		}
		if(k>n)//若k太大需离散化
		{
			sort(b+1,b+1+n);
			int cnt=unique(b+1,b+1+n)-b-1;
			for(int i=1;i<=n;i++)a[i]=lower_bound(b+1,b+1+cnt,a[i])-b;
		}
		int now=1;
		for(int i=1;i<=n;i++)
		{
			while(!pre[a[now]]&&now<=n)pre[a[now++]]++;
			pre[a[i]]--;len[i]=now-i;
		}
		int cnt=min(k,len[1]+1),f1=0;
		for(int i=1;i<=cnt;i++)//枚举可能的起点
		{
			int f2=1;
			for(int j=i;j<=n;j+=k)
			{
				if(j+len[j]>n)continue;
				if(len[j]!=k)
				{
					f2=0;break;
				}
			}
			if(f2)
			{
				f1=1;break;
			}
		}
		if(f1)printf("YES\n");
		else printf("NO\n");
	}
	return 0;
}
```
---
```c++
# 离散+DP
#pragma warning (disable:4996)
#include <iostream>
#include <algorithm>
#include <iomanip>
#include <cstring>
#include <string>
#include <cstdio>
#include <cmath>
#include <vector>
#include <queue>
#include <list>
#include <set>
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

int a[maxn],b[maxn],cnt[maxn],lp[maxn];
bool vis[maxn];
int main()
{
	int t;
	scanf("%d",&t);
	while(t--){
		int n,k;
		int ok=1;
		scanf("%d%d",&n,&k);
		for(int i=1;i<=n;i++){
			scanf("%d",&a[i]);
			b[i]=a[i];
			if(a[i]>k)ok=0;
		}
		if(!ok){
			puts("NO");
			continue;
		}
		sort(b+1,b+1+n);
		
		for(int i=1;i<=n;i++)a[i]=lower_bound(b+1,b+1+n,a[i])-b;
		for(int i=1;i<=n;i++)cnt[i]=0;
		
		int l=1;
		for(int i=1;i<=n;i++){
			cnt[a[i]]++;
			while(cnt[a[i]]>1){
				cnt[a[l]]--;
				l++;
			}
			lp[i]=l;
		}
		ok=0;
		for(int i=1;i<=n;i++){
			if(i<=k){
				vis[i]=(lp[i]==1);
			}else{
				vis[i]=(vis[i-k]&(lp[i]<=i-k+1));
			}
			if(vis[i]&&lp[n]<=i+1){
				ok=1;
				break;
			}
		}
		puts(ok?"YES":"NO");
	}

 	return 0;
}
```






