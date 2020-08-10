## 牛客训练赛第七场 SUMMARY 08-01

### code

#### 牛客7_B gcd思维

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


