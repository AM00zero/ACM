## 牛客训练赛第四场 SUMMARY 07-20

### summary

- 提交代码前，自己需至少检查3-5min**测试检查无误**后再进行提交【0720.4】
- 如果代码提交TLE，最多在同样算法的时间复杂度下，按最优化的形式再提交至多**一次**，如果仍然TLE，必须在更换（改进优化）思路后，才继续提交，防止T爆家门【0720·4】

### code

#### 牛客4场 _f题  

```c++
ZYB has a so-called smart brain: He can always point out the keypoint in a complex problem.
There are two parallel lines AB and CD in a plane. A,B,C,D{A,B,C,D}A,B,C,D are all distinct points. You only know the Euclidean Distances between AC,AD,BC,BD{AC,AD,BC,BD}AC,AD,BC,BD. but you don’t know the exact order of points. (i.e. You don’t know whether it’s AB∥CDAB \parallel CDAB∥CD or AB∥DCAB \parallel DCAB∥DC).


Could you determine the order of points quickly, like the ZYB does?

输入描述:
The input contains multiple cases. The first line of the input contains a single integer T (1≤T≤100)T\ (1 \le T \le 100)T (1≤T≤100), the number of cases.
For each case, there are four integers a,b,c,d(1≤a,b,c,d≤1000)a,b,c,d(1 \le a,b,c,d \le 1000)a,b,c,d(1≤a,b,c,d≤1000) in a line, indicating the distances between AC,AD,BC,BD{AC,AD,BC,BD}AC,AD,BC,BD.It is guaranteed that each case corresponds to a valid solution.
输出描述:
For each case, output ‘AB//CD’ (Quotation marks) if AB∥CDAB \parallel CDAB∥CD, or output ‘AB//DC’ (Quotation marks) if AB∥DCAB \parallel DCAB∥DC.

示例1

输入

2
3 5 5 3
5 3 3 5


输出

AB//CD
AB//DC

```
```c++
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
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

int main()
{
 int t;
 scanf("%d", &t);
 while (t--)
 {
  int ac, ad, bc, bd;
  scanf("%d%d%d%d", &ac, &ad, &bc, &bd);
  bool ans;//1:CD, 0:DC
  if (ac < bc)//c左
  {
   if (ad < bd)//d左
   {
    if (bc > bd)
     ans = 1;
    else
     ans = 0;
   }
   else//d右
    ans = 1;
  }
  else//c右
  {
   if (ad < bd)//d左
    ans = 0;
   else//d右
   {
    if (ac < ad)
     ans = 1;
    else
     ans = 0;
   }
  }
  if (ans)
   printf("AB//CD\n");
  else
   printf("AB//DC\n");
 }
}

```

#### 牛客4场 _b题 

```c++
题目描述

As a great ACMer, ZYB is also good at math and number theory.
ZYB constructs a function fc(x)f_c(x)fc​(x) such that: . Give some positive integer pairs (ni,ci)(n_i, c_i)(ni​,ci​), ZYB wants to know fci(ni)mod  (109+7)f_{c_i}(n_i)\mod (10^9+7)fci​​(ni​)mod(109+7).
输入描述:
The input contains multiple test cases. The first line of input contains one integer T(1≤T≤106)T (1 \leq T \leq 10^6)T(1≤T≤106).In the following T{T}T lines, each line contains two integers ni,cin_i, c_ini​,ci​ (1≤ni,ci≤1061 \leq n_i, c_i \leq 10^61≤ni​,ci​≤106) describing one question.
输出描述:
For each test case, output one integer indicating the answer.

示例1

输入

2
3 3
10 5

输出

3
25
```
```c++
#pragma warning (disable:4996)
#include <algorithm>
#include <iostream>
#include <iomanip>
#include <cstring>
#include <string>
#include <cstdio>
#include <queue>
#include <stack>
#include <cmath>
#include <map>
#define inf 0X7f7f7f7f
#define MS_I(x) memset(x,-inf,sizeof(x))
#define MS(x) memset(x,0,sizeof(x))
#define MS_1(x) memset(x,-1,sizeof(x))
#define MSI(x) memset(x,inf,sizeof(x))
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

const int P = 1e9 + 7;
int n, c;
const int maxn = 1e7 + 10;

int prime[maxn];
int visit[maxn];
int Prime() {
    for (int i = 2; i <= maxn; i++) {
        if (!visit[i]) {
            prime[++prime[0]] = i;      //纪录素数， 这个prime[0] 相当于 cnt，用来计数
        }
        for (int j = 1; j <= prime[0] && i * prime[j] <= maxn; j++) {
            //            cout<<"  j = "<<j<<" prime["<<j<<"]"<<" = "<<prime[j]<<" i*prime[j] = "<<i*prime[j]<<endl;
            visit[i * prime[j]] = 1;
            if (i % prime[j] == 0) {
                break;
            }
        }
    }
    return prime[0];
}

int divide(int n, int inum)
{
    int times = 0;
    for (int i = 1; (prime[i] <= sqrt(n)) && (i < inum); i++)
    {
        while (n % prime[i] == 0)
            n /= prime[i], times++;// cout << "n:" << n << ends << "prime[i]:" << prime[i] << ends << i << "times:" << times << endl, getchar();
    }
    if (n > 1)
        times++;
    return times;
}

int qpow(int a, int b)
{
    int ans = 1;
    for (; b; b >>= 1)
    {
        if (b & 1)
            ans = (ll)ans * a % P;
        a = (ll)a * a % P;
    }
    return ans;
}

int main()
{
    int t;
    scanf("%d", &t);
    int num = Prime();
    while (t--)
    {
        scanf("%d %d", &n, &c);
        printf("%d\n", qpow(c, divide(n, num)) % P);
    }
    return 0;
}

```
