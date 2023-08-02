---
layout:     post
title:      Changersh的算法模板
subtitle:   粗浅整理
date:       2023-06-12
author:     Changersh
header-img: img/the-first.png
catalog:   true
tags:
    - 算法学习
---
Changersh的ACM模板

## 基础算法

### 排序

#### 快速排序

底层思想是分治

1. 确定分界值：q[l]  q[(l + r) / 2]  q[r]
2. 调整范围，<= 分界点的在左边  >= 分界点的 在右边
3. 递归处理

~~~java
private static void quick_sort(int[] q, int l, int r) {
    // l 和 r 是闭区间的边界
    if (l >= r) return; // 如果区间内只有一个或者没有数字，就返回
    int x = q[l + r >> 1]; // 分界点
    int i = l - 1, j = r + 1; // i j 取了区间的两端外，是为了配合下面的 do while
    while (i < j) {
        do i++; while (q[i] < x);
        do j--; while (q[j] > x);

        if (i < j) {
            int t = q[i];
            q[i] = q[j];
            q[j] = t;
        }
    }
    // 向左右子区间递归
    quick_sort(q, l, j);
    quick_sort(q, j + 1, r);
}
~~~

#### 归并排序

1. 找分界点
2. 递归排序，把左右两边分别递归排序
3. 归并——合二为一

最好用快读，不然会超

~~~java
private static final int N = 100000 + 10;
private static int[] q;
private static int[] tmp;
private static void merge_sort(int l, int r) {
    if (l >= r) return; // 边界情况
    int mid = l + r >> 1; // 判断分界点
    // 左右递归分治
    merge_sort(l, mid); merge_sort(mid + 1, r);
    // 归并
    int p = 0;
    int i = l, j = mid + 1;
    while (i <= mid && j <= r) {
        if (q[i] <= q[j]) tmp[p++] = q[i++];
        else tmp[p++] = q[j++];
    }
    // 将上面没有归并完的归并
    while (i <= mid) tmp[p++] = q[i++];
    while (j <= r) tmp[p++] = q[j++];

    // 排序后的再复制回原数组
    for (i = l, j = 0; i <= r; i++, j++) q[i] = tmp[j];
}
~~~



#### 冒泡排序

#### 选择排序

### 二分

#### 整数二分

lower_bound是大于等于某个数的最小下标

~~~java
> x  ==  >= (x + 1)
< x  ==  (>= x) - 1
<= x ==  (>= x + 1) - 1
~~~



~~~java
// 闭区间
private int lower_bound(int[] a, int target) {
    int n = a.length;
    int l = 0, r = n - 1;
    while (l <= r) {
        int mid = l + ((r - l) >> 1); // 防止溢出
        if (a[mid] >= target) r = mid - 1;
        else l = mid + 1;
    }
    return l; // 最后 r + 1 和 l 指向的是 第一个 >= target 的数
}


// 左闭右开 （左开右闭类似）
private int lower_bound(int[] a, int target) {
    int n = a.length;
    int l = 0, r = n; // r = n
    while (l < r) {
        int mid = l + ((r - l) >> 1); // 防止溢出
        if (a[mid] >= target) r = mid;
        else l = mid + 1;
    }
    return l; // 最后 r 和 l 指向的是 第一个 >= target 的数
}

// 开区间
private int lower_bound(int[] a, int target) {
    int n = a.length;
    int l = -1, r = n;
    while (l + 1 < r) {
        int mid = l + ((r - l) >> 1); // 防止溢出
        if (a[mid] >= target) r = mid;
        else l = mid;
    }
    return l + 1; // 最后 r 和 l + 1 指向的是 第一个 >= target 的数
}
~~~



即，按照某种性质来划分，一边是满足这个性质的，一边是不满足这个性质的

// 这个模板得不到 边界值

~~~java
boolean check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
// 求左边界点
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}



// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
// 求右边界点
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1; // +1 防止死循环 l == r - 1 时 r - 1 + r >> 1 = r - 1如果 l = mid  则 l = r - 1 死循环
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
~~~

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20221124154125982.png" alt="image-20221124154125982" style="zoom:80%;" />

第一个模板，求绿色部分的左端点

- 如果满足绿色部分，则端点在左侧，更新 r = mid， 区间 [l, mid]
- 不满足绿色部分，端点在右侧， l = mid + 1，区间 [mid + 1, r]

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20221124153754583.png" alt="image-20221124153754583" style="zoom:80%;" />

第2个模板，我们要满足**红色**性质，即求红色的最右边端点，先取当前区间的终点 mid，

==mid = l + r + 1 >> 1==，防止死循环，只要是 l = mid 就需要补上 + 1

- 如果满足性质，说明 mid 在红色区间，则我们要求的端点在右边，更新 l = mid，得到区间  [mid, r]
- 如果不满足性质，说明mid在绿色区间，则所求端点在其左侧，更新 r = mid - 1，得到区间 [l, mid - 1]





#### 浮点数二分

因为小数没有整除，是可以都可取到的，所以不用考虑边界问题，直接 = mid 即可

~~~java
boolean check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求，比题目要求保留的小数位精两位
    while (r - l > eps)
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
~~~

### 高精度

~~~java
// 使用 BigInteger
加：add;
减：subtract();
乘：multiply();
除：divide();
~~~



~~~c++
struct Bignum {
	int len,s[MAXN*2];
	Bignum() {
		memset(s,0,sizeof(s));
		len = 1;
	}
	Bignum operator +(const Bignum &A)const {
		Bignum B;
		B.len = max(len,A.len);
		for(int i = 0; i< B.len; i++) {
			B.s[i] =B.s[i] + A.s[i] + s[i];
			B.s[i+1] += B.s[i]/10;
			B.s[i] = B.s[i]%10;
		}
		if(B.s[B.len]) B.len ++;
		return B;
	}
	bool operator <(const Bignum &A)const {
		if(len != A.len) return len < A.len;
		for(int i= len-1; i >=0 ; i--) {
			if(s[i] != A.s[i]) return s[i] < A.s[i];
		}
		return false;
	}
	Bignum operator *(const Bignum &A)const {
		Bignum B;
		B.len = len+A.len-1;
		for(int i = 0; i< A.len; i++) {
			for(int j=0; j< len; j++) {
				B.s[i+j] =B.s[i+j] + A.s[i] * s[j];
				B.s[i+j+1] += B.s[i+j]/10;
				B.s[i+j] = B.s[i+j]%10;
			}
		}

		if(B.s[B.len]) B.len ++;
		return B;
	}
	void write() {
		int k = len-1;
		while(s[k]==0 && k >0) k--;
		for(int i = k; i>=0; i--)   cout << s[i];
	}
};
~~~



### 前缀和

#### 一维

前缀和：S[i] = a1 + a2 +....+ a[i]

**作用：**快速求出来一段数字的和，[l, r] = S[r] - S[l - 1]



1. **求 S[i]** ：直接遍历求和，S[i] = S[i - 1] + a[i]，所以为了方便，空出**s[0] = 0**，方便循环

~~~java
// 建议a数组读入时尽量也从 下标为 1 开始
s[0] = 1;
for (int i = 1; i <= n; i++) 
    s[i] = s[i - 1] + a[i];
~~~



#### 二维

**二维前缀和**：解决的是二维矩阵中的矩形区域求和问题

**二维前缀和数组中，每一个格子记录的是：以当前位置为区域的右下角（区域左上角恒定为原数组的左上角）的区域和**

因此当我们要求 (x1, y1) 作为左上角，(x2, y2)作为右下角 的区域和时，可以直接利用前缀和数组快速求解：

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20220817111647742.png" alt="image-20220817111647742" style="zoom: 33%;" />

红色+绿色部分是 (x2, y1-1)部分的前缀和

蓝色+绿色部分是 (x1-1, y2) 部分的前缀和

可以发现，绿色部分被**减去**了两次，所以我们需要**加上**一次绿色部分的前缀和

~~~java
s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1]
~~~

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230115155137194.png" alt="image-20230115155137194" style="zoom: 50%;" />

==注意：==原数组最好都从下标为 1 开始读入，方便计算

1. **求前缀和 s[i] [j]：**

   ~~~java
   // 以 (i, j) 为右下角的矩阵的和
   s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];
   // 如果已经将原数组插入s中
   s[i][j] += s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1];
   ~~~

2. **求子矩阵的和**：

   ~~~java
   // 以 (x1, y1) 为左上角，(x2, y2) 为右下角的子矩阵的和
   s[x2][y2] - s[x1][y1] = s[x2][y2] - s[x2][y1 - 1] - s[x1 - 1][y2] + s[x1 - 1][y1 - 1]
   ~~~

### 差分

#### 一维差分

原数组为 a

构造b数组，使得：`a[i] = b[1] + b[2] + ... + b[i]`，b就是 a 的差分数组

> b[1] = a[1]
>
> b[2] = a[2] - a[1].....

==作用==：处理区间加减同一个数的问题：a数组的[l, r] 区间中的数都 +或- c

1. [l, r] 区间内每个数都 + c

   ~~~java
   // 也可直接把 a 数组用下面的方法插入，方便构造，insert(i, i, a[i]);
   // 相当于假定 a，初始都为 0，a[i]都是后面插入的数字。
   // 不需要构造差分数组b了
   b[l] += c; 
   b[r + 1] -= c;
   ~~~

   

~~~java
for (int i = 1; i <= n; i++) {
    a[i] = sc.nextInt();
    insert(i, i, a[i]); // 插入 a[i]
}

while (m-- > 0) {
    int l = sc.nextInt(), r = sc.nextInt(), c = sc.nextInt();
    insert(l, r, c);
}

for (int i = 1; i <= n; i++) {
    b[i] += b[i - 1]; // 还原成原数组
}

private static void insert(int l, int r, int c) {
    b[l] += c;
    b[r + 1] -= c;
}
~~~

#### 二维差分

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230115164117331.png" alt="image-20230115164117331" style="zoom: 50%;" />

原数组为 a，构造 b 数组为差分数组，a[i] [j] = 以 [i, j] 为右下角的矩阵和

> b[x1] [y1]  += c，黄框中的元素都 +c，但是只有红色格子加，其他的都要减去
>
> 粉色、蓝色都减去，减了两次绿色区域，所以绿色区域 += c



**作用：**解决子矩阵同时加上 / 减去同一个数的问题

~~~java
// 给以(x1, y1)为左上角，(x2, y2)为右下角的子矩阵中的所有元素加上c：
b[x1][y1] += c;
b[x2 + 1][y1] -= c;
b[x1][y2 + 1] -= c;
b[x2 + 1][y2 + 1] += c;
~~~

求原数组 a

~~~java
b[i][j] += b[i][j - 1] + b[i - 1][j] - b[i - 1][j - 1]; 
~~~



### 双指针

理解思想吧，模板不固定

~~~java
for (int i = 0, j = 0; i < n; i++) {
    while(j < i && chick(i, j)) j++;
    // 题目具体逻辑
}
~~~

~~~java
package com.csh.chap1.double_pointer;
/**
 * @author :Changersh
 * @date : 2022/11/26
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class acw_800 {
    private static int N = 100005, n, m, x;
    private static int[] a = new int[N], b = new int[N];
    public static void main(String[] args) {
        n = sc.nextInt(); m = sc.nextInt(); x = sc.nextInt();
        for (int i = 0; i < n; i++) a[i] = sc.nextInt();
        for (int i = 0; i < m; i++) b[i] = sc.nextInt();

        for (int i = 0, j = m - 1; i < n; i++) {
            if (a[i] >= x) break;
            // 双指针 j = m - 1 从后往前遍历
            while (j >= 0 && a[i] + b[j] > x) j--;
            if (a[i] + b[j] == x) {
                out.println(i + " " + j);
                break;
            }


            // 二分
//            int t = x - a[i];
//            int l = 0, r = m - 1;
//            while (l < r) {
//                int mid = l + r >> 1;
//                if (b[mid] >= t) r = mid;
//                else l = mid + 1;
//            }
//            if (b[l] == t) {
//                out.print(i + " " + l);
//                break;
//            }
        }
        out.close();
    }
    static class FastScanner{
        // sc.xxx;
        // out.print();
        // out.flush();
        // out.close();
        BufferedReader br;
        StringTokenizer st;
        public FastScanner(InputStream in) {
            br=new BufferedReader( new InputStreamReader(System.in));
            eat("");
        }
        public void eat(String s) {
            st=new StringTokenizer(s);
        }

        public String nextLine() {
            try {
                return br.readLine();
            }catch(IOException e) {
                return null;
            }
        }

        public boolean hasNext() {
            while(!st.hasMoreTokens()) {
                String s=nextLine();
                if(s==null)return false;
                eat(s);
            }

            return true;
        }

        public String next() {
            hasNext();
            return st.nextToken();
        }

        public int nextInt() {
            return Integer.parseInt(next());
        }

        public long nextLong() {
            return Long.parseLong(next());
        }

        public double nextDouble() {
            return Double.parseDouble(next());
        }
    }

    static FastScanner sc=new FastScanner(System.in);
    static PrintWriter out=new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
}
~~~

### 位运算

#### lowbit

lowbit(x)：返回 x 的最后一位 1

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20221126202618712.png" alt="image-20221126202618712" style="zoom: 50%;" />

~~~java
private static int lowbit(int x) {
    return x & (-x);
}
~~~

#### 判断是不是 2 的次幂

~~~java
x & (x - 1) == 0
~~~



#### 格雷码

一个排列，它的二进制表示中，任意两个（包括首尾）相邻的数只有一位二进制数不同。这种编码方式就是格雷码

二进制码转换成二进制格雷码，其法则是保留二进制码的最高位作为格雷码的最高位，而次高位格雷码为二进制码的高位与次高位相异或，而格雷码其余各位与次高位的求法相类似。

（从左边第二位开始，每一位和自己左边一位的异或值）

可以用以下函数得到 x 对应的格雷码

~~~java
int gray(x) {
    return x ^ (x >> 1);
}
~~~



### 离散化

前提是数值数组 a 是有序的

用于取值范围很大，但是数值数量很少的情况。将数组中的数一一映射到0 1 2 .... 的数组

1. 如果有重复数值？**去重**
2. 如何算出 x 的值在a中的下标？**二分查找**

 ~~~java
private static int find(int x) {
    // 二分查找位置即可
    int l = 0, r = all.size() - 1;
    while (l < r) {
        int mid = l + r >> 1;
        if (all.get(mid) >= x) r = mid;
        else l = mid + 1;
    }
    return r + 1; // 下标从 1 开始
}
 ~~~

#### 去重

~~~java
private long[] unique(long[] a) {
    Arrays.sort(a);
    int k = 0;
    for (int i = 1; i < a.length; i++)
        if (a[k] != a[i])
            a[++k] = a[i];
    return Arrays.copyOf(a, k + 1);
}
~~~



#### 例题

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/2 9:23
 * 离散化
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class acw_802 {
    private static int N = 300010, n, m;
    private static List<Integer> all = new ArrayList<>(); // 记录所有输入的数值（包括询问的和要加上的）
    private static int[] a; // 离散化后的位置对应要加上的数
    private static int[] s; // 离散化后的前缀和
    private static List<int[]> add = new ArrayList<>(); // 记录某个位置 加上的数值
    private static List<int[]> query = new ArrayList<>(); // 记录询问区间
    public static void main(String[] args) {
        n = sc.nextInt(); m = sc.nextInt();
        // 记录 某位置 x + c
        for (int i = 0; i < n; i++) {
            int x = sc.nextInt(), c = sc.nextInt();
            add.add(new int[]{x, c});

            all.add(x);
        }
        // 记录 询问
        for (int i = 0; i < m; i++) {
            int l = sc.nextInt(), r = sc.nextInt();
            query.add(new int[]{l, r});

            all.add(l);
            all.add(r);
        }

        // all去重
        all.sort(new Comparator<Integer>() {
            @Override
            public int compare(Integer o1, Integer o2) {
                return o1 - o2;
            }
        });
        unique();
        a = new int[all.size() + 1];
        s = new int[all.size() + 1];
        // 处理插入
        for (var t : add) {
            int x = find(t[0]); // find() 找离散化的位置
            a[x] += t[1];
        }

        // 前缀和
        for (int i = 1; i <= all.size(); i++) s[i] = s[i - 1] + a[i];

        // 询问
        for (var t : query) {
            int l = find(t[0]), r = find(t[1]);
            out.println(s[r] - s[l - 1]);
        }

        out.close();
    }

    // 去重函数
    private static void unique() {
        int j = 0;

        for (int i = 0; i < all.size(); i++) {
            if (i == 0 || all.get(i) != all.get(i - 1)) all.set(j++, all.get(i));
        }
        all = new ArrayList<>(all.subList(0, j));
    }

    // 离散化
    private static int find(int x) {
        // 二分查找位置即可
        int l = 0, r = all.size() - 1;
        while (l < r) {
            int mid = l + r >> 1;
            if (all.get(mid) >= x) r = mid;
            else l = mid + 1;
        }
        return r + 1; // 下标从 1 开始
    }

}
~~~



### 区间合并

将有交集的区间合并成一个区间

要将区间进行排序，先左区间从小到大，左区间相同，右区间从小到大

区间分三种情况：

1. 当前区间在 [st, ed] 内，就合并，取 max(ed, r)
2. 有交集，合并，取 max(ed, r)
3. l > ed，将当前 [st, ed] 加入集合, st = l, ed = r 重新开始合并

~~~java
private static void merge() {
    List<int[]> a = new ArrayList<>();
    list.sort(new Comparator<int[]>() {
        @Override
        public int compare(int[] o1, int[] o2) {
            if (o1[0] != o2[0]) return o1[0] - o2[0];
            return o1[1] - o2[1];
        }
    });

    int st = -1000000001, ed = -1000000001;
    for (var it : list) {
        if (ed < it[0]) {
            if (st != -1000000001)a.add(it);
            st = it[0]; ed = it[1];
        } else {
            ed = Math.max(ed, it[1]);
        }
    }
    if (st != -1000000001) a.add(new int[]{st, ed});
    list = a;
}
~~~





## 数据结构

### 单链表

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230103160158407.png" alt="image-20230103160158407" style="zoom:80%;" />

用邻接表的形式来写，e[0] 是 0号点的值，ne[0] 是0号点后面指向的点

~~~java
package com.csh.chap2.A_dan_lian_biao;
/**
 * @author :Changersh
 * @date : 2023/1/3 16:04
 * 单链表
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class acw_826 {
    private static int N = 100010, head, idx = 0, n; // head指向头节点，idx记录当前到了哪个点了
    private static int[] e = new int[N], ne = new int[N]; // e存储结点的值，ne存储结点的下一个点
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        init();
        while (n-- > 0) {
            char opt = sc.next().charAt(0);
            if (opt == 'H') {
                int x = sc.nextInt();
                add_to_head(x);
            } else if (opt == 'D') {
                int k = sc.nextInt();
                if (k == 0) head = ne[head];
                else remove(k - 1);
            } else {
                int k = sc.nextInt(), x = sc.nextInt();
                add(k - 1, x);
            }
        }

        for (int i = head; i != -1; i = ne[i]) {
            System.out.print(e[i] + " ");
        }
    }
    // 初始化
    private static void init() {
        head = -1; // 指向空
    }
    // 向链表头插入x
    private static void add_to_head(int x) {
        e[idx] = x;
        ne[idx] = head;
        head = idx++;
    }
    // 删除第k个插入的数后面的数（我们的编号是从 0 开始的，所以是k - 1，主方法中以及减去了）
    private static void remove(int k) {
        ne[k] = ne[ne[k]];
    }
    // 将 x 插入在第 k 个插入的数的后面
    private static void add(int k, int x) {
        e[idx] = x;
        ne[idx] = ne[k];
        ne[k] = idx++;
    }
}
~~~



### 双链表

r[i] 存储 i 的右端点，l[i] 存储 i 的左端点，将 0 设为最左端，1 设为最右端

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/3 16:45
 * 双链表
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 100010, n, idx = 0;
    private static int[] e = new int[N], l = new int[N], r = new int[N];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        init();
        while (n-- > 0) {
            String s = sc.next();
            if (s.equals("L")) {
                int x = sc.nextInt();
                insert(r[0], x); // 在最左侧插入，insert方法是往一个数的左侧，我们要找到左端点的右侧，它左侧就是左端点
            } else if (s.equals("R")) {
                int x = sc.nextInt();
                insert(1, x); // 右端点序号是 1，在左侧直接插入即可
            } else if (s.equals("D")) {
                int k = sc.nextInt();
                remove(k + 1);
            } else if (s.equals("IL")) {
                    int k = sc.nextInt(), x = sc.nextInt();
                    insert(k + 1, x);
            } else {
                int k = sc.nextInt(), x = sc.nextInt();
                insert(r[k + 1], x); // idx从 2 开始， k 要 +1，找 k+1右边的点，r[k +]
            }
        }
        for (int i = r[0]; i != 1; i = r[i]) System.out.print(e[i] + " ");
    }
    // 初始化
    private static void init() {
        // 用 0 代表 最左端，用 1 代表最右端
        r[0] = 1;
        l[1] = 0;
        idx = 2;
    }
    // 删除第 k 个插入的数，因为idx初识为 2，第k个插入，下标应该是 k + 1
    private static void remove(int k) {
        l[r[k]] = l[k];
        r[l[k]] = r[k];
    }
    // 在第k个插入的数左侧，插入一个数（右侧同理，把  即可）
    private static void insert(int k, int x) {
        e[idx] = x;
        r[idx] = k;
        l[idx] = l[k];
        r[l[k]] = idx;
        l[k] = idx;
        idx++;
    }
}
~~~



### 栈

先进后出，数组模拟栈

~~~java
int[] st[N];
int tt = 0;
// 插入
st[++tt] = x;

// 弹出
tt--;

// 判断是否为空
if (tt > 0) not empty;
else empty;

// 栈顶
st[tt];
~~~



### 队列

~~~java
// 在队尾插入元素，在队首弹出元素
int q[N], hh = 0, tt = -1;

// 插入
q[++tt] = x;

// 弹出
hh++;

// 判断队列是否为空
if (hh <= tt) not empty;
else empty;

// 取出队首元素
q[hh];
~~~



### 单调栈

**经典题型**：给定一个长度为 N 的整数数列，输出每个数左边第一个比它小的数

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/4 15:34
 * 单调栈
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 100010, n, tt = 0;
    private static int[] st = new int[N];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        for (int i = 0; i < n; i++) {
            int x = sc.nextInt();
            while (tt > 0 && st[tt] >= x) tt--;
            if (tt > 0) System.out.print(st[tt] + " ");
            else System.out.print("-1 ");
            st[++tt] = x;
        }
    }
}
~~~



### 单调队列

例题：

**滑动窗口** https://www.acwing.com/problem/content/156/

**滑动窗口模板**：[P1886 滑动窗口 /【模板】单调队列 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P1886)

tips:

1、可以用双指针来写

2.也可以用队列，ArrayDeque 

3、常见的就是找滑动窗口的最大最小值，从队尾和队首分别取出不符合的元素

~~~java
int n, k; // n个数字，滑动窗口大小为 k

// 输出窗口的最小值
private static void getMin() {
    hh = 0; tt = -1;
    for (int i = 0; i < n; i++) {
        // 判断队首是否在窗口内
        if (hh <= tt && i - k + 1 > q[hh]) hh++; // 队列不空的情况下，如果 队首小于 i - k + 1，则已经滑出了窗口
        while (hh <= tt && a[q[tt]] >= a[i]) tt--; // 队列不空情况下，如果队尾元素大于等于当前元素，则不需要，出队
        q[++tt] = i; // jiang
        if (i >= k - 1) System.out.print(a[q[hh]] + " "); // 如果已经足够窗口大小，就输出
    }
}
private static void getMax() {
    hh = 0; tt = -1;
    for (int i = 0; i < n; i++) {
        // 判断队首是否在窗口内
        if (hh <= tt && i - k + 1 > q[hh]) hh++;
        while (hh <= tt && a[q[tt]] <= a[i]) tt--;
        q[++tt] = i;
        if (i >= k - 1) System.out.print(a[q[hh]] + " ");
    }
}
~~~



### kmp

~~~java
private static int[] nxt = new int[N];

// 求 next 数组
for (int i = 2, j = 0; i <= n; i++) {
    while (j > 0 && p[i] != p[j + 1]) j = nxt[j]; // 不符合条件就一直向前寻找
    if (p[i] == p[j + 1]) j++; // 找到与 p[i] 匹配的 j
    nxt[i] = j;
}

// 匹配
for (int i = 1, j = 0; i <= m; i++) {
    while (j > 0 && s[i] != p[j + 1]) j = nxt[j];
    if (s[i] == p[j + 1]) j++;
    if (j == n) {
        // 匹配成功
        out.print((i - n) + " ");
        j = nxt[j];
    }
}
~~~



### Trie Tree

例题：板子 https://ac.nowcoder.com/acm/problem/16864 

树形结构，用于统计、排序、保存大量的字符串，主要利用公共前缀

主要操作：插入和查找



对于 son[] [] 的解释：son[0] [1] = 3 ，[0]是根节点，[1] 是有一个儿子是 'b' ，3 是这个儿子位置是 3，而 [3] [2] = 8 ，代表 'b' 的儿子是 'c'，这个孙子结点的位置是 8

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/5 15:13
 * 字典树
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 100010, n, idx = 0;
    private static int[] cnt = new int[N]; // cnt表示以该行结尾的单词数
    private static int[][] son = new int[N][26]; // 下标是0的点，既是根节点，又是空结点
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        String s = "";
        while (n-- > 0) {
            s = sc.next();
            if (s.equals("I")) {
                s = sc.next();
                insert(s);
            } else {
                s = sc.next();
                System.out.println(query(s));
            }
        }
    }

    private static void insert(String s) {
        char[] c = s.toCharArray();
        int p = 0;
        for (int i = 0; i < c.length; i++) {
            int u = c[i] - 'a';
            if (son[p][u] == 0) son[p][u] = ++idx; // 如果不存在，就开辟一个
            p = son[p][u];
        }
        cnt[p]++;
    }

    private static int query(String s) {
        char[] c = s.toCharArray();
        int p = 0;
        for (int i = 0; i < c.length; i++) {
            int u = c[i] - 'a';
            if (son[p][u] == 0) return 0; // 查找时，字母不存在，单词也不可能存在
            p = son[p][u];
        }
        return cnt[p];
    }
}
~~~



### 并查集

1. 将两个集合合并
2. 询问两个元素是否在同一个集合当中

时间复杂度：O(n log(n) )

#### 朴素并查集

~~~java
// pre[i] 记录每个点的祖宗结点
private static int[] pre = new int[100000]; 

private static int find(int x) {
    if (pre[x] != x) pre[x] = find(pre[x]);
    return pre[x];
}
// main
// 要先初始化
for (int i = 1; i <= n; i++) pre[i] = i;
// 查找
int fx = find(x), fy = find(y); 
// 合并
pre[fx] = fy; 





// 迭代写法
int pre[1000];
int find(int x)                                       //查找根节点
{ 
    int r=x;
    while ( pre[r] != r )                           //返回根节点 r
          r=pre[r];

    int i=x , j ;
    while( i != r )                                   //路径压缩
    {
         j = pre[ i ];                 // 在改变上级之前用临时变量  j 记录下他的值 
         pre[ i ]= r ;                 //把上级改为根节点
         i=j;
    }
    return r ;
}

void join(int x,int y)               //判断x y是否连通，
                                       //如果已经连通，就不用管了 如果不连通，就把它们所在的连通分支合并起,
{
    int fx=find(x),fy=find(y);
    if(fx!=fy)
        pre[fx ]=fy;
}
~~~

#### 维护size的并查集

~~~java
// pre[i] 记录 i 的祖宗结点，size[i] 只有祖宗结点的值有意义：该祖宗结点所在集合的点的数量
private static int[] pre = new int[100000];
private static int[] size = new int[100000];
private static int find(int x) {
    if (pre[x] != x) pre[x] = find(pre[x]);
    return pre[x];
}

// 初始化
for (int i = 1; i <= n; i++) {
    pre[i] = i;
    size[i] = 1;
}
// 查找，合并，size合并
int fx = find(x), fy = find(y);
pre[fx] = fy;
if (fy != fx) size[fy] += size[fx]; // 因为是 x 合并到 y，y成为了 x 的祖宗结点，所以是把 x 加到 y上
~~~

#### 维护到祖宗结点距离的并查集

~~~ java
private static int[] pre = new int[10000];
private static int[] dis = new int[10000];

private static int find(int x) {
    if (pre[x] != x) {
        int u = find(pre[x]);
        dis[x] += dis[pre[x]]; // 根据题目自己变化
        pre[x] = u;
    }
    return pre[x];
}

// 初始化
for (int i = 1; i <= n; i++) {
    pre[i] = i;
    dis[i] = 0;
}
// 查找，合并
int fx = find(x), fy = find(y);
pre[fx] = fy;
dis[fx] = distance; // 根据题目具体分析，初始化 find(a) 的偏移量
~~~



### 堆



### 哈希表

处理冲突的两种方式：拉链法，开放寻址法

位置是 i % N，这个 N 一般取大于取值范围的第一个质数

#### 拉链法

相同key的数放在同一条链上，利用数组模拟单链表

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230106150543978.png" alt="image-20230106150543978" style="zoom:33%;" />

~~~java
package com.csh.chap2.J_hash;
/**
 * @author :Changersh
 * @date : 2023/1/6 14:43
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class acw_840 {
    private static int N = 100003, n, idx = 0;
    private static int e[] = new int[N], ne[] = new int[N], h[] = new int[N];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt();
        Arrays.fill(h, -1);
        while (n-- > 0) {
            String s = sc.next();
            int x = sc.nextInt();
            if (s.equals("I")) {
                insert(x);
            } else {
                System.out.println(find(x) ? "Yes" : "No");
            }
        }
    }
    private static void insert(int x) {
        int k = (x % N + N) % N; // +N 防止有负数
        e[idx] = x;
        ne[idx] = h[k];
        h[k] = idx++;
    }
    private static boolean find(int x) {
        int k = (x % N + N) % N; // +N 防止有负数
        for (int i = h[k]; i != -1; i = ne[i]) {
            if (e[i] == x) return true;
        }
        return false;
    }
}
~~~



#### 开放寻址法

key数组长度一般开到题目数据范围的 2~3倍，概率较低，也是找一个大于数组长度的质数

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/6 14:43
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 200003, n, nl = 0x3f3f3f3f;
    private static int h[] = new int[N];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Arrays.fill(h, nl);
        n = sc.nextInt();
        while (n-- > 0) {
            String s = sc.next();
            int x = sc.nextInt();
            int k = find(x);
            if (s.equals("I")) {
               h[k] = x;
            } else {
                System.out.println(h[k] != nl ? "Yes" : "No");
            }
        }
    }
    private static int find(int x) {
        int k = (x % N + N) % N; // +N 防止有负数
        while (h[k] != nl && h[k] != x) {
            k++;
            if (k == N) k = 0;
        }
        return k;
    }
}
~~~



#### 字符串哈希

字符串前缀哈希

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230106164058692.png" alt="image-20230106164058692" style="zoom:50%;" />

1. 把字符串看成 p 进制的一个数，如果这个字符串有n个字母，就看成 n 位
2. 把 p 进制的数转化成 10 进制的数

**不要把某个字符映射成 0，最好从 1 开始 **

假设我们没有遇到冲突：

一般 p 取 131 / 13331，Q 取 2的64次方 = 18446744073709551616，遇到冲突的概率最小

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230106203904081.png" alt="image-20230106203904081" style="zoom:80%;" />

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/6 20:51
 * 字符串哈希
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 100010, n, m, P = 131; // P 是 P进制，用来求哈希值
    private static long[] p = new long[N]; // P 的次方
    private static long[] h = new long[N]; // 存放hash的前缀值
    private static char[] c;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        n = sc.nextInt(); m = sc.nextInt();
        c = sc.next().toCharArray();

        // 求 hash
        p[0] = 1;
        for (int i = 1; i <= n; i++) {
            p[i] = p[i - 1] * P; // 求 P 的次方
            h[i] = h[i - 1] * P + c[i - 1]; // 相当于 h[i - 1] 左移一位，再加上最低位 c[i - 1]
        }

        while (m-- > 0) {
            int l1 = sc.nextInt();
            int r1 = sc.nextInt();
            int l2 = sc.nextInt();
            int r2 = sc.nextInt();
            System.out.println(get(l1, r1) == get(l2, r2) ? "Yes" : "No");
        }
    }

    private static long get(int l, int r) {
        return h[r] - h[l - 1] * p[r - l + 1];
    }
}
~~~





### 树状数组

单点修改，区间查询

~~~java
// 三个函数
private static int lowbit(int x) {
    return x & -x;
}

private static void add(int x, int u) {
    for (; x <= n; x += lowbit(x)) a[x] += u;
}

private static long ask(int x) {
    long ans = 0;
    for (; x > 0; x -= lowbit(x)) ans += a[x];
    return ans;
}

// 初始化「树状数组」，要默认数组是从 1 开始
for (int i = 1; i <= n; i++) {
    int x = sc.nextInt();
    add(i, x);
}

// 区间查询
ask(y) - ask(x - 1)
~~~



区间修改，单点查询

~~~java
private static long[] a = new long[N], b = new long[N]; // a 是原数组，b是修改的前缀和数组

// 读入原数组
for (int i = 1; i <= n; i++) a[i] = sc.nextLong();

// 区间修改，[x, y]，思路和差分一样
add(x, k);
add(y + 1, -k);

// 单点查询
pw.println(ask(x) + a[x]);





// 三个函数
private static int lowbit(int x) {
    return x & -x;
}

private static void add(int x, int u) {
    for (int i = x; i <= n; i += lowbit(i)) b[i] += u;
}

private static long ask(int x) {
    long ans = 0;
    for (; x > 0; x -= lowbit(x)) ans += b[x];

    return ans;
}
~~~







### 线段树

>  对于编号为 `u` 的节点而言，其左子节点的编号为 `u << 1`，其右节点的编号为 `u << 1 | 1`

单点修改

~~~java
class NumArray {
    class Node {
        int l, r, v;// l, r 是区间左右端点，v是和
        Node(int _l, int _r) {
            l = _l; r = _r;
        }
    }
    int N = 10010;
    Node[] tree = new Node[N * 4];
    // 建树 结点编号为 u，区间 [l, r]
    void build(int u, int l, int r) {
        tree[u] = new Node(l, r);
        if (l == r) { // 该结点是叶子结点
            tree[u].v = input[l];
            return;
        }
        int mid = l + r >> 1;
        build(u << 1, l, mid); // 递归构造左右子树
        build(u << 1 | 1, mid + 1, r);
        pushup(u); // 计算该点的总和
    }
    // 区间查询
    int query(int u, int l, int r) {
        if (l == tree[u].l && r == tree[u].r) {
            return tree[u].v;
        }
        int mid = tree[u].l + tree[u].r >> 1;
        int ans = 0;
        if (r <= mid) ans += query(u << 1, l, r);
        else if (l > mid)
            ans += query(u << 1 | 1, l, r);
        else 
            ans += query(u << 1, l, mid) + query(u << 1 | 1, mid + 1, r);
        return ans;
    }
    // 单点修改
    void update(int u, int x, int v) {
        if (tree[u].l == x && tree[u].r == x) { // 叶子结点说明找到了
            tree[u].v += v;
            return ;
        }
        int mid = tree[u].l + tree[u].r >> 1;
        if (x <= mid) update(u << 1, x, v); // 往左子树
        else update(u << 1 | 1, x, v); // 往右子树
        pushup(u); // 更新父节点的值
    }
    void pushup(int u) {
        tree[u].v = tree[u << 1].v + tree[u << 1 | 1].v;
    }
}
~~~

区间修改，单点查询，不用 lazy

~~~java

~~~





以下是**区间修改**的时候会用到的 lazy 标记

~~~java
class Solution {
    class Node {
        int l, r, v, add;
        Node(int _l, int _r) {
            l = _l; r = _r;
        }
    }
    int N = 20009;
    Node[] tr = new Node[N * 4];
    // 加和
    void pushup(int u) {
        tr[u].v = tr[u << 1].v + tr[u << 1 | 1].v;
    }
    // 标记下放
    void pushdown(int u) {
        int add = tr[u].add;
        tr[u << 1].v += add;
        tr[u << 1].add += add;
        tr[u << 1 | 1].v += add;
        tr[u << 1 | 1].add += add;
        tr[u].add = 0;
    }
    // 建树
    // 当前编号是 u，区间 [l, r]
    void build(int u, int l, int r) {
        tr[u] = new Node(l, r);
        if (l != r) {
            int mid = l + r >> 1;
            build(u << 1, l, mid);
            build(u << 1 | 1, mid + 1, r);
        }
    }
    // 更新结点，在[l, r] 范围内改变 v（区间修改）
    void update(int u, int l, int r, int v) {
        if (l <= tr[u].l && tr[u].r <= r) {
            tr[u].v += v;
            tr[u].add += v;
        } else {
            pushdown(u);
            int mid = tr[u].l + tr[u].r >> 1;
            if (l <= mid) update(u << 1, l, r, v);
            if (r > mid) update(u << 1 | 1, l, r, v);
            pushup(u);
        }
    }
    // 从结点 u 开始，查找 [l, r] 的和
    int query(int u, int l, int r) {
        if (l <= tr[u].l && tr[u].r <= r) {
            return tr[u].v;
        } else {
            pushdown(u);
            int mid = tr[u].l + tr[u].r >> 1;
            int ans = 0;
            if (l <= mid) ans += query(u << 1, l, r);
            if (r > mid) ans += query(u << 1 | 1, l, r);
            return ans;
        }
    }
    public int[] corpFlightBookings(int[][] bs, int n) {
        build(1, 1, n);
        for (int[] bo : bs) {
            update(1, bo[0], bo[1], bo[2]);
        }
        int[] ans = new int[n];
        for (int i = 0; i < n; i++) {
            ans[i] = query(1, i + 1, i + 1);
        }
        return ans;
    }
}
~~~



一个数列要改来改去的，单点操作或者区间操作

————[P3374 【模板】树状数组 1 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P3374)

#### 单点修改，区间查询

```cpp
#include <stdio.h>

int f[2000001]; // f[i] 是 第 i 个结点代表的的区间的和
int a[500001]; // 存数字
int n, m; // n：点数，m：操作数

void buildtree(int k, int l, int r) { // 当前这一点标号为 k，区间左端点 l，右端点为 r
    if (l == r) {                     // 区间走到底了
        f[k] = a[l];                 // f[k] 就是这个区间的和，是这个数本身
        return;
    }
    int m = (l + r) >> 1;            // 否则 m 就是 l 到 r  的中间的数
    // 递归建树
    buildtree(k + k, l, m);            // 左子树
    buildtree(k + k + 1, m + 1, r); // 右子树
    f[k] = f[k + k] + f[k + k + 1]; // 当前区间的和，就是 左子树 + 右子树 的和
}


void add(int k, int l, int r, int x, int y) { // 下标为 k 的点，对应的区间是 [l, r]， 要在 x 的结点上 加上 y
    f[k] += y;                    // 只要是包含 x 的区间都要 + y
    if (l == r) return;            // 到叶子结点就返回

    int m = (l + r) >> 1;
    if (x <= m)                    // 如果在左子树
        add(k + k, l, m, x, y);
    else
        add(k + k + 1, m + 1, r, x, y);
}

int cal(int k, int l, int r, int s, int t) { // 下标是k，区间是[l, r]， 求 s 到 t的和
    if (l == s && r == t)
        return f[k];

    int m = (l + r) >> 1;
    if (t <= m)                    // [s, t] 完全坐落在左区间
        return cal(k + k, l, m, s, t);
    else if (s > m)                // 完全坐落在右区间
        return cal(k + k + 1, m + 1, r, s, t);
    else
        return cal(k + k, l, m, s, m) + cal(k + k + 1, m + 1, r, m + 1, t);
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);
    buildtree(1, 1, n); // 建树，标号为 1 的点，对应的是 [1, n] 这个区间

    for (int i = 0; i < m; i++) {
        int t, x, y;
        scanf("%d%d%d", &t, &x, &y);
        if (t == 1)                 // 操作一
            add(1, 1, n, x, y); // 1号点 对应的是 [1, n]， 要修改的下标是 x，修改的值是 y
        else
            printf("%d\n", cal(1, 1, n, x, y));
    }
    return 0;
}
```

————[P3368 【模板】树状数组 2 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P3368)

#### 区间修改，单点查询

**标记不下放**

```cpp
#include <stdio.h>

int n, m;
long long a[1000001];
long long f[4000001], v[4000001];

void buildtree(int k, int l, int r) {
    v[k] = 0;
    if (l == r) {
        f[k] = a[l];
        return;
    }
    int m = (l + r) >> 1;
    buildtree(k + k, l, m);
    buildtree(k + k + 1, m + 1, r);
    f[k] = f[k + k] + f[k + k + 1];
}

void insert(int k, int l, int r, int x, int y, long long t) {
    if (l == x && r == y) {
        v[k] += t;
        return;
    }
    f[k] += (y - x + 1) * t;
    int m = (l + r) >> 1;
    if (y <= m)
        return insert(k + k, l, m, x, y, t);
    else
        if (x > m)
            return insert(k + k + 1, m + 1, r, x, y, t);
        else
            return insert(k + k, l, m, x, m, t),
                insert(k + k + 1, m + 1, r, m + 1, y, t);
}

long long query(int k, int l, int r, int t, long long sum) {
    sum += v[k];
    if (l == r && l == t) {
        return f[k] + sum;
    }
    int m = (l + r) >> 1;
    if (t <= m)
        return query(k + k, l, m, t, sum);
    else
        return query(k + k + 1, m + 1, r, t, sum);
}


int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++)
        scanf("%lld", &a[i]);

    buildtree(1, 1, n);

    for (int i = 0; i < m; i++) {
        int opt;
        scanf("%d", &opt);
        if (opt == 1) {
            long long x, y;
            long long t;
            scanf("%lld%lld%lld", &x, &y, &t);
            insert(1, 1, n, x, y, t);
        }
        else {
            long long index;
            scanf("%lld", &index);
            printf("%lld\n", query(1, 1, n, index, 0));
        }
    }
    return 0;
}
```

————[P3372 【模板】线段树 1 - 洛谷 | 计算机科学教育新生态 (luogu.com.cn)](https://www.luogu.com.cn/problem/P3372)

#### 区间修改，区间查询

**标记不下放**

```cpp
#include <stdio.h>

int n, m;
int a[100001];
long long f[400001], v[400001]; // a：存数
// f[i]：当前区间的和，是排除这个点之上的点的影响，只考虑下面的点对他的影响
// v[i]：这个区间的每一个点都要加上 v[i] 所对应的值

void buildtree(int k, int l, int r) {
    v[k] = 0; // 刚开始没有修改
    if (l == r) {
        f[k] = a[l];
        return;
    }
    int m = (l + r) >> 1;
    buildtree(k + k, l, m);
    buildtree(k + k + 1, m + 1, r);

    f[k] = f[k + k] + f[k + k + 1];
}

void insert(int k, int l, int r, int x, int y, long long z) {
    // 当前点的下标是 k，对应区间是 [l, r]，要修改的区间是 [x, y]， 值是 z
    if (l == x && r == y) {
        v[k] += z; // f[k] 不用修改，因为不考虑当前的点到根节点的 v 的影响
        return;
    }
    f[k] += (y - x + 1) * z; // f[k]不考虑这个点到根节点的影响，
    // 但是要考虑它下面的结点的影响，这个区间里面有 (y - x + 1)个结点要修改

    int m = (l + r) >> 1;
    if (y <= m)
        insert(k + k, l, m, x, y, z);
    else
        if (x > m)
            insert(k + k + 1, m + 1, r, x, y, z);
        else {
            insert(k + k, l, m, x, m, z);
            insert(k + k + 1, m + 1, r, m + 1, y, z);
        }
}

long long calc(int k, int l, int r, int x, int y, long long p) {
    // 第 k 个点，区间是 [l, r] 要求 [x, y] 的和
    // p 是 从根到当前结点的 v 的和，是每个点都要加一个v
    p += v[k];
    if (l == x && r == y) {
        // 一共有 (r - l + 1)
        return p * (r - l + 1) + f[k];
    }

    int m = (l + r) >> 1;
    if (y <= m)
        return calc(k + k, l, m, x, y, p);
    else
        if (x > m)
            return calc(k + k + 1, m + 1, r, x, y, p);
        else
            return calc(k + k, l, m, x, m, p) + calc(k + k + 1, m + 1, r, m + 1, y, p);

}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);

    buildtree(1, 1, n);

    for (int i = 1; i <= m; i++) {
        int t;
        scanf("%d", &t);
        if (t == 1) {
            int x, y;
            long long k;
            scanf("%d%d%lld", &x, &y, &k);
            insert(1, 1, n, x, y, k);
        }
        else {
            int x, y;
            scanf("%d%d", &x, &y);
            printf("%lld\n", calc(1, 1, n, x, y, 0));
        }
    }
    return 0;
}
```

**标记下放**

```cpp
#include <stdio.h>

int n, m;
int a[100001];
long long f[400001], v[400001]; // a：存数
// f[i]：当前区间的和，是排除这个点之上的点的影响，只考虑下面的点对他的影响
// v[i]：这个区间的每一个点都要加上 v[i] 所对应的值

void buildtree(int k, int l, int r) {
    v[k] = 0; // 刚开始没有修改
    if (l == r) {
        f[k] = a[l];
        return;
    }
    int m = (l + r) >> 1;
    buildtree(k + k, l, m);
    buildtree(k + k + 1, m + 1, r);

    f[k] = f[k + k] + f[k + k + 1];
}

void insert(int k, int l, int r, int x, int y, long long z) {
    if (l == x && r == y) { // 个人习惯，如果 需要操作的区间和当前区间一致，就不再下放标记了
        v[k] += z;
        return;
    }
    if (v[k] != 0) { // 标记下放，之后当前标记清零
        v[k + k] += v[k];
        v[k + k + 1] += v[k];
        v[k] = 0;
    }

    // 不完全重合，把标记往下扔
    int m = (l + r) >> 1;
    if (y <= m)
        insert(k + k, l, m, x, y, z);
    else
        if (x > m)
            insert(k + k + 1, m + 1, r, x, y, z);
        else {
            insert(k + k, l, m, x, m, z);
            insert(k + k + 1, m + 1, r, m + 1, y, z);
        }

    // 收回标记
    f[k] = f[k + k] + v[k + k] * (m - l + 1) + f[k + k + 1] + v[k + k + 1] * (r - m);
}

long long calc(int k, int l, int r, int x, int y) {
    if (l == x && r == y) {
        return f[k] + v[k] * (r - l + 1);
    }
    if (v[k] != 0) { // 标记下放，之后当前标记清零
        v[k + k] += v[k];
        v[k + k + 1] += v[k];
        v[k] = 0;
    }
    int m = (l + r) >> 1;
    long long ans = 0; // 记录返回值，因为下面要收回标记，所以不能直接return
    if (y <= m)
        ans = calc(k + k, l, m, x, y);
    else
        if (x > m)
            ans = calc(k + k + 1, m + 1, r, x, y);
        else
            ans = calc(k + k, l, m, x, m) + calc(k + k + 1, m + 1, r, m + 1, y);

    // 收回标记
    f[k] = f[k + k] + v[k + k] * (m - l + 1) + f[k + k + 1] + v[k + k + 1] * (r - m);
    return ans;
}

int main() {
    scanf("%d%d", &n, &m);
    for (int i = 1; i <= n; i++)
        scanf("%d", &a[i]);

    buildtree(1, 1, n);

    for (int i = 1; i <= m; i++) {
        int t;
        scanf("%d", &t);
        if (t == 1) {
            int x, y;
            long long k;
            scanf("%d%d%lld", &x, &y, &k);
            insert(1, 1, n, x, y, k);
        }
        else {
            int x, y;
            scanf("%d%d", &x, &y);
            printf("%lld\n", calc(1, 1, n, x, y));
        }
    }
    return 0;
}
```

## 搜索与图论

### 回溯

#### 子集型

每个元素都可以 **选 / 不选**

#### 组合型

#### 排列型

### DFS

一条路走到头才会回溯走下一条路

使用 stack，占空间少，存的时候可以只存一条路上的，和高度成正比

~~~java
private static int N = 10, n;
private static int[] path = new int[N];
private static boolean[] vis = new boolean[N]; // 标记是否走过了
private static void dfs(int u) {
    if (u == n) {
        到达终点...
        return;
    }

    for (int i = 1; i <= n; i++) {
        if (!vis[i]) { // 如果没有被用过
            vis[i] = true; // 标记已经走过
            path[u] = i; // 放入
            dfs(u + 1); // 递归
            vis[i] = false; // 取消标记，进行回溯
        }
    }
}
~~~

八皇后逐个遍历棋子

~~~java
private static void dfs(int x, int y, int s) {
    if (y == n) { // 某行走到头了，直接到下一行的开头
        y = 0;
        x++;
    }
    if (x == n) {
        if (s == n) { // 遍历完，并且棋子也放完了
            for (int i = 0; i < n; i++)
                System.out.println(g[i]);
            System.out.println();
        }
        return;
    }

    // 不放棋子
    dfs(x, y + 1, s);
    // 放棋子
    if (!row[x] && !col[y] && !dg[x + y] && !udg[x - y + n]) {
        g[x][y] = 'Q';
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = true;
        dfs(x, y + 1, s + 1);
        row[x] = col[y] = dg[x + y] = udg[x - y + n] = false;
        g[x][y] = '.';
    }
}
~~~



### BFS

一层一层的搜索，使用 queue，占空间多，需要存一整层的

具有最短路的性质，但是只有边的权重为 1 的时候才可以找到最短路

acw_844走迷宫

~~~java
private static int N = 110;
private static int n, m;
private static int[][] g; // 存图
private static int[][] d; // 存每个点到起点的距离，初始为-1
private static Queue<int[]> q = new LinkedList<>();
private static int[] dx = {-1, 0, 1, 0}, dy = {0, 1, 0, -1};

private static void bfs() {
    d[0][0] = 0;
    q.add(new int[]{0, 0});
    while (!q.isEmpty()) {
        int[] t = q.poll();
        for (int i = 0; i < 4; i++) {
            int x = t[0] + dx[i], y = t[1] + dy[i];
            if (x >= 0 && x < n && y >= 0 && y < m && g[x][y] == 0 && d[x][y] == -1) {
                d[x][y] = d[t[0]][t[1]] + 1;
                q.add(new int[]{x, y});
            }
        }
    }
}
~~~



### 树和图的存储

树是一种特殊的图，是无环有向图

无向图也是一种特殊的有向图，可以把无向拆开成两个有向的：a-b==>  a->b 和 b->a

因此，我们只需考虑有向图的存储

- 邻接矩阵
  - 就是一个二维数组 `g[a, b]`中就存储 a和b之间的关系，（是否有边 /  之间的权重），但不能存重边，只能存重边中的一条
  - 但是很浪费空间，一般适用于==稠密图==
- 邻接表
  - 单链表形式存储
  - 每个点都有一个对应的单链表，存储这个点可以到达的所有的点

使用数组模拟邻接表

![image-20221104133946675](C:\Users\lenovo\Desktop\My MD\img\image-20221104133946675.png)

~~~java
// 对于每个点k，开一个单链表，存储k所有可以走到的点。h[k]存储这个单链表的头结点
int N = 100010, M = N * 2;
// h存储单链表的头节点
// e存储值
// ne存储下一个点的索引
int h[N], e[M], ne[M], idx;

// 添加一条边a->b
void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++ ;
}

// 初始化
idx = 0;
memset(h, -1, sizeof h); // 所有点的头节点初始化为 -1
~~~

#### dfs

O(n + m)

~~~java
private static int N = 100010, M = N * 2, n, idx = 0, ans = N;
private static int[] h = new int[N], e = new int[M], ne = new int[M]; // 无向图的时候，e和ne是两倍的
private static boolean vis[] = new boolean[N]; // 标记是否访问过

public static void main(String[] args) {
    Arrays.fill(h, -1); // 初始化
    ....add(a, b);

    dfs(1);
}
// add 
private static void add(int a, int b) {
    e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
}
// dfs
private static int dfs(int u) {
    vis[u] = true; // 标记访问过了

    for (int i = h[u]; i != -1; i = ne[i]) {
        int j = e[i];
        if (!vis[j]) {
           dfs(j);
        }
    }
    
}
~~~

#### bfs

O(n + m)

~~~java
public class acw_847 {
    private static int N = 100010, n, m, idx = 0;
    private static int[] h = new int[N], e = new int[N], ne = new int[N], d = new int[N], q = new int[N];
    public static void main(String[] args) {
        // 初始化
        Arrays.fill(h, -1);
        Arrays.fill(d, -1); // 距离都初始化为-1，也表示没有被访问过
    }

    private static int bfs() {
        int hd = 0, tl = 0;
        q[0] = 1;
        d[1] = 0;
        while (hd <= tl) {
            int t = q[hd++]; // 取出队首结点
            for (int i = h[t]; i != -1; i = ne[i]) { // 遍历队首元素的所有出边
                int j = e[i];
                if (d[j] == -1) { 
                    d[j] = d[t] + 1;
                    q[++tl] = j;
                }
            }
        }

        return d[n];
    }

    private static void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
    }
}
~~~



### 拓扑排序

有向图才有拓扑序列，有向无环图，一定有拓扑序列

对于一个点的序列，满足有向边xy，点x 都出现在 y 前面

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230108201716072.png" alt="image-20230108201716072" style="zoom:33%;" />

一个有向无环图，一定至少存在一个入度为0的点

找入度为 0 的点，是不会被指向的，直接入队，然后以此来遍历，它连接到的边一定是满足拓扑序的，把遍历过的边删除（即使被指向的点的入度 - 1）

重复以上两步，就可以找到拓扑序

如果遍历结束之后，所有点都入队了，表示满足拓扑序

~~~java
/**
 * @author :Changersh
 * @date : 2023/1/8 20:25
 */

import java.io.*;
import java.util.*;
import java.lang.*;

public class Main {
    private static int N = 100010, n, m, idx;
    private static int[] h = new int[N], e = new int[N], ne = new int[N], d = new int[N]; // d 存储入度
    private static int[] q = new int[N];
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        Arrays.fill(h, -1); // 初始化
        n = sc.nextInt(); m = sc.nextInt();
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(), b = sc.nextInt();
            add(a, b);
            d[b]++; // 更新入度
        }

        if (topsort()) {
            for (int i = 0; i < n; i++) {
                System.out.print(q[i] + " ");
            }
        } else {
            System.out.println("-1");
        }
    }
    private static void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
    }

    private static boolean topsort() {
        int hh = 0, tt = -1;
        // 所有入度为 0 的点都可以作为topo排序的头节点
        // 所以将所有入度为 0 的点入队
        for (int i = 1; i <= n; i++)
            if (d[i] == 0) q[++tt] = i;

        while (hh <= tt) {
            int t = q[hh++];
            for (int i = h[t]; i != -1; i = ne[i]) {
                int j = e[i];
                d[j]--;
                if (d[j] == 0) q[++tt] = j;
            }
        }

        return tt == n - 1;
    }
}
~~~

### 最短路

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20221109145821370.png" alt="image-20221109145821370" style="zoom:80%;" />

n是点数，m是边数

稠密图：m 和 n^2^  是一个级别

稀疏图：m 和 n 是一个级别的

* 单源最短路：一个点到其他所有点的距离
  1. 所有边权都是正数
     - 朴素Dijkstra算法	O(n^2)（边非常多的时候用）
     - 堆优化的Dijkstra算法   O(mlogn)
  2. 存在负权边
     - Bellman-Ford   O(nm)
     - SPFA   一般情况：O(m)，最坏：O(nm)
* 多源汇最短路：可能是多次询问，任意两个点的距离，起点和终点是不确定的
  * Floyd算法   O(n^3)

### 朴素Dijkstra

适用于稠密图，边比点多很多的情况，m = n^2^

使用邻接矩阵实现，O(n^2)

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230109091855257.png" alt="image-20230109091855257" style="zoom:80%;" />

1. 初始化：只有起点到起点的距离是 0，其他所有点到起点的距离正**正无穷**
2. n次迭代，每次找到距离起点最近的点，用这个点更新其他所有点的距离，将这个点加入已确定的集合中

~~~java
public class Main {
    private static int N = 510, n, m;
    private static int g[][] = new int[N][N], dist[] = new int[N];
    private static boolean vis[] = new boolean[N]; // 标记是否已经确定好距离
    
    public static void main(String[] args) {
        for (var i : g) Arrays.fill(i, 0x3f3f3f3f); // 初始化为 正无穷
        
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            g[a][b] = Math.min(g[a][b], c); // 对于重边，取最小值
        }

        System.out.println(dijkstra());
    }

    private static int dijkstra() {
        Arrays.fill(dist, 0x3f3f3f3f); // 初始化为 正无穷
        dist[1] = 0;

        // 总共迭代 n 次，求出来答案
        for (int i = 0; i < n; i++) {
            int t = -1;
            for (int j = 1; j <= n; j++) {
                if (!vis[j] && (t == -1 || dist[t] > dist[j]))
                    t = j; // 找到没有确定距离的点中，距离起点最近的点
            }
            vis[t] = true; // 标记已经确定距离
            // 用这个点更新所有有关的点
            for (int j = 1; j <= n; j++) {
                if (!vis[j]) dist[j] = Math.min(dist[j], dist[t] + g[t][j]);
            }
        }
        return dist[n] == 0x3f3f3f3f ? -1 : dist[n];
    }
}
~~~



### 堆优化Dijkstra

一般是稀疏图，边和点差不多的，就采用==邻接表==存储，n 和 m 差不多大

用堆存储最短距离, O(mlogn)

~~~java
public class Main {
    private static int N = 200010, n, m, idx;
    private static int h[] = new int[N], w[] = new int[N], e[] = new int[N], ne[] = new int[N];
    private static int dist[] = new int[N];
    private static boolean vis[] = new boolean[N]; // 标记是否已经确定好距离
    
    public static void main(String[] args) {
        Arrays.fill(h, -1); // 初始化
 
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            add(a, b, c);
        }

        System.out.println(dijkstra());
    }

    private static void add(int a, int b, int c) {
        e[idx] = b; w[idx] = c; ne[idx] = h[a]; h[a] = idx++;
    }

    private static int dijkstra() {
        Arrays.fill(dist, 0x3f3f3f3f); // 初始化
        dist[1] = 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>(new Comparator<int[]>() {
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[0] - o2[0];
            }
        });

        // 入队
        pq.add(new int[]{0, 1}); // 距离为 0，点编号为 1
        
        // 队列不空就一直循环
        while (pq.size() > 0) {
            var t = pq.poll();
            int a = t[1], val = t[0];
            if (vis[a]) continue; //如果已经被访问过了，跳过这次循环
            // 用这个点更新其他所有点
                vis[a] = true;
                for (int i = h[a]; i != -1; i = ne[i]) {
                    int j = e[i];
                    if (!vis[j] && dist[j] > val + w[i]) {
                        dist[j] = val + w[i];
                        pq.add(new int[]{dist[j], j});
                    }
                }
        }
        return dist[n] == 0x3f3f3f3f ? -1 : dist[n];
    }
}
~~~



### Bellman-Ford

处理有负权边的最短路问题，O(nm)，有负环时使用，没有负环shi

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230110092248338.png" alt="image-20230110092248338" style="zoom:67%;" />

~~~java
时间复杂度 O(nm), n 表示点数，m 表示边数
注意在模板题中需要对下面的模板稍作修改，加上备份数组，详情见模板题。

int n, m;       // n表示点数，m表示边数
int dist[N];        // dist[x]存储1到x的最短路距离

struct Edge     // 边，a表示出点，b表示入点，w表示边的权重
{
    int a, b, w;
}edges[M];

// 求1到n的最短路距离，如果无法从1走到n，则返回-1。
int bellman_ford()
{
    memset(dist, 0x3f, sizeof dist);
    dist[1] = 0;

    // 如果第n次迭代仍然会松弛三角不等式，就说明存在一条长度是n+1的最短路径，由抽屉原理，路径中至少存在两个相同的点，说明图中存在负权回路。
    for (int i = 0; i < n; i ++ )
    {
        for (int j = 0; j < m; j ++ )
        {
            int a = edges[j].a, b = edges[j].b, w = edges[j].w;
            if (dist[b] > dist[a] + w)
                dist[b] = dist[a] + w;
        }
    }

    if (dist[n] > 0x3f3f3f3f / 2) return -1;
    return dist[n];
}
~~~



### 有边数限制的bellman-ford

把方法中外循环的 n 次循环限制在了 k 次，并且加上了 backup的备份数组，防止串联

~~~java
public class acw_853 {
    private static int N = 510, M = 10010, n, m, k;
    private static int[] dist = new int[N];
    private static int[] backup = new int[N]; // 作为备份使用，是上一次迭代的结果。用来更新下一次的遍历
    private static Edge[] edge = new Edge[M];
    // 边的类，模拟结构体
    static class Edge {
        int a; int b; int w;

        public Edge(int a, int b, int w) {
            this.a = a;
            this.b = b;
            this.w = w;
        }
    }

    public static void main(String[] args) {
        for (int i = 0; i < m; i++) { // 读入边
            int a = sc.nextInt(), b = sc.nextInt(), w = sc.nextInt();
            edge[i] = new Edge(a, b, w);
        }

        bellman_ford();
    }

    private static void bellman_ford() {
        // 初始化
        Arrays.fill(dist, 0x3f3f3f3f);
        dist[1] = 0;
        // 限制在 k 条边
        for (int i = 0; i < k; i++) {
            backup = Arrays.copyOf(dist, N); // 复制，备份
            for (int j = 0; j < m; j++) {
                int a = edge[j].a, b = edge[j].b, w = edge[j].w;
                dist[b] = Math.min(dist[b], backup[a] + w); // 使用backup来更新，防止串联
            }
        }
        if (dist[n] > 0x3f3f3f3f / 2) System.out.println("impossible"); // 这个判断是因为有时候有负权边，不会完全等于 0x3f3f3f3f
        else System.out.println(dist[n]);
    }
}
~~~

### spfa判断最短路

是bellman-Ford的优化，即，如果这条边想要变小，那么它的入边肯定是变小的，所以把遍历到的变小的点的出边入队来一次性操作

和dijkstra 很像

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230110101600223.png" alt="image-20230110101600223" style="zoom:67%;" />

~~~java
public class acw_851 {
    private static int N = 100010, n, m, idx;
    private static int h[] = new int[N], w[] = new int[N], e[] = new int[N], ne[] = new int[N];
    private static int dist[] = new int[N];
    private static boolean vis[] = new boolean[N]; // 标记是否入队
    
    public static void main(String[] args) {
     
        Arrays.fill(h, -1); // 初始化
   
        for (int i = 0; i < m; i++) { // 加入边
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            add(a, b, c);
        }

        spfa();
    }

    private static void add(int a, int b, int c) {
        e[idx] = b; w[idx] = c; ne[idx] = h[a]; h[a] = idx++;
    }

    private static void spfa() {
        Arrays.fill(dist, 0x3f3f3f3f); // 初始化
        dist[1] = 0;
        
        LinkedList<Integer> q = new LinkedList<>();
        q.add(1);
        vis[1] = true; // 防止重复入队
        
        while (q.size() > 0) {
            int t = q.poll();
            vis[t] = false;

            for (int i = h[t]; i != -1; i = ne[i]) { // 遍历所有出边
                int j = e[i];
                if (dist[j] > dist[t] + w[i]) {
                    dist[j] = dist[t] + w[i];
                    if (!vis[j]) {
                        q.add(j);
                        vis[j] = true;
                    }
                }
            }
        }


        System.out.println(dist[n] > 0x3f3f3f3f / 2 ? "impossible" : dist[n]);
    }
}
~~~



### spfa判断负环

~~~java
public class Main {
    private static int N = 100010, n, m, idx;
    private static int h[] = new int[N], w[] = new int[N], e[] = new int[N], ne[] = new int[N];
    private static int dist[] = new int[N];
    private static int cnt[] = new int[N]; // 记录到这个点经过的边数
    private static boolean vis[] = new boolean[N]; // 标记是否已经确定好距离
    
    public static void main(String[] args) {

        Arrays.fill(h, -1); // 初始化

        for (int i = 0; i < m; i++) { // 加入边
            int a = sc.nextInt(), b = sc.nextInt(), c = sc.nextInt();
            add(a, b, c);
        }

        if (spfa()) System.out.println("Yes");
        else System.out.println("No");
    }

    private static void add(int a, int b, int c) {
        e[idx] = b; w[idx] = c; ne[idx] = h[a]; h[a] = idx++;
    }

    private static boolean spfa() {
        // 存的不是距离的绝对值了，不需要初始化 dist 了
        LinkedList<Integer> q = new LinkedList<>();
        
        // 有可能从起点不能到达负环，所以初始时把所有点都入队
        for (int i = 1; i <= n; i++) {
            vis[i] =true;
            q.add(i);
        }
        
        while (q.size() > 0) {
            int t = q.poll();
            vis[t] = false;

            for (int i = h[t]; i != -1; i = ne[i]) { // 遍历所有出边
                int j = e[i];
                if (dist[j] > dist[t] + w[i]) {
                    dist[j] = dist[t] + w[i];
                    cnt[j] = cnt[t] + 1;
                    if (cnt[j] >= n) return true; // 经过该点的边大于 (n - 1) 条，
                    if (!vis[j]) {
                        q.add(j);
                        vis[j] = true;
                    }
                }
            }
        }
        return false;
    }
}
~~~



### Floyd

多源汇最短路

~~~java
public class Main {
    private static int N = 210, n, m, Q, INF = 0x3f3f3f3f;
    private static int[][] d = new int[N][N];
    
    public static void main(String[] args) {

        // 初始化
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= n; j++) {
                if (i == j) d[i][j] = 0;
                else d[i][j] = INF;
            }
        }

        // 读入边
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(), b = sc.nextInt(), w = sc.nextInt();
            d[a][b] = Math.min(d[a][b], w);
        }

        floyd();

        while (Q-- > 0) {
            int a = sc.nextInt(), b = sc.nextInt();
            if (d[a][b] > INF / 2) System.out.println("impossible");
            else System.out.println(d[a][b]);
        }
    }

    private static void floyd() {
        for (int k = 1; k <= n; k++) {
            for (int i = 1; i <= n; i++) {
                for (int j = 1; j <= n; j++)
                    d[i][j] = Math.min(d[i][j], d[i][k] + d[k][j]);
            }
        }
    }
}
~~~

### 最小生成树

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230111093808142.png" alt="image-20230111093808142" style="zoom:80%;" />

### Prim

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230111100026975.png" alt="image-20230111100026975" style="zoom:50%;" />

最小生成树，适用于 稠密图，用邻接矩阵存储

~~~java
public class Main {
    private static int N = 510, n, m, INF = 0x3f3f3f3f;
    private static int g[][] = new int[N][N], dist[] = new int[N]; // dist存储的是该点到 已访问过的点的集合中的任意一点的最小的距离，而不是到起点的距离了
    private static boolean vis[] = new boolean[N];
    
    public static void main(String[] args) {
		
        for (var i : g) Arrays.fill(i, INF); // 初始化

        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(); int b = sc.nextInt(); int c = sc.nextInt();
            g[a][b] = g[b][a] = Math.min(g[a][b], c); // 重边取长度最小的
        }

        int t = prim();

        if (t == INF) System.out.println("impossible");
        else System.out.println(t);
    }

    private static int prim() {
        Arrays.fill(dist, INF); // 初始化距离
        int ans = 0;
        // 迭代 n 次（因为有n个点）
        for (int i = 0; i < n; i++) {
            // 找到目前未访问过的，距离集合s最近的点
            int t = -1;
            for (int j = 1; j <= n; j++)
                if (!vis[j] && (t == -1 || dist[t] > dist[j]))
                    t = j;
            // 注意条件是 i != 0 或者是 t != 1，因为第一次取出的点距离设成 0
            if (i != 0 && dist[t] == INF) return INF; // 该点距离是 INF，证明没有边，不是通路，不存在最小生成树
            // 注意条件是 i != 0 或者是 t != 1，因为第一次取出点时，集合s是空的，没有边
            if (i != 0) ans += dist[t];
            // 用满足条件的点，更新所有点
            for (int j = 1; j <= n; j++)
                if (!vis[j]) dist[j] = Math.min(dist[j], g[t][j]);// 这里是和dijksrea不同的地方，dijkstra后面是 g[t][j] + dist[t] 因为求的是到起点的距离
            vis[t] = true; // 标记已加入集合
        }

        return ans;
    }
}
~~~



### Kruskal

稀疏图，邻接表存储

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230111105628723.png" alt="image-20230111105628723" style="zoom:80%;" />

~~~java
public class Main {
    private static int N = 200010, n, m;
    private static int p[] = new int[N]; // 存储的是 并查集的祖宗结点
    private static Edge edge[]; // 边的集合
    // 边的类
    static class Edge {
        int a, b, w;
        public Edge(int a, int b, int w) {
            this.a = a;
            this.b = b;
            this.w = w;
        }
    }
    
    public static void main(String[] args) {

        edge = new Edge[m];
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt(); int b = sc.nextInt(); int w = sc.nextInt();
            edge[i] = new Edge(a, b, w);
        }

        // 初始化并查集的祖宗数组
        for (int i = 0; i <= n; i++) p[i] = i;

        // 将边按权重排序
        Arrays.sort(edge, new Comparator<Edge>() {
            @Override
            public int compare(Edge o1, Edge o2) {
                return o1.w - o2.w;
            }
        });

        int ans = 0, cnt = 0; // ans是权重和，cnt是边数和
        for (int i = 0; i < m; i++) {
            int a = edge[i].a, b = edge[i].b, w = edge[i].w;
            int fa = find(a), fb = find(b);
            if (fa != fb) {
                p[fa] = fb;
                ans += w;
                cnt++;
            }
        }
        if (cnt <= n - 1) System.out.println(ans);
        else System.out.println("impossible");
    }

    private static int find(int x) {
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }
}
~~~



### 二分图

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230111095754603.png" alt="image-20230111095754603" style="zoom:80%;" />

### 染色法（判断是否是二分图）

二分图：当且仅当图中不含奇数环（即环中边数不为奇数）

二分图：可以把点分成两个集合，使得边是在集合之间的，集合内部没有边

染色法就是遍历所有的点，初始点染色1，所有儿子染色2，依次染色，如果出现矛盾（儿子已经被染色，并且儿子颜色和自己颜色相同）则不是二分图，否则就是二分图。

遍历所有点，如果没有被染色，就用 dfs 对其染色

~~~java
public class Main {
    private static int N = 100010, M = 200010, n, m, idx = 0;
    private static int[] h = new int[N], e = new int[M], ne = new int[M];
    private static int[] color = new int[N]; // 表示颜色

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        // 初始化
        Arrays.fill(h, -1);

        n = sc.nextInt();
        m = sc.nextInt();
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            add(a, b);
            add(b, a); // 因为是无向图
        }

        boolean ck = true;
        for (int i = 1; i <= n; i++) {
            if (color[i] == 0) {
                if (!dfs(i, 1)) { // i 号点，染色是 1
                    ck = false;
                    break;
                }
            }
        }
        if (ck) System.out.println("Yes");
        else System.out.println("No");
    }

    private static boolean dfs(int u, int x) {
        color[u] = x;

        for (int i = h[u]; i != -1; i = ne[i]) {
            int j = e[i];
            if (color[j] == 0) {
                if (!dfs(j, 3 - x)) return false;
            }
            else if (color[j] == x) return false;
        }
        return true;
    }

    private static void add(int a, int b) {
        e[idx] = b;
        ne[idx] = h[a];
        h[a] = idx++;
    }
}
~~~



### 匈牙利算法（求给定二分图的最大匹配）

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230111144717425.png" alt="image-20230111144717425" style="zoom:67%;" />

最大匹配：不存在两条边连向同一个点

逐个遍历第一个集合中的点，如果这个点连到的另一个集合中的点没有连接其他的点，就连接，匹配成功。如果连接到的另一个集合中的点已经有了匹配，就看看与它匹配的点有没有其他可以匹配的点。

匈牙利算法准则：待字闺中，据为己有；名花有主，求他放手。

~~~java
public class acw_861 {
    private static int N = 510, M = 100010, n1, n2, m, idx;
    private static int[] h = new int[N], e = new int[M], ne = new int[M];
    private static int[] match = new int[N]; // n2中每个点匹配的n1中的谁
    private static boolean[] vis = new boolean[N]; // 标记女生是否被访问过
    
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        // 初始化
        Arrays.fill(h, -1);
        n1 = sc.nextInt(); n2 = sc.nextInt(); m = sc.nextInt();
        for (int i = 0; i < m; i++) {
            int a = sc.nextInt();
            int b = sc.nextInt();
            add(a, b); // 虽然是无向图，但是我们只需要存储n1到n2的边即可
        }

        int ans = 0; // 匹配数量
        // 依次遍历分析，左集每个人该和右集的谁匹配
        for (int i = 1; i <= n1; i++) {
            Arrays.fill(vis, false); // 每个点匹配之前，保证每个女生都没有访问过
            if (find(i)) ans++;
        }
        System.out.println(ans);
    }

    private static boolean find(int x) {
        for (int i = h[x]; i != -1; i = ne[i]) {
            int j = e[i];
            if (!vis[j]) { // 如果当前的女生没有被访问过
                vis[j] = true;
                if (match[j] == 0 || find(match[j])) { // 如果没有被匹配到，或者匹配到了，但是那个男生可以换一个匹配对象
                    match[j] = x;
                    return true;
                }
            }
        }

        return false;
    }

    private static void add(int a, int b) {
        e[idx] = b; ne[idx] = h[a]; h[a] = idx++;
    }
}
~~~



## 数学知识

**质数**：

- 大于 1 的整数，只包含 1 和本身这两个约数。
- 1-n中，质数个数是`n/ln(n)`

ceil() 上取整函数，转换为整数运算：ceil(a / b) = (a + b - 1) / b

### 试除法—判定质数

性质1：数的约数是成对出现的，比如 12 = 3 * 4 = 2 * 6....，我们可以只列举小的那个约数，节省计算时间

时间复杂度：固定是O(sqrt(n))

~~~java
private static boolean is_prime(int x) {
    if (x < 2) return false;
    for (int i = 2; i <= x / i; i++) { // x / i 防止溢出，并且利用了性质1
        if (x % i == 0) return false;
    }
    return true;
}
~~~

### 试除法—分解质因子

性质1：n中最多只包含一个大于sqrt(n) 的质因子

时间复杂度：最坏是 O(sqrt(n))

~~~java
private static void divide(int x) {
    for (int i = 2; i <= x / i; i++) { // 利用 性质1，减少遍历时间
        if (x % i == 0) {
            int cnt = 0;
            while (x % i == 0) {
                x /= i;
                cnt++;
            }
            System.out.println(i + " " + cnt);
        }
    } 
    if (x > 1) System.out.println(x + " " + 1); // 利用 性质1，n中最多只有一个大于 sqrt(n) 的质因子
}
~~~

### 求所有素数

#### 埃氏筛—求素数

时间复杂度：O(n log( logn ))

~~~java
private static int N = 1000010, n, cnt;
private static int[] primes = new int[N]; // 存储筛出来的素数
private static boolean[] vis = new boolean[N]; // 表示这个数有没有被筛掉

private static void get_primes(int x) {
    for (int i = 2; i <= x; i++) {
        if (!vis[i]) { // 如果没有被筛掉，说明是素数了
            primes[cnt++] = i;
            // 将 i 的倍数筛掉
            for (int j = i; j <= x; j += i) vis[j] = true; // 只需要筛素数的倍数即可，因为合数的倍数会被素数筛掉
        }
    }
}
~~~

#### 线性筛—求素数

1. 当 i % primes[j] == 0 时，pj一定是 i * pj 的最小质因子，因为 pj 一定是小于等于 i 的
2. 当 i % primes[j] != 0 时，pj一定小于 i 的所有质因子，pj一定是 pj * i 的最小质因子

所以，筛每个数时，都是用的它的最小质因子筛掉的，所以是线性筛掉的

~~~java
private static int N = 1000010, n, cnt;
private static int[] primes = new int[N]; // 存储筛出来的素数
private static boolean[] vis = new boolean[N]; // 表示这个数有没有被筛掉

private static void get_primes(int x) {
    for (int i = 2; i <= x; i++) {
        if (!vis[i]) primes[cnt++] = i; // 没被筛掉，就是素数

        for (int j = 0; primes[j] <= x / i; j++) {
            vis[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}
~~~



### 试除法—求 x 的阶乘中5的次方

`n! 中p的次数是 n / p + n / p^2 + n / p^3 + ...`

直接循环就好，每次除一个，等于一直在除

~~~java
private long getCnt(long x) {
    long ans = 0;
    while (x != 0) {
        ans += x / 5;
        x /= 5;
    }
    return ans;
}
~~~

### 试除法—求所有约数

求 n 的所有约数

~~~java
// 用数组
private static int[] res = new int[1000];
private static int cnt = 0;
private static void get_divisors(int x) {
    for (int i = 1; i <= x / i; i++) {
        if (x % i == 0) {
            res[cnt++] = i;
            if (i != x / i) res[cnt++] = x / i;
        }
    }
    Arrays.sort(res, 0, cnt);
}

// ArrayList
private static int n, a;

private static List<Integer> get_divisors(int x) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int i = 1; i <= x / i; i++) {
        if (x % i == 0) {
            list.add(i);
            if (x / i != i) list.add(x / i); // 如果两个数相同，添加一个就够了（题目要求不重复）
        }
    }
    list.sort((a, b) -> (a - b));
    return list;
}
~~~

### 约数个数

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230112153729038.png" alt="image-20230112153729038" style="zoom:80%;" />

性质1：1-n中约数个数：`n + 2/n + 3/n +...+ n/n`

性质2：int范围内，约数最多的一个树，有 1500多个约数



求 n 个正整数的**乘积**的约数个数

p都是素因子哈

~~~java
// 如果 N = p1^c1 * p2^c2 * ... *pk^ck
// 约数个数： (c1 + 1) * (c2 + 1) * ... * (ck + 1)
// 试除法求素因数之后 +1 乘起来

public class acw_870 {
    private static int mod = 1000000007, n;
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int n = sc.nextInt();
        HashMap<Integer, Integer> map = new HashMap<>();
        while (n-- > 0) {
            int x = sc.nextInt();
            // 分别求每个数的质因子，以及个数
            for (int i = 2; i <= x / i; i++) {
                if (x % i == 0) {
                    int t = 0;
                    while (x % i == 0) {
                        x /= i;
                        t++;
                    }
                    map.put(i, map.getOrDefault(i, 0) + t);
                }
            }
            if (x > 1) map.put(x, map.getOrDefault(x, 0) + 1);
        }

        long ans = 1;
        Collection<Integer> a = map.values();
        for (var i : a) ans = ans * (i + 1) % mod;
        System.out.println(ans);
    }
}
~~~



### 约数和

这里的 p 都是素因子

a^b % mod 的约数和 ，其实就是 p的次方扩大到了 c1 * b 其他的都一样，但是要递归二分求和

<img src="C:\Users\lenovo\Desktop\My MD\img\QQ图片20220720190316.jpg" alt="QQ图片20220720190316" style="zoom: 25%;" />

~~~java
// a：素数，b：素数个数 * 次方数
private static long sum(long a, long b, long mod) {
    if (b == 0) return 1;
    if (b % 2 != 0) return sum(a, b / 2, mod) * (1l + quick_power(a, b / 2 + 1, mod)) % mod;
    else return sum(a, b / 2 - 1, mod) * (1 + quick_power(a, b / 2 + 1, mod)) % mod + quick_power(a, b / 2, mod) % mod;
}
~~~



~~~java
//约数之和： (p1^0 + p1^1 + ... + p1^c1) * ... * (pk^0 + pk^1 + ... + pk^ck)
//给定一个正整数x，请输出它的约数和

private static int mod = 1000000007, n;

public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    int n = sc.nextInt();
    HashMap<Integer, Integer> map = new HashMap<>();
    while (n-- > 0) {
        int x = sc.nextInt();
        // 分别求每个数的质因子，以及个数
        for (int i = 2; i <= x / i; i++) {
            if (x % i == 0) {
                int t = 0;
                while (x % i == 0) {
                    x /= i;
                    t++;
                }
                map.put(i, map.getOrDefault(i, 0) + t);
            }
        }
        if (x > 1) map.put(x, map.getOrDefault(x, 0) + 1);
    }

    long ans = 1;
    Set<Integer> ks = map.keySet();
    for (var i : ks) {
        int cnt = map.get(i); // 次方数
        long t = 1;
        while (cnt-- > 0) t = (t * i % mod + 1) % mod; // 求各项的乘积和 t * p + 1 可以求 p^0 + p^1...p^c1
        ans = ans * t % mod;
    }
    System.out.println(ans);
}
~~~

### 多项式展开式

<img src="C:\Users\lenovo\Desktop\My MD\img\ac345982b2b7d0a2102c76a4c6ef76094b369a25.png" alt="img" style="zoom:67%;" />

![img](https://iknow-pic.cdn.bcebos.com/bd3eb13533fa828bfac5efdaf01f4134970a5a20?x-bce-process=image%2Fresize%2Cm_lfit%2Cw_600%2Ch_800%2Climit_1%2Fquality%2Cq_85%2Fformat%2Cf_auto)

### 求欧拉函数

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230115094755908.png" alt="image-20230115094755908" style="zoom:80%;" />

利用容斥原理推导

定理1：互质，公约数只有 1

定理2：1 和任何正整数互质



欧拉函数：φ(N)：是 1~ N中，与 N互质的数的个数

算数基本定理：N = p~1~^α1^+ p~2~^α2^+....+p~m~^αm^

则 φ(N) = N ( (1 - p~1~) /p~1~ *  (1 - p~2~) p~2~ * ....* (1 - p~m~ ) / p~m~ )

求一个数的欧拉函数的时间复杂度：O(sqrt(N))

#### 朴素求法

~~~java
private static int euler(int x) {
    int ans = x;
    // 试除法分解质因子
    for (int i = 2; i <= x / i; i++) {
        if (x % i == 0) {
            ans = ans / i * (i - 1);
            while (x % i == 0) x /= i;
        }
    }
    // 说明剩下的是个质数
    if (x > 1) ans = ans / x * (x - 1);
    return ans;
}
~~~

#### 欧拉筛

线性筛法求欧拉函数

~~~java
private static int N = 1000010, cnt;
private static int[] euler = new int[N], primes = new int[N];
private static boolean[] vis = new boolean[N]; // 标记是否访问过

private static void get_eulers(int n) {
    euler[1] = 1; // 特判，便于之后求和

    for (int i = 2; i <= n; i++) {
        if (!vis[i]) { // 说明 i 是质数
            primes[cnt++] = i;
            euler[i] = i - 1; // 质数 i 和 1~(i - 1) 都互质
        }

        for (int j = 0; primes[j] <= n / i; j++) {
            vis[primes[j] * i] = true;
            if (i % primes[j] == 0) {
                // i % primes[j] == 0，pj完全包括在 i 中，则 pj * i的质因子就是 i 的质因子，求euler函数时只需要把 i 换成 pj * i，即 euler[i] * pj 即可
                euler[primes[j] * i] = euler[i] * primes[j];
                break;
            }
			// pj不再是 i 的约数，需要额外呈上一个质因子 pj 即多乘一个 pj(1 - 1/pj)，即 (pj - 1)
            euler[primes[j] * i] = euler[i] * (primes[j] - 1);
        }
    }
}
~~~

#### 埃氏筛

~~~java
private static int[] euler = new int[1000005];
private static void get_eulers(int n) {
    for (int i = 1; i <= n; i++) euler[i] = i; // 初始化
    for (int i = 2; i <= n / i; i++) {
        if (euler[i] == i) { // 代表 i 是素数
            for (int j = i; j <= n; j += i) {
                euler[j] = euler[j] / i * (i - 1); 
            }
        }
    }
}
~~~

#### 欧拉定理

**同余**：a，b 模 n 的余数相等，就对 n 同余：a ≡ b (mod n)



若 a 与 n 互质，则 a^φ(n)^ ≡ 1 (mod n)，模 n 和 1 同余

a^φ(p)^ ≡ 1 (mod p)

==a与p互质==

> 1~n中：a1，a2.... a~φ(n)~是 和 n 互质的
>
> 则，因为 a 和 n 互质，就有 aa1，aa2..... aa~φ(n)~也和 n 互质，并且 % n 的余数和上面相同，只是顺序不同，aa1.... 这些数两两不同
>
> 因为 如果相同，aai ≡ aaj (mod n)，模 n 同余
>
> a(ai - aj) ≡ 0 (mod n)
>
> ai ≡ aj (mod n)，但是我们上面定义 a1.... a~φ(n)~，两两不同的，所以不成立，aa1....aa~φ(n)~两两不同
>
> 
>
> aa1 x aa2 ..... aa~φ(n)~ ≡ a1 x a2 ..... a~φ(n)~ (mod n)

> a^φ(n)^(a1 x a2 ..... a~φ(n)~) ≡ a1 x a2 ..... a~φ(n)~ (mod n)
>
> a^φ(n)^ ≡ 1 (mod n)

a^φ(p)^ ≡ 1 (mod p)

#### 费马定理

欧拉定理：a^φ(p)^ ≡ 1 (mod p)（a 与 p 互质）

==a与p互质 & p是质数==

若 **p 是质数**，则 φ(p) = p - 1

则欧拉定理可以转换，a^p-1^ ≡ 1 (mod p)

因为质数的 φ(p) = p - 1



### 快速乘

```java
static private long quick_multi(long a, long k) {
    long ans = 0;
    while(k > 0) {
        if((k & 1) == 1) ans += a;
        a += a;
        k >>= 1;
    }
    return ans;
}
```

### 快速幂

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230116095858220.png" alt="image-20230116095858220" style="zoom: 33%;" />

> 把 a^k^拆开，拆成上面的若干项，用二进制分解，2^0^ , 2^1^ ...... 其实就相当于把 k 拆成若干个次幂之和，底数相同，指数相加，结果还是 k
>
> 上面拆 k 的过程其实就是看 k相应的 二进制位 是 1 / 0，是 1 就乘

时间复杂度：O(logk)

```java
// 求 a ^ k
static private long quick_power(long a, long k) {
    long ans = 1;
    while (k > 0) {
        if ((k & 1) == 1) ans *= a;
        a *= a;
        k >>= 1;
    }
    return ans;
} 

// 求 a ^ k % p
// (k & 1) == 1，取 p 的最低位，看是否是 1
private static long quick_power(long a, long k, long p) {
    long ans = 1;
    while (k > 0) {
        if ((k & 1) == 1) ans = ans * a % p;
        k >>= 1;
        a = a * a % p;
    }
    return ans;
}
```



### ksm求逆元

b 与 p 互质情况下，若 a/b ≡ a*x (mod p)，则 x 为 b 的**乘法逆元**

可转化为 b * x ≡ 1 (mod p)

若 p 为质数，由费马定理得：b^p-1^ ≡ 1 (mod p)

b * b^p-2^ ≡ 1 (mod p)，所以 x = b^p-2^

~~~java
// 题目规定 p 是质数，我们使用费马定理需要 a p 是互质并且 p 是质数
// 只有 a 是 p 得倍数时，不互质
if (a % p == 0) System.out.println("impossible");
else System.out.println(quick_power(a, p - 2, p));

// 快速幂
private static long quick_power(long a, long k, long p) {
    long ans = 1;
    while (k > 0) {
        if ((k & 1) == 1) ans = ans * a % p;
        k >>= 1;
        a = a * a % p;
    }
    return ans;
}
~~~



### gcd

性质1：如果 a能被d整除，并且 b 能被 d 整除，则 ax + by 能被 d 整除

> a % d == 0，b % d == 0 ---->  (ax + by) % d == 0

定理1：gcd(a, b) = gcd(b, a % b)

最大公约数

欧几里得算法

~~~java
private static int _gcd(int a, int b) {
    return b == 0 ? a : _gcd(b, a % b);
}
~~~

### exgcd

常用于：

- 求解线性同余方程a * x ≡ c ( mod   b )
- 在 b 不等于 0 时求逆元



定理1：**裴蜀定理**：任意整数 a, b，存在一对非0整数 x, y, 满足a x + b y = gcd(a, b)

> 原理：
>
> 记 a b 的最大公约数gcd(a, b)为 d
>
> a 是 d 的倍数，b 是 d 的倍数，则 ax 和 by 也是 d 的倍数
>
> 所以，ax + by 也是d = gcd(a, b) 的倍数

求x, y，使得ax + by = gcd(a, b)

~~~java
// 数组模拟引用
private static int[] x = new int[1], y = new int[1];

private static int exgcd(int a, int b, int[] x, int[] y) {
    // b == 0, gcd(a, b) = a, 所以 ax + 0y = a
    // x = 1, y = 0
    if (b == 0) {
        x[0] = 1;
        y[0] = 0;
        return a;
    }
    
    /*
    	由欧几里得：gcd(a, b) = gcd(b, a % b)
    	由裴蜀定理&扩展欧几里得：ax + by = gcd(a, b), bx + (a % b)y = gcd(b, a % b)
    	bx1 + (a - a/b * b)y1 = gcd(a, b)
    	ay1 + b(x1 - a/b * b * y1) = ax + by
    	x = y1; y = (x1 - a/b*b*y1)，所以计算的时候需要先存一下x1用来算y，有点麻烦
    	
    	下面的代码在传的时候就进行了x0 y0 的翻转，简化了计算
    	因为是用数组来递归的，本质是不变的
    	交换之后
    	by + (a - a/b *b)x = ax1 + by1
    	ax + b(y - a/b *bx) = ax1 + by1
    	x1 = x, y1 = (y - a/b *bx)
    	多次交换保证了，x是不变的
    */
    int d = exgcd(b, a % b, y, x);
    y[0] -= (a / b) * x[0];
    return d;
}



// 数组模拟引用
private static int[] x = new int[1], y = new int[1];

private static int exgcd(int a, int b, int[] x, int[] y) {
    if (b == 0) {
        x[0] = 1;
        y[0] = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y[0] -= (a / b) * x[0];
    return d;
}
~~~



#### 求线性同余方程

~~~java
private static int[] x = new int[1], y = new int[1];

int d = exgcd(a, m, x, y);
if (b % d != 0) System.out.println("impossible");
else System.out.println(((long) x[0] * (b / d) % m));
/*
	转化，ax ≡ b(mod m) ==> 
	ax = my + b ==> 
	ax - my = b ==>
	令 y1 - -y ==> ax + my1 = d，这个式子就很熟悉了，裴蜀定理 + exgcd可以求出来 x 和 d
	如果 b % d == 0，即 b 是 d 的倍数，x就存在，只要ax和my同时乘一个 b/d 即可得到上面的式子
	否则，x 不存在
	因为d最小是固定的，是gcd(a, m)，但是可以放大倍数
*/

private static int exgcd(int a, int b, int[] x, int[] y) {
    if (b == 0) {
        x[0] = 1;
        y[0] = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y[0] -= (a / b) * x[0];
    return d;
}
~~~

#### exgcd求逆元

> ax ≡ b (mod m)
>
> 求逆元时，b = 1  ——>  	ax ≡ 1 (mod m)
>
> ax = my1 + 1 ——>  ax - my1  = 1    y = -y1
>
> ax + my = 1，  转化成了 `ax + by = gcd(a, b)` 的形式
>
> 这个式子就可以带入到 exgcd，当且仅当 exgcd == 1 时存在逆元

~~~java
// exgcd(a, b, x, y) == 1 ? (x % b + b) % b : -1  	-1代表没有逆元

private static int exgcd(int a, int b, int[] x, int[] y) {
    if (b == 0) {
        x[0] = 1;
        y[0] = 0;
        return a;
    }
    int d = exgcd(b, a % b, y, x);
    y[0] -= (a / b) * x[0];
    return d;
}
~~~



### 中国剩余定理

还没学



### 高斯消元

在O(n^3^)内，求解 n 个未知数，n个方程的多元线性方程组

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230117101417775.png" alt="image-20230117101417775" style="zoom:80%;" />

初等行列变换不影响行列式的值：

1. 把某一行乘一个非零数
2. 交换某两行
3. 把某行的若干倍加到另一行上

通过变化，变成一个上三角形阶梯矩阵



**高斯消元**：

列举每一列c

1. 找到绝对值最大的一行
2. 将该行提到最上面（已确定的行不变）
3. 将该行第一个数变成 1
4. 将这列下面其他所有行的这一列的数都变成 0

最后判断是否有解

> 下面这题未知数个数 n 和方程的个数 m 是相等的，我们下面讨论一般的情况，即 m 和 n 是任意的

解有三种情况：

1. 无解

   > r < m，且 b~r+1~ ..... b~m-1~ 不全为 0

2. 无穷多解

   > r = m 且 r < n 有无穷多解
   >
   > 或者
   >
   > r < m 但 b~r+1~ ..... b~m-1~ 全为 0 ，并且 r < n

3. 唯一解

   > r = m 且 r = n
   >
   > 或者
   >
   > r < m 但 b~r+1~ ..... b~m-1~ 全为 0 ，并且 r = n

有解的情况下：回减

~~~java
private static int N = 110, n;
private static double eps = 1e-6; // 作为判断是否是 0 的标准
private static double[][] a = new double[N][N];


int t = gauss();
if (t == 1) System.out.println("No solution");
else if (t == 2) System.out.println("Infinite group solutions");
else {
    // 有些案例会有 -0.00 需要特判一下
    for (int i = 0; i < n; i++) if (Math.abs(a[i][n]) < eps) a[i][n] = 0;
    for (int i = 0; i < n; i++) System.out.printf("%.2f\n", a[i][n]);
}


private static int gauss() {
    int c, r;
    // 列举每一列
    for (c = 0, r = 0; c < n; c++) {
        // 找到绝对值最大的一行
        int t = r;
        for (int i = r; i < n; i++)
            if (Math.abs(a[i][c]) > Math.abs(a[t][c]))
                t = i;
        // t 是找到这一列绝对值最大的数所在的行，后面回把这行置顶，这个 t 就不能再用了，直接用 r 即可

        if (Math.abs(a[t][c]) < eps) continue; // 这一列都是 0，直接跳过到下一列

        // 将该行提到最上面
        for (int i = c; i <= n; i++) {
            double tmp = a[r][i];
            a[r][i] = a[t][i];
            a[t][i] = tmp;
        }

        // 将这一行的第一个数变成 1，因为本行要同时除第一个数，所以从后往前遍历，方便计算
        for (int i = n; i >= c; i--) a[r][i] /= a[r][c];

        // 将下面所有行的这一列，变成 0，并且是整行都要乘一个相同的数
        for (int i = r + 1; i < n; i++)
            for (int j = n; j >= c; j--)
                a[i][j] -= a[r][j] * a[i][c];
        r++;
    }

    // 判断解的情况
    if (r < n) {
        for (int i = n - 1; i >= r; i--)
            if (Math.abs(a[i][n]) > eps)
                return 1; // 无解

        return 2; // 无穷多解
    }

    // 有唯一解，倒着逐行求解
    for (int i = n - 1; i >= 0; i--) {
        for (int j = i + 1; j < n; j++) {
            a[i][n] -= a[j][n] * a[i][j];
        }
    }

    return 0; // 有唯一解
}
~~~



### lcm

最小公倍数

~~~java
private static int _gcd(int a, int b) {
    return b == 0 ? a : _gcd(b, a % b);
}
private static int _lcm(int a, int b) {
    return a / _gcd(a, b) * b;
}
~~~

### 求组合数

> 给 n组询问，每组询问给定两个整数 a，b，输出 C(a, b) % mod

我们是根据范围来选择算法

#### 1. 递推+预处理

a, b <= 2000，直接预处理 2000 以内的所有的 C(a, b)，因为ab范围相乘是 4e6，可以过

递推公式: <img src="C:\Users\lenovo\Desktop\My MD\img\image-20220722091114617.png" alt="image-20220722091114617" style="zoom:80%;" />

~~~java
// 1. 递推，适用于数量较小
private static int N = 2010, mod = 1000000007;
private static int[][] c = new int[N][N];

private static void init() {
    c[0][0] = 1;
    for (int i = 1; i < N; i++) {
        for (int j = 0; j <= i; j++) // b <= a
            if (j == 0) c[i][j] = 1; // j = 0，从 i 个中选 0 个，只有一种选法
        else c[i][j] = (c[i - 1][j] + c[i - 1][j - 1]) % mod;
    }
}
~~~

#### 乘法逆元

**这种方法只适用于对答案模一个大质数的情况。**

乘法逆元

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20220722092206432.png" alt="image-20220722092206432" style="zoom: 80%;" />



<img src="C:\Users\lenovo\Desktop\My MD\img\image-20220722092320999.png" alt="image-20220722092320999" style="zoom:80%;" />

a, b <= 1e5，预处理式子中的 n!  m!，(n-m)! ，因为除法是没有 模的特性的，即先模后除是没用的。

所以，预处理 **n! % mod，m! 的逆元 % mod**

~~~java
private static int N = 100010, n, mod = 1000000007;
private static long[] fact = new long[N], infact = new long[N]; // 存储 a! % mod  以及 b!逆元 % mod

System.out.println((fact[a] * infact[b] % mod) * infact[a - b] % mod);

private static void init() {
    fact[0] = infact[0] = 1;
    for (int i = 1; i < N; i++) {
        fact[i] = fact[i - 1] * i % mod;
        infact[i] = infact[i - 1] * quick_power(i, mod - 2, mod) % mod;
    }
}
// 快速幂求逆元
private static long quick_power(long a, long b, long p) {
    long ans = 1;
    while (b > 0) {
        if ((b & 1) == 1) ans = ans * a % p;
        a = a * a % p;
        b >>= 1;
    }
    return ans;
}
~~~

#### 卢卡斯定理

**适用于对答案模一个质数的情况**。质数无论大小

a, b <= 1e18

公式：![image-20220722102303494](C:\Users\lenovo\Desktop\My MD\img\image-20220722102303494.png)

可以发现，当n m 都小于 p的时候，公式就没有用了 C(0, 0) = 1，所以这个公式是在 n >= p，或者是 m >= p 的时候使用的

 ~~~java
private static long p, n;

System.out.println(lucas(a, b));

private static long lucas(long a, long b) {
    if (a < p && b < p) return C(a, b); // m n 都小于 p 时，上面的公式只剩 C(n%p, m%p) = C(n, m)，zhi'jie
    return lucas(a / p, b / p) * C(a % p, b % p) % p;
}

// 通过定义求C(a,b)
private static long C(long a, long b) {
    long ans = 1;
    // C(a,b) = (a * a-1 * a-2 *...* a-b+1) / b!
    for (int i = 1, j = (int) a; i <= b; i++, j--) {
        ans = ans * j % p;
        ans = ans * quick_power(i, p - 2, p) % p;
    }
    return ans;
}

private static long quick_power(long a, long b, long p) {
    long ans = 1;
    while (b > 0) {
        if ((b & 1) == 1) ans = ans * a % p;
        a = a * a % p;
        b >>= 1;
    }
    return ans;
}
 ~~~

#### 质因数分解

求的是真实的组合数，即不取模 

把组合数中要乘或者除的数，都分解质因数，再减去分母的质因数，最后剩下来的质因数乘起来，边乘边mod即可

分解质因数：可以用欧拉筛把每个数的最小质因数求出来，把 i 的最小质因数的编号保存才 min_prime[i]

~~~java
/*当我们需要求出组合数的真实值，而非对某个数的余数时，分解质因数的方式比较好用：
    1. 筛法求出范围内的所有质数
    2. 通过 C(a, b) = a! / b! / (a - b)! 这个公式求出每个质因子的次数。 n! 中p的次数是 n / p + n / p^2 + n / p^3 + ...
    3. 分别求当前三个阶乘包含的每个质因子的数量，然后相减，存储数量，后面直接乘即可
    4. 用高精度乘法将所有质因子相乘
*/

private static int N = 5010, cnt;
private static int[] primes = new int[N]; // 存储所有质数
private static boolean vis[] = new boolean[N]; // 存储每个质数是否被筛掉
private static int[] sum = new int[N]; // 存储每个质数的次数

public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    int a = sc.nextInt();
    int b = sc.nextInt();
    get_primes(N - 1); // 求出所有素数

    for (int i = 0; i < cnt; i++) {
        int p = primes[i];
        sum[i] = get(a, p) - get(b, p) - get(a - b, p);
    }

    BigInteger bi = new BigInteger("1");

    for (int i = 0; i < cnt; i++) {
        if (sum[i] == 0) continue;
        BigInteger t = new BigInteger("1");
        for (int j = 0; j < sum[i]; j++) {
            BigInteger c = new BigInteger(String.valueOf(primes[i]));
            t = t.multiply(c);
        }
        bi = bi.multiply(t);
    }
    System.out.println(bi);
}
// 计算 x 的阶乘中含有的 p 的个数
private static int get(int x, int p) {
    int ans = 0;
    while (x > 0) {
        ans += x / p;
        x /= p;
    }
    return ans;
}

// 线性筛法求素数
private static void get_primes(int n) {
    for (int i = 2; i <= n; i++) {
        if (!vis[i]) primes[cnt++] = i;
        for (int j = 0; primes[j] <= n / i; j++) {
            vis[primes[j] * i] = true;
            if (i % primes[j] == 0) break;
        }
    }
}
~~~



#### 卡特兰数

给定 n 个 0 和 n 个 1，它们将按照某种顺序排成长度为 2n 的序列，求它们能排列成的所有序列中，能够满足任意前缀序列中 0 的个数都不少于 1 的个数的序列有多少个。

答案 mod 1e9 + 7

如果mod是一个 质数，可以用快速幂求逆元。如果不是质数，用 exgcd 求逆元

~~~java
C(2n, n) / (n + 1)
~~~

~~~java
private static int mod = 1000000007;
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    int n = sc.nextInt();

    long ans = 1;
    for (int i = 2 * n; i > n; i--) ans = ans * i % mod;
    for (int i = 1; i <= n; i++) ans = ans * quick_power(i, mod - 2, mod) % mod;
    ans = ans * quick_power(n + 1, mod - 2, mod) % mod;

    System.out.println(ans);
}

// 快速幂求逆元
private static long quick_power(long a, long b, long p) {
    long ans = 1;
    while (b > 0) {
        if ((b & 1) == 1) ans = ans * a % p;
        a = a * a % p;
        b >>= 1;
    }
    return ans;
}
~~~

~~~java
// exgcd 求逆元
// exgcd(a, b, x, y) == 1 ? (x % b + b) % b : -1  -1代表没有逆元

private static long exgcd(long a, long b, long[] x, long[] y) {
    if (b == 0) {
        x[0] = 1;
        y[0] = 0;
        return a;
    }
    long d = exgcd(b, a % b, y, x);
    y[0] -= a / b * x[0];
    return d;
}
~~~





### 逆序对

归并算法





逆序对的总数 = (n! / 2) * 不相等的数字对数

2 4 4 3 1 1，2和第一个4，是一不相等对，2和第二个4，也是一对，而 4 4 、1 1算是相等数字对 

任意两个不相等的数字，随机排列，形成逆序对的概率是 1/2。n! / 2就是一对数字，在所有排列中产生逆序对的期望个数

不相等对数：总对数 - 不相等对数的和（只算重复数字）

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20221120152933247.png" alt="image-20221120152933247" style="zoom:80%;" />



### 容斥

时间复杂度：2^n^ - 1

奇加偶减，(s1+s2+...) - (s1∩s2 + s1∩s3+----) - (s1∩s2∩s3)

用位运算，二进制枚举 来模拟选的过程，1~ 2^n^-1，看当前的 i 的二进制位，每一位是 1 还是 0

~~~java
private static int N = 20;
private static int[] a = new int[N];

public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    int n = sc.nextInt();
    int m = sc.nextInt();
    for (int i = 0; i < m; i++) a[i] = sc.nextInt();

    int ans = 0;
    for (int i = 1; i < (1 << m); i++) { // 二进制枚举，1 ~ 2^n 共 2^n 中情况，看 i 的二进制
        int cnt = 0, t = 1; // cnt 记录奇数个还是偶数个
        for (int j = 0; j < m; j++) { // 枚举 i 的每一位
            if ((i >> j & 1) == 1) { // 右移取每一位
                cnt++;
                if ((long) a[j] * t > n) { // 乘积大于n，不满足了，直接返回就好
                    t = -1;
                    break;
                }
                t *= a[j];
            }
        }
        if (t == -1) continue;
        if (cnt % 2 == 1) ans += n / t;
        else ans -= n / t;
    }

    System.out.println(ans);
}
~~~



### 公平组合游戏ICG

若一个游戏满足：

1. 由两名玩家交替行动。
2. 在游戏进程的任意时刻，可以执行的合法行动与轮到哪个玩家无关。
3. 不能行动的玩家判负。

则称该游戏为一个公平组合游戏。
NIM博弈属于公平组合游戏，但常见的棋类游戏，比如围棋，就不是公平组合游戏。因为围棋交战双方分别只能落黑子和白子，胜负判定也比较复杂，不满足条件2和条件3。

### Nim游戏

给定N堆物品，第i堆物品有Ai个。两名玩家轮流行动，每次可以任选一堆，取走任意多个物品，可把一堆取光，但不能不取。取走最后一件物品者获胜。两人都采取最优策略，问先手是否必胜。

我们把这种游戏称为NIM博弈。把游戏过程中面临的状态称为局面。

整局游戏第一个行动的称为先手，第二个行动的称为后手。若在某一局面下无论采取何种行动，都会输掉游戏，则称该局面必败。

所谓采取最优策略是指，若在某一局面下存在某种行动，使得行动后对面面临必败局面，则优先采取该行动。同时，这样的局面被称为必胜。我们讨论的博弈问题一般都只考虑理想情况，即两人均无失误，都采取最优策略行动时游戏的结果。
NIM博弈不存在平局，只有先手必胜和先手必败两种情况。

> 先手必胜状态：可以走到一个让对手先手必败的状态
>
> 先手必败状态：走不到任何一个让对手先手必败的状态

所有石子异或和 = 先手必败，不等于 0 先手必胜



### 有向图游戏

~~~java
给定一个有向无环图，图中有一个唯一的起点，在起点上放有一枚棋子。两名玩家交替地把这枚棋子沿有向边进行移动，每次可以移动一步，无法移动者判负。该游戏被称为有向图游戏。
任何一个公平组合游戏都可以转化为有向图游戏。具体方法是，把每个局面看成图中的一个节点，并且从每个局面向沿着合法行动能够到达的下一个局面连有向边。
~~~

### Mex运算

```java
设S表示一个非负整数集合。定义mex(S)为求出不属于集合S的最小非负整数的运算，即：
mex(S) = min{x}, x属于自然数，且x不属于S
```

### SG函数

```c++
在有向图游戏中，对于每个节点x，设从x出发共有k条有向边，分别到达节点y1, y2, …, yk，定义SG(x)为x的后继节点y1, y2, …, yk 的SG函数值构成的集合再执行mex(S)运算的结果，即：
SG(x) = mex({SG(y1), SG(y2), …, SG(yk)})
特别地，整个有向图游戏G的SG函数值被定义为有向图游戏起点s的SG函数值，即SG(G) = SG(s)。
```

### 有向图游戏的和

—— 模板题 AcWing 893. 集合-Nim游戏

```c++
设G1, G2, …, Gm 是m个有向图游戏。定义有向图游戏G，它的行动规则是任选某个有向图游戏Gi，并在Gi上行动一步。G被称为有向图游戏G1, G2, …, Gm的和。
有向图游戏的和的SG函数值等于它包含的各个子游戏SG函数值的异或和，即：
SG(G) = SG(G1) ^ SG(G2) ^ … ^ SG(Gm)
```

定理
有向图游戏的某个局面必胜，当且仅当该局面对应节点的SG函数值大于0。
有向图游戏的某个局面必败，当且仅当该局面对应节点的SG函数值等于0。

~~~java
private static int N = 110, M = 10010, n, k;
private static int[] s = new int[N], f = new int[M]; // f存储 sg函数的值
public static void main(String[] args) {
    Scanner sc = new Scanner(System.in);
    k = sc.nextInt();
    for (int i = 0; i < k; i++) s[i] = sc.nextInt();

    n = sc.nextInt();
    Arrays.fill(f, -1); // 便于之后的记忆化搜索
    int ans = 0;
    for (int i = 0; i < n; i++) {
        int x = sc.nextInt();
        ans ^= sg(x);
    }

    if (ans == 0) System.out.println("No");
    else System.out.println("Yes");
}
// 记忆化搜索求 sg 函数值
private static int sg(int x) {
    if (f[x] != -1) return f[x]; // 说明已经搜索过了

    Set<Integer> set = new HashSet<>(); // 存储 x 被取石子之后所有的可能性
    for (int i = 0; i < k; i++) {
        int t = s[i];
        if (x >= t) set.add(sg(x - t));
    }

    for (int i = 0; ; i++)
        if (!set.contains((Object)i)) return f[x] = i;

}
~~~



## 动态规划

状态个数 * 单个状态的计算时间

### 背包问题

#### 总结

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230405091522537.png" alt="image-20230405091522537" style="zoom:50%;" />

如果求出来的方程类似背包，且由 A区域推出当前数，则是倒序遍历

B区域转移过来是正序遍历

- 至多装capacity，求方案数 / 最大价值和
- 恰好capacity，求方案数  / 最大 / 最小价值和
- 至少capacity，求方案数 / 最小价值和

##### 恰好

~~~java
// 恰好的情况
class Solution {
    private int n, target;
    private int[] a;
    private int[][] f = new int[1010][1010];

    public int findTargetSumWays(int[] nums, int target) {
        n = nums.length;
        a = nums;
        target = target;

        for (int i = 0; i < n; i++) target += a[i];
        if (target < 0 || target % 2 != 0) return 0;

        for (int[] i : f) Arrays.fill(i, -1);

        target /= 2;
        return dfs(n - 1, target);
    }

    private int dfs(int u, int c) {
        if (u < 0) return c == 0 ? 1 : 0;
        if (c < a[u]) return dfs(u - 1, c); // 剩余的 c 小于当前数，不能选
        if (f[u][c] != -1) return f[u][c]; // 搜过了

        return f[u][c] = dfs(u - 1, c) + dfs(u - 1, c - a[u]); // 选 / 不选
    }
}
~~~

##### 至多

~~~java
class Solution {
    private int n, target;
    private int[] a;
    private int[][] f = new int[1010][1010];

    public int findTargetSumWays(int[] nums, int target) {
        n = nums.length;
        a = nums;
        target = target;

        for (int i = 0; i < n; i++) target += a[i];
        if (target < 0 || target % 2 != 0) return 0;

        for (int[] i : f) Arrays.fill(i, -1);

        target /= 2;
        return dfs(n - 1, target);
    }

    private int dfs(int u, int c) {
        if (u < 0) return 1; // 至多的情况，直接返回 1 即可，能递归到终点说明 c >= 0
        if (c < a[u]) return dfs(u - 1, c); // 剩余的 c 小于当前数，不能选
        if (f[u][c] != -1) return f[u][c]; // 搜过了

        return f[u][c] = dfs(u - 1, c) + dfs(u - 1, c - a[u]); // 选 / 不选
    }
}
~~~

##### 至少

~~~java
// 恰好的情况
class Solution {
    private int n, target;
    private int[] a;
    private int[][] f = new int[1010][1010];

    public int findTargetSumWays(int[] nums, int target) {
        n = nums.length;
        a = nums;
        target = target;

        for (int i = 0; i < n; i++) target += a[i];
        if (target < 0 || target % 2 != 0) return 0;

        for (int[] i : f) Arrays.fill(i, -1);

        target /= 2;
        return dfs(n - 1, target);
    }

    private int dfs(int u, int c) {
        if (u < 0) return c <= 0 ? 1 : 0;
        if (c < a[u]) return dfs(u - 1, c); // 剩余的 c 小于当前数，不能选
        if (f[u][c] != -1) return f[u][c]; // 搜过了

        return f[u][c] = dfs(u - 1, c) + dfs(u - 1, c - a[u]); // 选 / 不选
    }
}
~~~



#### 01背包

N件物品，每件物品有  体积v 和 权重w，背包容量是 V

每件物品最多使用 1 次。

#### 完全背包

每件物品有无限个

#### 多重背包

每件物品的个数不同，s~i~个

#### 分组背包

物品有 N组，每组物品若干个，每组中只能选一个。



### 线性DP



## 贪心



## 复杂度

<img src="C:\Users\lenovo\Desktop\My MD\img\image-20230128113548901.png" alt="image-20230128113548901" style="zoom:80%;" />

## 模拟

## C++STL

## Java特性

### 数组

- 二维数组排序

  ~~~java
  int[][] score, int k;
   Arrays.sort(score, (a, b) -> b[k] - a[k]);
  ~~~


#### 二分函数

binarySearch

方法的返回值有几种：

1.找到的情况下：如果key在数组中，则返回搜索值的索引。

 2.找不到的情况下：

 [1] 该搜索键在范围内，但不是数组元素，由1开始计数，得“ - 插入点索引值”； 

[2] 该搜索键在范围内，且是数组元素，由0开始计数，得搜索值的索引值；

 [3] 该搜索键不在范围内，且小于范围（数组）内元素，返回–(fromIndex + 1)； 

[4] 该搜索键不在范围内，且大于范围（数组）内元素，返回 –(toIndex + 1)。



**Java实现 lowerbound 和 upperbound**

lower_bound( begin,end,num)：从数组的begin位置到end-1位置二分查找第一个大于或等于num的数字，找到返回该数字的地址，不存在则返回end。

upper_bound( begin,end,num)：从数组的begin位置到end-1位置二分查找第一个大于num的数字，找到返回该数字的地址，不存在则返回end。

~~~java
int a = end, b = end; // 记录最后得到的位置
// upper
int l = begin, r = end - 1;
while (l < r) {
    int mid = l + r >> 1;
    if (f[mid] > num) r = mid;
    else l = mid + 1;
}
if (f[l] > num) a = l;


// lower
l = begin; r = end - 1;
while (l < r) {
    int mid = l + r >> 1;
    if (f[mid] >= num) r = mid;
    else l = mid + 1;
}
if (f[l] >= num)
~~~



### 堆

PriorityQueue

默认是最小堆

**最大堆**

```java
PriorityQueue<Integer> maxheap = new PriorityQueue<>(Collections.reverseOrder());
```

**自定义排序**

```java
Comparable<Integer> comparable = new Comparable<Integer>() {
        @Override
        public int compareTo(Integer o) {
        return 0;
    }
};
```



### 集合

ArrayList二维数组，每一行都是一个 list

可以直接 son[i] 访问第一维，第二维度直接 add到末尾，不用管位置

```java
List<Integer>[] son = new ArrayList[6005];
```



比较两个数组是否相等：`Arrays.equals(a, b)`



### 栈和队列

ArrayDeque和LinkedList这两者底层，一个采用数组存储，一个采用链表存储；

数组存储，容量不够时需要扩容和数组拷贝，通常容量不会填满，会有空间浪费；

链表存储，每次push都需要new Node节点，并且node节点里面有prev和next成员，也会有额外的空间占用。

这两个都可以同时作为队列和栈使用，虽然Java中有 Stack类，但是有历史遗留问题，建议使用上面两个代替。



**对比**

ArrayDeque无论是作为 队列，还是 栈，速度都略快于  LinkedList

所以建议使用 ArrayDeque



### Map

新学的函数：merge

将制定的Key和Value合并到Map中。如果传入的key不存在，则会将传入的key和value直接添加到map中；如果传入的key已经存在，则根据提供的BiFunction函数，将原有的value和传入的value进行合并后，更新到Map中去。

~~~java
Map<String, Integer> map = new HashMap<>();
map.put("A", 1);
map.put("B", 2);

map.merge("A", 2, Integer::sum); // 将A对应的值加2
map.merge("C", 3, Integer::sum); // 直接插入C对应的键值对

System.out.println(map);
~~~



### 计算时间

#### 计算时间差

Java 日期类中，可以使用Java 8新增的`java.time`包来计算时间差。具体步骤如下：

1. 使用`LocalDateTime`类创建两个日期时间对象

```java
LocalDateTime startDateTime = LocalDateTime.of(2023, 5, 1, 10, 10, 10);
LocalDateTime endDateTime = LocalDateTime.of(2023, 5, 5, 18, 18, 18);
```

2. 使用`Duration`类计算时间差

```java
Duration duration = Duration.between(startDateTime, endDateTime);
```

3. 获取时间差的值

```java
long seconds = duration.getSeconds();
long minutes = duration.toMinutes();
long hours = duration.toHours();
long days = duration.toDays();
```

完整代码如下：

```java
import java.time.Duration;
import java.time.LocalDateTime;

public class TimeDifferenceExample {
    public static void main(String[] args) {
        LocalDateTime startDateTime = LocalDateTime.of(2023, 5, 1, 10, 10, 10);
        LocalDateTime endDateTime = LocalDateTime.of(2023, 5, 5, 18, 18, 18);

        Duration duration = Duration.between(startDateTime, endDateTime);

        long seconds = duration.getSeconds();
        long minutes = duration.toMinutes();
        long hours = duration.toHours();

        System.out.println("时间差（秒）：" + seconds);
        System.out.println("时间差（分钟）：" + minutes);
        System.out.println("时间差（小时）：" + hours);
    }
}
```

输出：

```
时间差（秒）：349488
时间差（分钟）：5824
时间差（小时）：97
```







### 正则

字符串匹配

~~~java
String s = "daweffffefeeefaf1"; // dai'pi'p
Pattern pattern = Pattern.compile("[!\\[\\]?/]"); // 匹配规则
Matcher matcher = pattern.matcher(s);
System.out.println(matcher.find());
~~~

`text.trim().split("\\s+")`

- 正则表达式， 第一个 \ 是转义， \s ：匹配任意的一个空白字符，\s+：匹配任意多个空白字符

- `trim`：删除前导和尾部空格



### 数学

#### 指定底数的log

Math类中的log10方法来计算以10为底的对数，如果需要表示以其他底数的对数，可以使用换底公式：

log_a(b) = log_c(b) / log_c(a)



### 输入输出

#### 格式化输出

| 符号 | 意义       |
| ---- | ---------- |
| `%f` | 浮点类型   |
| `%s` | 字符串类型 |
| `%d` | 整数类型   |
| `%c` | 字符类型   |

```java
class Test {
    public static void main(String[] args) {
        int a = 12;
        char b = 'A';
        double s = 3.14;
        String str = "Hello world";
        System.out.printf("%f\n", s);
        System.out.printf("%d\n", a);
        System.out.printf("%c\n", b);
        System.out.printf("%s\n", str);
    }
}
```

#### 快速的输入输出

基本的

~~~java
BufferedReader sc = new BufferedReader(new InputStreamReader(System.in));
String[] s = sc.readLine().split(" ");
n = Integer.parseInt(s[0]);
k = Integer.parseInt(s[1]);

// 输出
br.write("\n");

br.close();
~~~



偷学姐的

直接写在 主类内

~~~java
static class FastScanner {
    // 看看有没有溢出，是否要用 long
    // sc.xxx;
    // out.print();
    // out.flush();
    // out.close();
    BufferedReader br;
    StringTokenizer st;

    public FastScanner(InputStream in) {
        br = new BufferedReader(new InputStreamReader(System.in));
        eat("");
    }

    public void eat(String s) {
        st = new StringTokenizer(s);
    }

    public String nextLine() {
        try {
            return br.readLine();
        } catch (IOException e) {
            return null;
        }
    }

    public boolean hasNext() {
        while (!st.hasMoreTokens()) {
            String s = nextLine();
            if (s == null) return false;
            eat(s);
        }

        return true;
    }

    public String next() {
        hasNext();
        return st.nextToken();
    }

    public int nextInt() {
        return Integer.parseInt(next());
    }

    public long nextLong() {
        return Long.parseLong(next());
    }

    public double nextDouble() {
        return Double.parseDouble(next());
    }
}

static FastScanner sc = new FastScanner(System.in);
static PrintWriter pw = new PrintWriter(new BufferedWriter(new OutputStreamWriter(System.out)));
~~~
