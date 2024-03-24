+++
title = "[note]可持久化权值线段树（主席树）算法学习笔记" 
date = 2024-03-24T17:10:17+08:00
draft = false
hidden = false
tag = ["note","data_structure"]
slug = 'Segment_Tree_note'
+++

# 前言

学习参考资料：

[Bilibili C08 可持久化线段树（主席树）](https://www.bilibili.com/video/BV1PT411L7XQ/?share_source=copy_web&vd_source=2e98187f1ace1a8ba63370f264b433b5)

[可持久化线段树 - OI Wiki](https://oi-wiki.org/ds/persistent-seg/)

# 介绍

学习主席树，你需要先掌握：

- 线段树概念、写法

- 权值线段树

- 离散化

一道经典的问题是：给定 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "n")n个整数构成的序列 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "a")，将对于指定的闭区间 [l,r] 查询其区间内的第 ![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7 "k") k小值。

我们发现因为是k小值所以ST表失效无法使用，普通线段树也无法维护区间第k小的值，我们可以用**可持久化权值线段树（主席树**来解决这个问题。

# 算法内容

注意到，有个修饰词是**可持久化**，何为可持久化呢，意思就是随着时间变化尽管数据有变化，我依旧可以访问过去的数据内容，而可持久化一个关键概念就是创建的**版本**。



主席树的关键思想：保存每次修改数据时的**历史版本**，相当于n颗线段树的整合



这听上去似乎很奇怪，拿例题来说，假设现在我们已经有了1-3的数据，现在我想要1-2中第1小的数据，我们则可以回到第二个版本（尚未插入第三个数据的版本）来进行查询操作来获得答案。

那么，我们该怎么实现呢？

**注意，下面的写法已经是权值线段树，不了解的请先去学**

![](https://github.com/lihepei/scholarpei_blog/blob/main/scholarpei-blog/static/blog-picture/segment_tree_note/1.png?raw=true)

一种朴素的思想是每次修改数据的时候就建一个新的线段树，但是很明显这样会导致爆空间，因此，我们只考虑保存线段树中**得到修改**的部分，使用动态开点的方法，其他部分则继承上一个版本线段树的数据，对于每一个历史版本，我们还应该保存根节点的编号。

对此我们可以计算内存空间得到：初始建树2n-1节点，n次插入过程，每次插入最多插入logn+1个节点，节点总数为 **2n + n(logn + 1) = n(logn +3)**



## 建树

### 离散化

离散化的原因往往是我们只关心把一个值域很大但数据不多的数据保存下他们的大小关系而不在乎他们具体的值，因此对于这种情况我们可以用STL简洁实现离散化

```cpp
vector<int> a(n, 0);
	for (int i = 0; i < n; i++){
		cin >> a[i];
		id.push_back(a[i]);
	}
	sort(id.begin(), id.end());
	id.erase(unique(id.begin(), id.end()), id.end());
```

我们用id 的vector复制一遍a的数据内容，首先排序实现大小从小到大，然后利用unique函数可以将重复的数据移动到最后面并且返回一个指向第一个重复数据的指针，因此可以用erase将这个指针后面所有的数据删除，实现起来仅需要两行代码。

当我们需要用到离散化后数据所指向具体的值的时候，我们需要用二分以**logn**的时间复杂度查找数据，我们可以利用STL中的lower_bound()函数来实现：

```cpp
int getId(int num)
{
	return lower_bound(id.begin(), id.end(), num) - id.begin()        ;
}
```

(注意lower_bound()返回的是一个指针，不是第几项）

## 建树

```cpp
struct Node
{
	int son[2];
	int t;
}Tree[22 * N];
int root[N], idx;

#define ls(x) (Tree[x].son[0])
#define rs(x) (Tree[x].son[1])

void build(int l,int r,int &i)
{
	i = ++idx;
	if(l == r)
		return;
	int mid = (l + r) >> 1;
	build(l, mid,ls(i));
	build(mid + 1, r, rs(i));
}
```

```cpp
int main()
{
    ...
    build(1, id.size(), root[0]);
    ...
}
```

我们将建树本身的操作也看成构建一次新的线段树的过程，用动态开点并且引用的方法将节点位置确定，并且拓展至儿子两个区间。



## 插入

```cpp
for (int i = 0; i < a.size();i++){
		insert(1, id.size(), root[i], root[i + 1], getId(a[i])+1);
	}
```

```cpp
void insert(int l,int r,int x,int &y,int v)//x last y now
{
	y = ++idx;
	Tree[y] = Tree[x];
	Tree[y].t++;//add one
	if(l==r) return ;
	int mid = (l + r) >> 1;
	if(v <= mid)
		insert(l, mid, ls(x), ls(y), v);
	else
		insert(mid + 1, r, rs(x), rs(y), v);
	return;
}
```

这里的x是上一个版本的线段树，y是现在这个修改版本的线段树，将y设为引用类型是因为需要动态开点。首先我们通过`Tree[y] = Tree[x]`复制上一个版本的线段树，接着让修改的权值t加1，在下面的进入子节点的insert中，我们进入**需要修改的**子区间，其他部分保持不变，这样我们就实现了**修改区间动态开出新点，未改变区间继承上一个版本的信息**

一个需要留意的点是，我们插入的是离散化后的数据，需要用到getId这个函数。



## 询问

首先我们先思考：在主席树上如何寻找[l,r]的第k小？

可查询历史版本 -> 查询历史版本区间的数据 -> 利用**前缀和的思想**查询历史版本区间

求[l,r]上的第k小，我们只需要用第r个版本上的信息减去第l-1个版本上的信息，就是[l,r]区间上的数据了，再去查询第k小，就变成一个权值线段树的问题了。

```cpp
int main()
{
    ...
    int l, r, k;
	for (int i = 1; i <= m;i++){
		cin >> l >> r >> k;
		cout << id[Query(1, id.size(), root[l - 1], root[r], k)-1]<<endl;
	}
    ...
}
```

```cpp
int Query(int l,int r,int x,int y,int k)
{
	if(l==r) return l;
	int lt = Tree[ls(y)].t - Tree[ls(x)].t;
	int mid = (l + r) >> 1;
	if(k<=lt)
		return Query(l, mid, ls(x), ls(y), k);
	else
		return Query(mid + 1, r, rs(x), rs(y), k - lt);
}
```



# 完整代码与例题

例题链接：[【模板】可持久化线段树 2 - 洛谷](https://www.luogu.com.cn/problem/P3834)

完整代码：

```cpp
#include<iostream>
#include<fstream>
#include<vector>
#include<algorithm>
#include<iomanip>

using namespace std;

const int N = 2e5 + 5;
const int INF = 1 << 30;

int n, m;

vector<int> id;
int getId(int num)
{
	return lower_bound(id.begin(), id.end(), num) - id.begin();
}

struct Node
{
	int son[2];
	int t;
}Tree[22 * N];
int root[N], idx;

#define ls(x) (Tree[x].son[0])
#define rs(x) (Tree[x].son[1])

void build(int l,int r,int &i)
{
	i = ++idx;
	if(l == r)
		return;
	int mid = (l + r) >> 1;
	build(l, mid,ls(i));
	build(mid + 1, r, rs(i));
}

void insert(int l,int r,int x,int &y,int v)//x last y now
{
	y = ++idx;
	Tree[y] = Tree[x];//¼Ì³ÐÈ¨Öµ¡¢ÆäÖÐÒ»¸ö¶ù×Ó
	Tree[y].t++;//add one
	if(l==r) return ;
	int mid = (l + r) >> 1;
	if(v <= mid)
		insert(l, mid, ls(x), ls(y), v);
	else
		insert(mid + 1, r, rs(x), rs(y), v);
	return;
}

int Query(int l,int r,int x,int y,int k)
{
	if(l==r) return l;
	int lt = Tree[ls(y)].t - Tree[ls(x)].t;
	int mid = (l + r) >> 1;
	if(k<=lt)
		return Query(l, mid, ls(x), ls(y), k);
	else
		return Query(mid + 1, r, rs(x), rs(y), k - lt);
}

int main()
{
	cin >> n >> m;
	vector<int> a(n, 0);
	for (int i = 0; i < n; i++){
		cin >> a[i];
		id.push_back(a[i]);
	}
	sort(id.begin(), id.end());
	id.erase(unique(id.begin(), id.end()), id.end());
	//ÀëÉ¢»¯

	build(1, id.size(), root[0]); //build empty tree

	for (int i = 0; i < a.size();i++){
		insert(1, id.size(), root[i], root[i + 1], getId(a[i])+1);
	}

	int l, r, k;
	for (int i = 1; i <= m;i++){
		cin >> l >> r >> k;
		cout << id[Query(1, id.size(), root[l - 1], root[r], k)-1]<<endl;
	}

	return 0;
}
```

