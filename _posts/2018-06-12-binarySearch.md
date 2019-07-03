---
layout:     post                    # 使用的布局（不需要改）
title:      乱七八糟的二分搜索        # 标题 
subtitle:   千万别信                 #副标题
date:       2018-06-12              # 时间
author:     canjuly                 # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - 二分搜索
    - 回收站
    - ACM
---
## 写在前面
> 我为啥要写这玩意？

刚写期末作业，从电脑里翻出一个叫二分搜索的文档，仔细看看发现是一年之前为了骗点津贴写的讲稿，后来似乎津贴也没了，遂不了了之。看了写了蛮多的，不舍得扔，索性放上来算了。

当然这篇讲稿写的思绪混乱，估计还有一些错误，不要太当真。

# 二分搜索——让你的搜索快人一步

众所周知，搜索是计算机非常重要的应用，如何从浩如烟海的数据中找到我们所要的那个数据并不是一件那么容易的事情，尤其是在我们现如今这个信息爆炸的时代。人工自然无法应对，所以如何使用计算机来快速地完成这件事，就显得非常重要了。

首先我们来想一下我们最普通的搜索算法：如何在一列无序的，元素各不相同的数中最快的查找到我们所想要找的那个数呢？显然的，因为这列数是无序的，他可能出现在数组中的任意位置，并且这个位置没有任何特征。所以我们除了遍历一遍整个数组之外别无选择。显然这样最坏的情况是要遍历整个数组，所以时间复杂度达到了O(n)。假如这个数组有一亿个元素的话，那么计算机按这种算法去处理的话，显然效率就是并不那么使人满意。

那么我们假如换一下条件，让我们原来的这个数组从无序改成升序（或者降序）呢？这个时候我们有没有更加快一点的方法去实现搜索呢？比如下面这个例子：

```
例1：  
现在有一个长度n=5的升序数组a{2,3,6,8,9}，我们要找到一个数k=8的位置。
```

假设现在我是一无所知的萌新，我自然会从头开始遍历，先遍历到2，发现不是我们要找的数，于是我们又遍历到3，发现也不是我们要找的数，于是继续遍历到6，还不是我们要找的数，继续遍历到8，发现就是我们需要的，于是结束，返回答案4，即8在数组中的位置是第四个。

很多人会想，这也太麻烦了，有序的数列还要这么找，那它的有序又有什么用呢？于是萌新的我决定试一试跳着找：我从头开始遍历，先遍历到2，发现不是我们要找的数，于是我决定跳一下，遍历后面第二个元素也就是6，还不是我们要找的数，继续跳一个遍历到9，发现比8大了，因为数组是升序的，所以我就往前找，然后就找到了8，于是返回答案4。这样就用到了升序这个条件，显然如果数组更大，那么我们现在这种做法就会比原来的方法快一倍。

跳一个既然可以，那我们为什么不跳两个，不跳三个呢？于是我们试了一下，发现都可以，而且效率也都提升了。但是这样跳着找又太不稳定了，我怎么知道到底跳几个才能让效率不那么慢？这时，我们的二分搜索就闪亮登场了。  

其实上面的那个方法已经有了一点二分的思想在里面，但是显得比较僵硬。二分搜索并不从头开始搜索，跳的步数也不是固定的。二分搜索先是将数组的中位数与关键字比较，如果二者相等，那么查找成功，那么查找成功，返回结果。否则根据中间位置将表分成前后两个新数组，如果中位数大于关键字，就进一步搜索前一子数组，如果中位数小于关键字就进一步查找后一子数组。重复这个操作，直到找到关键字为止。  

对于例1来说，我们就先拿8和数组的中位数6去比，发现8比6要大，于是我们就查找6后面的位置，即{8,9},这个数组的中位数是8，与关键字相同，于是我们返回8在原数组中的位置4。代码可以写出如下所示：  
```
#include<iostream>  
using namespace std;  
int a[100];  
int search(int left, int right, int key)  
{  
    int middle = (left + right) / 2;`  
	if (a[middle] == key)  
		return middle;  
	else if (a[middle] < key)  
		return search(middle + 1, right, key);  
	else  
		return search(left, middle - 1, key);  
}  
int main()  
{  
	int n;  
	cin >> n;  
	for (int i = 1; i <= n; i++)  
		cin >> a[i];  
	int k;  
	cin >> k;  
	cout << search(1, n, k) << endl;  
	return 0;  
}  
```
显然的，因为每次比较我们都将数据规模缩小了一半，所以二分查找的时间复杂度位O(log2(n))。这样就会使得效率非常的高。但是二分查找的缺点也是显而易见的：它无法适用无序的数组。
那么难道无序的数组还是用朴素的逐个比较吗？其实这也是看情况的。假如只有一次询问，那么逐个比较的时间复杂度是O(n)，而二分搜索需要先将数组排序再搜，所以时间复杂度位O(n*log2(n)+log2(n))，毫无疑问逐个比较是更优的方法，但是我们实际情况中，往往要处理不止一次询问，现在假设我们又q次询问，那么逐个比较的时间复杂度就变成了O(q*n)，而二分搜索的复杂度位O(n*log2(n)+q*log2(n))，因为log2(n)的增长是十分缓慢的，所以这时二分搜索的时间复杂度就比逐个比较优秀太多了。
来个例题吧：
```
例2，hznuoj-2053：

Description

 给你n个整型，你需要从小到大排序，再给你m个整型ai，请输出在排序完的数列中最后一个ai的下标。（数据保证这些数字出现过）

Input

第一行输入一个数字n (1<=n<=2*10^6)
第二行n个整型
第三行输入一个数字m (0<=m<=10^6)
接下来m行每行1个整型

Output

第一行输出排列好的数字。
之后m行，每行输出一个数字代表答案。

Samples

input:
10
10 9 8 7 6 5 4 3 2 1
3
10
6
1
output:
1 2 3 4 5 6 7 8 9 10
10
6
1

```
这道题在短学期的时候也是难倒了很多人啊，一个个在问到底哪里有死循环了为什么老是time limit exceed。其实这道题超出时间限制完全不是你代码打错了，而是你的算法不够快（当然出题人也很好心的在下面提示了）。且不论排序的时间，单说查找按传统逐个遍历的话就是O(m*n)的复杂度，也就是从会达到10^12之多，而题目只给了5s，显然是不行的。这个时候显然我们就应该用二分搜索。首先用qsort或者sort将数组在O(n*log2(n))的时间内排好序，然后裸套一个二分搜索就ok了，最后大概时间应该是只花了两秒半，快了不少。  

代码基本如下：
```c
#include<iostream>
#include<algorithm>
#include<cstdio>
using namespace std;

int a[2000002];
int search(int left, int right, int key)
{
	if (left >= right)
		return left;
	int middle = left + (right - left + 1) / 2;
	if (a[middle] <= key)
		return search(middle, right, key);
	else
		return search(left, middle - 1, key);
}
int main()
{
	int n;
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		scanf("%d", &a[i]);
	sort(a + 1, a + n + 1);
	for (int i = 1; i <= n; i++)
	{
		if (i != 1)
			printf(" ");
		printf("%d", a[i]);
	}
	printf("\n");
	int k;
	scanf("%d", &k);
	while (k--)
	{
		int val;
		scanf("%d", &val);
		printf("%d\n", search(1, n, val));
	}
	return 0;
}
```
注意此时用c++的cin和cout会导致超时，也注意一次下根据题目对二分查找函数的改编。  

当然其实在c++的STL中提供了二分搜索的函数，即lower_bound()和upper_bound()。其中lower_bound()函数在first和last的前开后闭的区间进行二分查找返回大于或等于val的第一个元素的位置，若没有则返回last的位置。而upper_bound()与lower_bound()相似，只是查找第一个大于val的元素的位置。举个例子讲的更清楚一点吧：有长度n=8的数组a{2,3,3,5,9,11,11,13}，那么我们想要查找5的第一次出现的位置，就要这么调用函数：lower_bound(a,a+n,5)-a,此时返回的是3（注意数组下标是从0开始的），也就是5的位置。同理我们用lower_bound()去查找11，那么返回的就是5，而不是6。加入我们用upper_bound()去找5，那么我们返回的就是4，即第一个大于5的元素的下标。而用upper_bound()来查找11的话返回的就是7了。  

现在我们有了lower_bound()和upper_bound(),代码也就简洁多了，刚才的题目代码就可以简化成如下所示：
```c
#include<iostream>
#include<algorithm>
#include<cstdio>
using namespace std;

int a[2000002];
int main()
{
	int n;
	scanf("%d", &n);
	for (int i = 1; i <= n; i++)
		scanf("%d", &a[i]);
	sort(a + 1, a + n + 1);
	for (int i = 1; i <= n; i++)
	{
		if (i != 1)
			printf(" ");
		printf("%d", a[i]);
	}
	printf("\n");
	int k;
	scanf("%d", &k);
	while (k--)
	{
		int val;
		scanf("%d", &val);
		printf("%d\n", upper_bound(a + 1, a + n + 1, val) - (a + 1));
	}
	return 0;
}

```
现在我们已经基本知道了二分搜索的原理以及该怎么去实现它。很多聪明的同学这时可能就会想：这太简单了！我之前就会。没错，二分搜索的原理的确非常简单且朴素，但是他真的仅仅只能用来搜索吗？

我们来看看下面这题
```
例3，poj-1064：
描述：
有N条绳子，他们长度分别位Li。如果从他们中切割出K条长度相同的绳子的话，这K条绳子每条最长能有多长？答案保留到小数点后2位。
限制条件：
1<=N<=10000
1<=K<=10000
1<=Li<=100000
样例：
输入
N = 4
K = 11
L = {8.02,  7.43,  4.57,  5.39}
输出
2.00
```
这道题看起来不难吧，想一想怎么做吧。

这下传统的枚举可是彻底的失效了，数据可不是123这样的整数，它是浮点数啊，你每次加0.00001这样去弄那不知道得多慢。

这事可能有机智的同学又发现了：这和二分搜索好像啊，不也是遍历太慢没法解决的问题吗？没错，我们换个方向思考：现在我们有所有长度的绳子，他们的长度已经按升序排好了序，我们要找的不就是满足条件的最长的那根绳子嘛。这不就是二分搜索？代码也很容易就可以写出来了吧（因为题目样例有点问题，所以只能写个伪代码）：
```c
int n, k;
double a[10002];

bool C(double x)
{
	int num = 0;
	for (int i = 0; i < n; i++)
	{
		num += (int)(a[i] / x);
	}
	return num >= k;
}
void solve()
{
	double lb = 0, ub = 999999;
	for (int i = 0; i < 100; i++)
	{
		double mid = (lb + ub) / 2;
		if (C(mid))
			lb = mid;
		else
			ub = mid;
	}
	printf("%.2lf\n", ub);
}
```
没错，这就是二分搜索的一大应用：判断某个解是否可行，或者更常见的，我们称它为叫二分答案。我们再看看二分答案的另外一道例题。
```
例4：poj-2456
描述
农夫约翰搭了一间有N (2 <= N <= 100,000)间牛舍的小屋。第i号牛舍在xi的位置(0 <= xi <= 1,000,000,000)。
但是他的C (2 <= C <= N)头牛对小屋很不满意，因此经常相互攻击。约翰为了防止牛之间相互伤害，因此决定把每头牛都放在离其他牛尽可能远的牛舍。也就是要最大化两头牛之间的距离。

Input
* 第1行: 由空格分隔的两个数: N和C 
* 第2到N+1行: 第i+1行包含了一个数xi，代表i号牛舍的位置

Output
* 第1行: 一个数: 最大的距离。

Sample Input
5 3
1
2
8
4
9
Sample Output
3
```
类似的这种最大化最小值或者最小化最大值的问题，都可以用二分搜索法很好的解决。我们定义
C(d)=可以安排牛的位置使得最近的两头牛的距离不小于d

那么问题就变成了求满足C(d)的最大d。另外，最近的间距不小于d也可以说成是所有牛的间距都不小于d，因此就有

C(d)=可以安排牛的位置使得任意牛的距离不小于d

这个问题的判断使用贪心法便可以非常容易的求解。

1.对牛舍的位置xi进行排序

2.把第一头牛放入x0的牛舍

3.如果第i头牛放入了xj的话，第i+1头牛就要放入满足xj+d<=xk的最小的xk中

对x的排序只需在一开始时进行一次就可以了，每一次判断对每头牛最多进行一次处理，因此复杂度是O(n)。

代码也是非常的简洁明了：
```c
#include<stdio.h>  
#include<algorithm>  
using namespace std;
const int N = 100005;
int a[N], n, c;

bool judge(int x)
{
	int cnt = 1, tmp = a[0];
	for (int i = 1; i < n; i++)
	{
		if (a[i] - tmp >= x)
		{
			cnt++;
			tmp = a[i];
			if (cnt >= c) //可以放下C头牛  
				return true;
		}
	}
	return false;
}

int get_ans() //二分搜索最小值  
{
	int l = 0, r = a[n - 1] - a[0];
	while (l <= r)
	{
		int mid = (l + r) / 2;
		if (judge(mid))
			l = mid + 1;
		else
			r = mid - 1;
	}
	return l - 1;
}

int main()
{
	while (~scanf("%d%d", &n, &c))
	{
		for (int i = 0; i < n; i++)
			scanf("%d", &a[i]);
		sort(a, a + n);
		printf("%d\n", get_ans());
	}
	return 0;
}
```
再来整理一下二分答案这个算法吧：

算法思想：

以求最小值的最大值（最小值最大化）为例，尝试一个可能的答案，如果这个答案符合题目条件，那么它肯定是“最小”（可行解），但不一定是“最大”（最优解），然后我们换个更大的可能答案，如果也符合条件，那这个新可行解就更优，不断重复即可。怎么找呢？这时就该二分上场了。

二分前提：

1.答案区间上下限确定，即最终答案在哪个范围是容易知道的。

2.检验某值是否可行是个简单活，即给你个值，你能很容易的判断是不是符合题目要求。

3.可行解满足区间单调性，即若x是可行解，则在答案区间内x+1（也可能是x-1）也可行。

两种情况：

下图中L,R为当前答案区间，M为中心点，根据二分思想判断M是否符合条件，再移动L或R，变成L'，R'，图中的T和F表示是否符合条件。

二分答案只是众多二分搜索应用中的一种，我们再看看其他的应用。

也是先放一道例题吧。
```
例5：
描述：
有n个物品的重量和价值分别是wi和vi。从中选出k个物品使得单位重量的价值最大。
限制条件：
1<=k<=N<=10000
1<=wi,vi<=1000000
样例：
输入
N = 3
K = 2
(w , v) = {(2 , 2) ,  (5 , 3) ,  (2 , 1)}
输出
0.75
```
值得注意的是，样例中答案选取的是0号和2号物品，平均价值是（2+1）/（2+2）=0.75。而我们按照一般方法去想的话，可能是先把物品按单位价值进行排序，从大到小贪心进行选取。但是这个方法对于样例输入得到的结果是5/7=0.714.所以这个方法是不可行的。那么应该如何求解呢？

实际上，对于这个问题使用二分搜索法可以很好的解决。我们定义条件C(x)=可以选择使得单位重量的价值不小于x

因此，原问题就变成了求满足C(x)的最大值x。那么怎么判断C(x)是否可行呢？假设我们选了某个物品的集合S，那么它们的单位重量价值是

  /   
因此就变成了判别是否存在S满足下面条件

  /    >= x
把这个不等式变形就得到

  >= 0

因此可以对 的值进行排序贪心的进行选取。因此就变成了C(x)= 从大到小排列中的前k个的和不小于0

每次判断的复杂度是O(log2(n))。

代码基本如下：
```c
#include <iostream>  
#include <cmath>  
#include <algorithm>  
#include <cstdio>  
using namespace std;

#define maxn 10010  
double w[maxn], v[maxn], c[maxn];
int n, k;
bool ok(double ans)
{
	for (int i = 0; i<n; i++)
		c[i] = v[i] - ans*w[i];
	sort(c, c + n);
	double cnt = 0;
	for (int i = 0; i<k; i++)
		cnt += c[n - 1 - i];
	return cnt >= 0;
}
int main()
{
	scanf("%d%d", &n, &k);
	for (int i = 0; i<n; i++)
		scanf("%lf%lf", &w[i], &v[i]);
	double l = 0, r = 1000010;
	while (abs(l - r)>1e-6)
	{
		double mid = (l + r) / 2;
		if (ok(mid))
			l = mid;
		else
			r = mid;
	}
	printf("%.2lf\n", r);
	return 0;
}
```