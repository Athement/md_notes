## [线段树解题思路](https://www.cnblogs.com/xenny/p/9801703.html)

**问题：**

有一个数组`a`，下标从`0`到`n-1`，现在给你`w`次修改，`q`次查询，修改的话是修改数组中某一个元素的值；查询的话是查询数组中任意一个区间的和，`w + q < 500000`。

**解决方案：**

> 1. 通常做法：修改是O(1)的时间复杂度，查询是O(*n*)的复杂度，总体时间复杂度为O(q*n)
>
> 2. 前缀树：使用前缀和优化查询，查询是O(1)复杂度；而修改时，之后的所有前缀和都要更新，所以修改的时间复杂度是O(n)。

以上两种做法，要么查询是O(1)，修改是O(n)；要么修改是O(1)，查询是O(n)。

可通过线段树减低整体时间复杂度。

### 线段树概念

**线段树结构**

线段树是一种二叉搜索树，每个结点都存储了一个区间。

**线段树作用**

线段树可以在线维护修改、查询区间上的最值，求和。

一维线段树可以扩充到二维线段树（矩阵树）和三维线段树（空间树）。

对于一维线段树来说，每次更新以及查询的时间复杂度为O(logN)

**线段树和其他RMQ算法的区别**

常用的解决RMQ问题有**线段树算法**和**稀疏表（ST)算法**

*相同点*

> 二者预处理时间都是O(NlogN)

*不同点*

> ST算法的单次查询操作是O(1)，而线段树算法查询操作是O(logN)
>
> 线段树支持在线更新值，而ST算法不支持在线操作

### 线段树操作

线段树常用的操作包括build，pushUp，query，update，pushDown

> build用于构建线段树，通常是一个递归的过程
>
> pushUp用于处理父节点，通常采用后序遍历，其处理结果依赖于子节点
>
> query用于查询子区间的值
>
> update用于更新指定索引的值，同时更新相关的线段树节点，需要pushUp来更新父节点
>
> pushDown一般用于区间更新，将区间子节点的更新延迟到子节点查询

**问题描述**

对于数组arr={1,8,6,4,3,5}，使用线段树获取指定区间最大值。

<img src="assets/image-20210209135653260.png" alt="image-20210209135653260" style="zoom: 50%;" />

**build建立线段树**

```java
const int maxn = 100005;
int a[maxn],t[maxn<<2];        //a为原来区间，t为线段树

void pushup(int k){        //更新函数，这里是实现最大值 ，同理可以变成，最小值，区间和等
    t[k] = max(t[k<<1],t[k<<1|1]);
}

//递归方式建树 build(1,1,n);
void build(int k,int l,int r){    //k为当前需要建立的结点，l为当前需要建立区间的左端点，r则为右端点
    if(l == r)    //左端点等于右端点，即为叶子节点，直接赋值即可
        t	[k] = a[l];
    else{
        int m = (r+l)>>1;    //m则为中间点，左儿子的结点区间为[l,m],右儿子的结点区间为[m+1,r]
        build(k<<1,l,m);    //递归构造左儿子结点
        build(k<<1|1,m+1,r);    //递归构造右儿子结点
        pushup(k);    //更新父节点
    }
}
```

代码分析：

> 代码始位置，表示左子树和右子树更简便。
>
> 以1为起始位置，表示左子树和右子树更简便。
>
> ×/÷2都采用位运算，提高计算效率
>
> 更新父节点使用**后序处理**，因为依赖于子节点的值（取子节点最大值）
>
> 取l,r中点时，一般采用（r+l)/2，但l+r可能超出边界，使用l+(l-r)/2更安全。

**query查询区间值**

```java
//递归方式区间查询 query(L,R,1,n,1);
int query(int L,int R,int l,int r,int k){    //[L,R]即为要查询的区间，l，r为结点区间，k为结点下标
    if(L <= l && r <= R)    //如果当前结点的区间真包含于要查询的区间内，则返回结点信息且不需要往下递归
        return t[k];
    else{
        int res = -INF;    //返回值变量，根据具体线段树查询的什么而自定义
        int mid = (r+l)>>1;    //m则为中间点，左儿子的结点区间为[l,m],右儿子的结点区间为[m+1,r]
        if(L <= m)    //如果左子树和需要查询的区间交集非空
            res = max(res, query(L,R,l,m,k<<1));
        if(R > m)    //如果右子树和需要查询的区间交集非空，注意这里不是else if，因为查询区间可能同时和左右区间都有交集
            res = max(res, query(L,R,m+1,r,k<<1|1));
        return res;    //返回当前结点得到的信息
    }
}
```

代码分析：

> 从[l,r]区间中查询[L,R]区间的值，包含以下三个步骤
>
> 1. [l,r]⊆[L,R]，直接返回线段树结果
> 2. 将[l,r]拆分，直到[l,r]⊆[L,R]
> 3. 对多个子结果进行归并处理(求和/求最值等)

**update点单值**

```java
//递归方式更新 updata(p,v,1,n,1);
void updata(int p,int v,int l,int r,int k){    //p为下标，v为要加上的值，l，r为结点区间，k为结点下标
    if(l == r)    //左端点等于右端点，即为叶子结点，直接加上v即可
        a[k] += v,t[k] += v;    //原数组和线段树数组都得到更新
    else{
        int m = l + ((r-l)>>1);    //m则为中间点，左儿子的结点区间为[l,m],右儿子的结点区间为[m+1,r]
        if(p <= m)    //如果需要更新的结点在左子树区间
            updata(p,v,l,m,k<<1);
        else    //如果需要更新的结点在右子树区间
            updata(p,v,m+1,r,k<<1|1);
        pushup(k);    //更新父节点的值
    }
}
```

代码分析：

> 点更新类似二叉搜索树的搜索过程，不过更新子节点后需要通过pushUp对父节点进行同步更新

**update区间更新**

```java
void Pushdown(int k){    //更新子树的lazy值，这里是RMQ的函数，要实现区间和等则需要修改函数内容
    if(lazy[k]！=0){    //如果有lazy标记
        lazy[k<<1] += lazy[k];    //更新左子树的lazy值
        lazy[k<<1|1] += lazy[k];    //更新右子树的lazy值
        t[k<<1] += lazy[k];        //左子树的最值加上lazy值
        t[k<<1|1] += lazy[k];    //右子树的最值加上lazy值
        lazy[k] = 0;    //lazy值归0
    }
}

//递归更新区间 updata(L,R,v,1,n,1);
void updata(int L,int R,int v,int l,int r,int k){    //[L,R]即为要更新的区间，l，r为结点区间，k为结点下标
    if(L <= l && r <= R){    //如果当前结点的区间真包含于要更新的区间内
        lazy[k] += v;    //懒惰标记
        t[k] += v;    //最大值加上v之后，此区间的最大值也肯定是加v
    }
    else{
        Pushdown(k);    //重难点，查询lazy标记，更新子树
        int m = l + ((r-l)>>1);
        if(L <= m)    //如果左子树和需要更新的区间交集非空
            update(L,R,v,l,m,k<<1);
        if(m < R)    //如果右子树和需要更新的区间交集非空
            update(L,R,v,m+1,r,k<<1|1);
        pushup(k);    //更新父节点
    }
}
```

lazy标签的作用：

> 对于一个区间[L,R]来说，我们可能每次都更新区间中的每个值，那样的话更新的复杂度将会是O(NlogN)。
>
> 为了降低区间更新的复杂度，线段树将每个值的更新延迟至查询该节点的时机(**延迟更新不影响使用**)，并且没有增加查询的时间复杂度

pushDown作用：

> pushDown用于将当前节点的lazy标签向下传递，并重置当前节点的lazy标签

update分析：

> 区间更新是**区间查询**与**点更新**的结合
>
> 1. [l,r]⊆[L,R]，更新结果并设置lazy标签
>
> 2. 更新区间也是对区间的一次查询，先使用pushDown完成lazy标签向下传递，解决了多次更新一次查询可能引发的问题
>
>    之后将[l,r]拆分，直到[l,r]⊆[L,R]。
>
> 3. 更新父节点。

query代码加入pushDown更新操作

```java
//递归方式区间查询 query(L,R,1,n,1);
int query(int L,int R,int l,int r,int k){    //[L,R]即为要查询的区间，l，r为结点区间，k为结点下标
    if(L <= l && r <= R)    //如果当前结点的区间真包含于要查询的区间内，则返回结点信息且不需要往下递归
        return t[k];
    else{
        Pushdown(k);    /**每次都需要更新子树的Lazy标记*/
        int res = -INF;    //返回值变量，根据具体线段树查询的什么而自定义
        int mid = l + ((r-l)>>1);    //m则为中间点，左儿子的结点区间为[l,m],右儿子的结点区间为[m+1,r]
        if(L <= m)    //如果左子树和需要查询的区间交集非空
            res = max(res, query(L,R,l,m,k<<1));
        if(R > m)    //如果右子树和需要查询的区间交集非空，注意这里不是else if，因为查询区间可能同时和左右区间都有交集
            res = max(res, query(L,R,m+1,r,k<<1|1));

        return res;    //返回当前结点得到的信息
    }
}
```

query分析：

> 在查询子区间前，先判断是否带有lazy标签，若有则需先更新子节点再进行下一步操作。

## [树状数组](https://blog.csdn.net/FlushHip/article/details/79165701)

### 树状数组概念

线段树最多需要4*n个空间来构建一棵线段树，其中一部分空间被浪费。可利用树状数组可降低空间使用。

**树状数组结构与性质**

<img src="assets/b720737e6735c96e30b9e2dc52dd3820.png" alt="这里写图片描述" style="zoom:67%;" />

> 树状数组规定，对于arr数组，c数组保留了arr部分索引位置的和；
>
> c数组的索引从1开始，对应arr数组的索引0
>
> 对于c数组中的索引i，lowbit(i)取出i的零头*（二进制，lowbit(110)=10,lowbit(1000)=1000）*；
>
> c[i]表示arr中第i-lowbit(i)+1位到i的和。

树状数组实现了O(N)空间复杂度**（常数项为1）**，区间查询和单点更新时间复杂度为O(logN)。

### 树状数组操作

**取零操作**

```java
int lowbit(int x){
	return x&(-x)
}
```

**查询前缀和**

```java
int sum(int x, int[] c, int n)
{
    int ret = 0;
    for ( ; x > 0; ret += c[x], x -= lowbit(x));
    return ret;
}
```

**单点更新**

```java
void update(int x, int val, int[] c)
{
    for ( ; x < c.length; c[x] += val, x += lowbit(x));
}
```

**构建树状数组**

```java
int[] build(int[] arr){
	int[] c=new int[arr.length+1];
    for(int i=1;i<c.length;i++){
        update(i,arr[i-1],c);
    }
}
```

### 树状数组与线段树的异同

**同**

> 区间和查询和单点更新的时间复杂度为O(logN)
>
> 空间复杂度为O(N)

**异**

> 线段树的空间利用率低于树状数组

**运用场景**

使用树状数组的场景都能使用线段树。

差异详见：

<img src="assets/image-20210215225948028.png" alt="image-20210215225948028" style="zoom:67%;" />

## 问题记录

### 307 区间求和

**问题描述**

<img src="assets/image-20210209162127071.png" alt="image-20210209162127071" style="zoom:67%;" />

**解题思路(线段树）**

1. 构建线段树，pushUp采用t[k]=t[k<<1]+t[k<<1|1]
2. 在query中，res要分别加上左右子树的值

**解题思路（数组）**

使用sum数组统计从开始到当前索引的和，如sum[i]=arr[0]+...+arr[i].

range[i,j]=sum[j]-sum[i]+arr[i];

### 218 天际线

**问题描述**
![image-20210209171129481](assets/image-20210209171129481.png)

### 315 统计逆序对

**问题描述**

<img src="assets/image-20210217164448261.png" alt="image-20210217164448261" style="zoom:50%;" />

**[解题思路](https://blog.csdn.net/m0_38033475/article/details/80330157)**

1. 将数组的值与索引绑定，并对数组的值进行排序。

   <img src="assets/image-20210217165015287.png" alt="image-20210217165015287" style="zoom:50%;" />

2. 根据排序后的索引，对arr（树状数组）进行赋值。并统计之后的空位数，即在原序列中小于该索引对应值并排在之后的数目。

   <img src="assets/image-20210217170059135.png" alt="image-20210217170059135" style="zoom:67%;" />
