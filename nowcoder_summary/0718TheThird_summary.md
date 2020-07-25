## 牛客训练赛第三场 SUMMARY 07-18

### summary

- 提交代码前，，若有一起做题的队友，**和队友沟通确认无误**，经过系统和队友的样例AC后，再提交，减少不必要的罚时。【0718·3】
- 及时抛弃难以克服的题目，哪怕看上去结构模式似乎简单，实际难度应主要参考其**通过人数**，优先攻克通过人数和通过率高的赛题，减少时间浪费。【0718·3】

### code

#### 牛客3场 _g题  并查集+链表

给一张图n点m边，一开始每个点颜色不同，共n种颜色。后接q次染色操作，每次给出一个点（给出一种颜色），如果该点（集合）颜色没被改变过，由该点（集合）出发把与其相连的点（集合）染成相同颜色，求q次操作后结果。**开始时当然按点理解，当开始合并后，两个集合相遇是要把整个集合都染色的。**

难点在于对每个点都维护一个链表，存储i号颜色集合里**尚未对周围染色的点**。对于每次操作的节点u，如果该点已被染色（find(x)!=x）则跳过。否则遍历u的链表，从链表中节点t开始bfs（这一步看作是让颜色u**集合中每个点都尝试向外染色**），对于与t相连的v点，如果v还未被染色，用并查集合并进u的集合，再把v的链表拼接到u下，这样下次再对颜色u操作时，就可以**从原本颜色v的点开始**向外染色了。

```c++
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

#### 牛客3场 _l题 
```c++
题目描述

Dreamoon loves lovely things, like lovely girls, lovely bed sheets, lovely clothes…
So he thinks a string is lovely if and only if it begins with the word “lovely”(case insensitive). You are given a string. Please tell us whether Dreamoon thinks that string is lovely.

输入描述:
The input contains only one line.
This line contains a string s ss. The length of s ss is between 8 88 and 10 1010.
$ s$ consists of only the English alphabet. It can be lower case or upper case.
输出描述:
If a string is lovely, please output “lovely”, Otherwise, output “ugly”. (The output is case sensitive).

示例1
输入

LovelyAA

输出

lovely

示例2
输入

lovelymoon

输出

lovely

示例3
输入

NOWCOWDER

输出

ugly

示例4
输入

LoveLive

输出

ugly

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
#include <set>
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

int main()
{
    string a;
 cin >> a;
 if (a[0] == 'l' || a[0] == 'L')
  if (a[1] == 'o' || a[1] == 'O')
   if (a[2] == 'v' || a[2] == 'V')
    if (a[3] == 'e' || a[3] == 'E')
     if (a[4] == 'l' || a[4] == 'L')
      if (a[5] == 'y' || a[5] == 'Y')
      {
       cout << "lovely";
       return 0;
      }
 cout << "ugly";
}

```
#### 牛客3场 _B题  

```c++
64bit IO Format: %lld

题目描述

Given a string S consists of lower case letters. You’re going to perform Q operations one by one. Each operation can be one of the following two types:

Modify: Given an integer x. You need to modify S according to the value of x. If x is positive, move the leftmost x letters in S to the right side of S; otherwise, move the rightmost |x| letters in S to the left side of S. Answer: Given a positive integer x. Please answer what the x-th letter in the current string S is.

输入描述:
There are Q+2 lines in the input. The first line of the input contains the string S. The second line contains the integer Q. The following Q lines each denotes an operation. You need to follow the order in the input when performing those operations.

Each operation in the input is represented by a character c and an integer x. If c = ‘M’, this operation is a modify operation, that is, to rearrange S according to the value of x; if c = ‘A’, this operation is an answer operation, to answer what the x-th letter in the current string S is.

• 2≤∣S∣≤2×1062 \le |S| \le 2 \times 10^62≤∣S∣≤2×106 (|S| stands for the length of the string S)
• S consists of lower case letters
• 1≤Q≤8×1051 \le Q \le 8 \times 10^51≤Q≤8×105
• c = ‘M’ or ‘A’
• If c = ‘M’, 1≤∣x∣<∣S∣1 \le |x| < |S|1≤∣x∣<∣S∣
• If c = ‘A’, 1≤x≤∣S∣1 \le x \le |S|1≤x≤∣S∣
• There is at least one operation in the input satisfies c = ‘A’

输出描述:
For each answer operation, please output a letter in a separate line representing the answer to the operation. The order of the output should match the order of the operations in the input.


示例1

输入

nowcoder
6
A 1
M 4
A 6
M -3
M 1
A 1

输出
n
o
w

备注:
Initially, S is ‘nowcoder’, six operations follow.

• The 1-st operation is asking what the 1-st letter is. The answer is ‘n’.
• The 2-nd operation is to move the leftmost 4 letters to the rightmost side, so S is modified to ‘odernowc’.
• The 3-rd operation is asking what the 6-th letter is. The answer is ‘o’.
• The 4-th operation is to move the rightmost 3 letters to the leftmost side, so S is modified to ‘owcodern’.
• The 5-th operation is to move the leftmost 1 letter to the rightmost side, so S is modified to ‘wcoderno’.
• The 6-th operation is asking what the 1-st letter is. The answer is ‘w’

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
#include <set>
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

const int maxn = 1e6 + 20;
char c;
int q, x;

char s[maxn];
int num[maxn];
int nextt[maxn];
int head, tail;

int main()
{
 cin >> s;
    head = 0;
 tail = strlen(s) - 1;
 for (int i = 0; i < strlen(s) - 1; i++)
  nextt[i] = i + 1;
 scanf("%d", &q);
 while (q--)
 {
  cin >> c;
  if (c == 'M')
  {
   scanf("%d", &x);
   if (x > 0)
   {
    //s = s.substr(x) + s.substr(0, x);
    int tmp = (head + x - 1) % strlen(s);
    tail = tmp;
    head = nextt[tmp];
   }
   else
   {
    x = -x;
    //s = s.substr(s.length() - x) + s.substr(0, s.length() - x);
    int tmp = (head + strlen(s) - x - 1) % strlen(s);
    tail = tmp;
    head = nextt[tmp];
   }
  }
  if (c == 'A')
  {
   scanf("%d", &x);
   int tmp = (head + x - 1) % strlen(s);
   cout << s[tmp] << endl;
  }
 }
}

```

#### 牛客3场 _A题 

```c++
题目描述

There is a fishing game as following: The game contains nnn stages, numbered from 111 to nnn. There are four types of stages (numbered from 000 to 333): type 000: There are no fish and no clam in this stage. type 111: There are no fish and one clam in this stage. type 222: There are one fish and no clam in this stage. type 333: There are one fish and one clam in this stage. In each stage, you can do exactly one of the following four actions. If there is a clam in the stage, you can use this clam to make one pack of fish bait. And the number of packs of fish bait you have is increased by one. You can use this pack of fish bait to catch fish after this stage. If there is one fish in the stage, you can catch this fish without any fish bait. After this stage, the number of packs of fish bait you have is not changed. If you have at least one pack of fish bait. You can always catch one fish by using exactly one pack of fish bait even if there are no fish in this stage. After this stage, the number of packs of fish bait you have is decreased by one. You can do nothing. Now, you are given nnn and the type of each stage. Please calculate the largest number of fish you can get in the fishing game.

输入描述:
The first line contains one integer t (1≤t≤2.5×1051 \le t \le 2.5 \times 10^51≤t≤2.5×105) — the number of test cases.

There are two lines in each test. The first line contains one integer n (1≤n≤2×1061 \le n \le 2 \times 10^61≤n≤2×106), indicating the number of stages in this game. The second line contains a string with length n. The i-th character of this string indicates the type of the i-th stage.

The sum of n across the test cases doesn’t exceed 2×1062 \times 10^62×106.

输出描述:
For each test case print exactly one integer — the maximum number of fish you can catch in the game configuration.

示例1

输入

2
4
0103
1
1

输出

2
0


备注:
One possible scenario you can catch two fishes is as follow:

stage 1: Do nothing.
stage 2: Make a pack of fish bait.
stage 3: Catch a fish by a pack of fish bait.
stage 4: Catch the fish that appears in the stage.
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
#include <set>
#define inf 0X3f3f3f3f
using namespace std;
typedef long long ll;
typedef unsigned long long ull;

const int maxn = 2e6 + 20;
string s;
int back[maxn];

int main()
{
 int t;
 scanf("%d", &t);
 while (t--)
 {
  int n;
  int ans = 0;
  scanf("%d", &n);
  back[n] = 0;
  cin >> s;
  for (int i = n - 1; i >= 0; i--)
   if (s[i] == '0')
    back[i] = back[i + 1] + 1;
   else
    back[i] = back[i + 1];
  int bait = 0;
  for (int i = 0; i < n; i++)
  {
   if (s[i] == '2' || s[i] == '3')
    ans++;
   else if (s[i] == '0')
   {
    if (bait == 0)
     continue;
    else
     ans++, bait--;
   }
   else
   {
    if (back[i] >= bait)
     bait++;
    else if (bait == 0)
     continue;
    else
     ans++, bait--;
   }
  }
  printf("%d\n", ans);
 }
}


```
