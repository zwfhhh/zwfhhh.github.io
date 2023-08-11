---
layout: post
title: 解题笔记
date: 2023-08-11 09:05:00 +0800
category: Algorithm
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/solve.jpg
icon: note
---


* content
{:toc}
# 解题笔记

## 博弈论问题

1.题目一般会给出几种选择的方式，比如取球博弈中的一次可以取几个球

2.一般是写一个dfs来遍历取球的各种情况，然后外面main函数里面在遍历每局的不同的初始球数

3.要注意用一个类似记忆化搜索的三维数组来记录已经出现过的情况，这样可以减少重复运算，要不然会超时

4.注意要输入输入的格式，一般不要将输入输出写在一个for里面

经典问题:  取球博弈

博弈问题可以参考 

> [(555条消息) 经典算法之博弈论 取球博弈_striner的博客-CSDN博客_博球程序算法](https://blog.csdn.net/striner/article/details/79780286)

## 数论

### 最大公因数

​     几个整数中公有的约数，叫做这几个数的[公约数](https://baike.baidu.com/item/公约数?fromModule=lemma_inlink)；其中最大的一个，叫做这几个数的**[最大公约数](https://baike.baidu.com/item/最大公约数?fromModule=lemma_inlink)**。例如：12、16的公约数有1、2、4，其中最大的一个是4，4是12与16的最大公约数，一般记为（12，16）=4。12、15、18的最大公约数是3，记为（12，15，18）=3。

### 最小公倍数

几个自然数公有的倍数，叫做这几个数的[公倍数](https://baike.baidu.com/item/公倍数?fromModule=lemma_inlink)，其中最小的一个自然数，叫做这几个数的**[最小公倍数](https://baike.baidu.com/item/最小公倍数?fromModule=lemma_inlink)**。例如：4的倍数有4、8、12、16，……，6的倍数有6、12、18、24，……，4和6的公倍数有12、24，……，其中最小的是12，一般记为[4，6]=12。12、15、18的最小公倍数是180。记为[12，15，18]=180。若干个[互质数](https://baike.baidu.com/item/互质数?fromModule=lemma_inlink)的最小公倍数为它们的乘积的[绝对值](https://baike.baidu.com/item/绝对值?fromModule=lemma_inlink)。

### 辗转相除法

> 辗转相除法是求两个自然数的最大公约数的一种方法，也叫[欧几里德算法](https://baike.baidu.com/item/欧几里德算法?fromModule=lemma_inlink)。

示例代码

```java
public class T {
 public static void main(String[] args) {
 int gcd = gcd(91, 49);
 System.out.println(gcd);
 }
 
 /**
 * greatest commond divisor
 * @param a
 * @param b
 * @return
 */
 public static int gcd(int a, int b) {
    return b!=0?gcd(b,a%b):a;
}
```

### 裴蜀定理

![image-20230114163909314](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230114163909314.png)

将这个裴蜀定理和辗转相除法相结合，就可以解决这个  蓝桥杯包子凑数的问题，上面这个裴蜀定理总的来说就a,b,c,....n这n个数进行遍历求这个最大公约数，如果最后这结果不等于这个1，则说明这些数一定凑不齐这个所有奇数，所以凑不齐的数字有无限个，如果等于1说明有有限个，在进行下一步

### 最小公倍数

**两个数的最小公倍数算法核心：最小公倍数=两整数的乘积/最大公约数**

所以要用[辗转相除法](https://so.csdn.net/so/search?q=辗转相除法&spm=1001.2101.3001.7020)求最大公约数

![image-20230114175527274](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230114175527274.png)

## 背包问题

[【算法笔记】背包模板-算法详解 - Z__X - 博客园 (cnblogs.com)](https://www.cnblogs.com/Michael-zx/p/12334836.html)

题目中带有不超过，后面的就是背包的最大容量

求最大值这个就是价值w,不超过这一类的东西就是类似各类商品的体积

## 搜索类题目

### 经典题目1

题目: 小猫爬山 链接：[acwing 165. 小猫爬山 - VanHope - 博客园 (cnblogs.com)](https://www.cnblogs.com/VanHa0101/p/15979462.html)

![image-20230204182652227](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230204182652227.png)



![image-20230204182812543](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230204182812543.png)

![image-20230204182831570](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230204182831570.png)

## 快速选择

![image-20230206152335311](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230206152335311.png)

输入多个数据时候，通过bufferdreader比scanner速度快很多

+号的优先级比这个>>的优先级高，所以l+r>>1,不用加括号

## 二分

一般使用来定位两种状态的==临界值==，把要查的值看作临界点

二分是一种思想方法，二分查找是一种思想方法的运用

### 整数的二分

整数的二分需要考虑边界问题，所以这个while里面的else需要 等于mid+1或者mid-1这就是边界问题

### 浮点数的二分

浮点数的二分不需要考虑这个边界问题，但是有一个需要注意的地方，就是这个while的条件是r-l>1e-（题目要求的小数位加2），也就是说当小于等于这个数时候可以认为此时的值就是答案，因为这个计算机存在存储的问题，存储浮点数的时候有误差

# 双指针算法

板子

```java
for(int i = 0,j = 0;i<n;i++) {
    while(j<=i&&check(i,j)) j++;
    //题目逻辑
   // xxxx
}
```

这个算法主要是可以将原来的O(n^2)变为O(n)的时间复杂度，但是如果要进行这样的优化的话，就要保证这个i,j这两个指针有相同的单调性

# 位运算

![image-20230208215306931](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230208215306931.png)

常用操作   n>>k&1

lowbit（）用来返回最后一个1,一般可以用来计算一个数的二进制有多少个1

![image-20230208220156142](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230208220156142.png)

# 并查集

![image-20230209201822941](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230209201822941.png)

# 哈希表

![image-20230210154849211](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230210154849211.png)

```java
//单链表的数组实现方式（类似静态链表）
int header = -1,e[N],ne[N],idx = 0;
//header头节点 ，e[i]第i个节点的值，ne[i]第i的几点的next指针的位置，idx已经用过的节点
//拉链法
void insert(int x) {
    e[idx] = x;
    ne[idx] = header;
    header = idx++;
}
boolean find(int x) {
    int k= (k%N+N)%N;//将这个负数范围亦可以映射到正数范围 suo'ji
    for(int i = header;i!=-1;i = e[i]) {
        if(e[i]==x) {
            return true;
        }
    }
    return false;
}
```

# Trie

用来高效存储和查找字符串集合的数据结构

# 数据范围反推算法复杂度记忆算法内容

![image-20230212223515229](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230212223515229.png)

# 拓扑排序

![image-20230213181640434](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230213181640434.png)

只有一个图不是环才可以有拓扑排序，否则没有

拓扑排序的前提是一个有向无环图

拓扑排序的具体实现：[(587条消息) 第十五章 图的BFS与拓扑序列_求图的bfs序列_Turing_Sheep的博客-CSDN博客](https://blog.csdn.net/weixin_72060925/article/details/128163868?csdn_share_tail={"type"%3A"blog"%2C"rType"%3A"article"%2C"rId"%3A"128163868"%2C"source"%3A"weixin_72060925"})

这个例子里面的图是通过邻接表来存储的图

# int的数据最大为2.1*10^9，long最大为2^63

# 图论的最短路径问题

![image-20230215203040066](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230215203040066.png)

## dijkstra算法

![image-20230215211212096](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230215211212096.png)

看着这张图可以帮助你理解这代码的实现

```java

//pu
import java.util.Arrays;
import java.util.Scanner;

public class Main {
    static int N = 510,n,m,max = 0x3f3f3f3f;
    static int g[][] = new int[N][N];//每两个点之间的距离
    static int dict[] = new int[N];//源点到每个点的最短距离
    static boolean st[] = new boolean[N];//每个点是否找到最小值



    public static int dijkstra() {
        Arrays.fill(dict,max);
        dict[1] = 0;
        //每次这个循环可以确定一个最小值
        for(int i = 0;i<n;i++) {
            int t = -1;//这里相当于笔记里面的额path的作用
            for(int j = 1;j<=n;j++) {
                if(!st[j]&&(t==-1||dict[j]<dict[t])) {
                    t = j;
                }
            }
            st[t] = true;
            //用已经确认的最短距离的点来更新后面的点
            //就是用1到t的距离加上t到j的距离来更新从1到j的长度
            for(int j= 1;j<=n;j++) {
                dict[j] = Math.min(dict[j],dict[t]+g[t][j]);
            }
           

        }
            if(dict[n]==max) return -1;
            else return dict[n];
    }
    public static void main(String[] args) {
        Scanner in = new Scanner(System.in);
         n = in.nextInt();
         m = in.nextInt();
        for(int i = 1;i<=n;i++) {
            Arrays.fill(g[i],max);
        }
         while(m-->0) {
             int a= in.nextInt();
             int b = in.nextInt();
             int c = in.nextInt();
             g[a][b] = Math.min(g[a][b],c);
         }

         int res = dijkstra();
        System.out.println(res);
    }
}
```

堆优化dijkstra

```java
package zuiduanlu;

import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.Scanner;

public class dijkstra {
	static int n,m,max = 0x3f3f3f3f,N = 150010,idx = 0;
	static int[] h = new int[N],e = new int[N],ne = new int[N],w = new int[N];
	static int dict[] = new int[N];
	static boolean st[] = new boolean[N];
	
	public static void add(int a,int b,int c) {
		w[idx] = c;
		e[idx] = b;
		ne[idx] = h[a];
		h[a]= idx++;
	}
	public static int dijkstra() {
		PriorityQueue<PIIs> queue = new PriorityQueue<PIIs>();
		Arrays.fill(dict, max);
		dict[1] = 0;
		queue.add(new PIIs(0,1));
		while(!queue.isEmpty()) {
			PIIs p = queue.poll();
			int dis = p.getDis();
			int t = p.getBianhao();
			if(st[t]) continue;
			st[t] = true;
			for(int i = h[t];i!=-1;i=ne[i]) {
				int j = e[i];
				if(dict[j]>dis+w[i]) {
					dict[j] = dis+w[i];
					queue.add(new PIIs(dict[j],j));
				}
			}
		}
		
		if(dict[n]==max) return -1;
		return dict[n];
	}
	
	public static void main(String[] args) {
		Scanner in  = new Scanner(System.in);
		n = in.nextInt();
		m = in.nextInt();
		Arrays.fill(h,-1);
		while(m-->0) {
			int a = in.nextInt();
			int b= in.nextInt();
			int c= in.nextInt();
			add(a,b,c);
		}
		int res = dijkstra();
		System.out.println(res);
	}
	
}
class PIIs implements Comparable<PIIs>{
	private int dis;
	private int bianhao;
	
	public PIIs(int a,int b) {
		dis = a;
		bianhao  = b;
	}
	
	
	
	public int getDis() {
		return dis;
	}



	public int getBianhao() {
		return bianhao;
	}



	@Override
	public int compareTo(PIIs o) {
		// TODO Auto-generated method stub
		return Integer.compare(dis, o.dis);
	}
	
}

```



# 完全背包

链接[AcWing 3. 完全背包问题 - AcWing](https://www.acwing.com/activity/content/code/content/2355819/)

# 进制转换

秦九韶算法,将b进制的s转换为十进制   String中都是数字，没给ABC啥的时候用这个，否则用封装类方法

```java
int qjz(string s,int b) {
    int ans = 0;
    for(int i=0;i<s.size();i++) 	
    	ans = ans*b + s[i]-'0';
    return ans;
}

//十进制转化为其他进制
Integer.toString(String s,int x);//x
```

![image-20230503225216671](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230503225216671.png)

![image-20230503230235721](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230503230235721.png)

==万能进制转换==

# 线性dp问题中开始的for()中的i的01开始的选择

我们一般dp中需要调用这个dp[i-1],则使用0开始,反之可以使用1开始

# 最短路问题

朴素djstla算法

![image-20230304081130620](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230304081130620.png)

![image-20230304081331043](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230304081331043.png)·

```java
static int g[][]//用来存储图的邻接矩阵
static int dict[]//到每个点的最短距离
static boolean st[]//判断每个点的最短路时候确定
int dijsktra() {
    Arrays.fill(dict,0x3f3f3f3f);
    dict[1]= 0;
    
    for(int i = 1;i<=n;i++) {
        int t   = -1;
        for(int j = 1;j<=n;j++) {
            if(!st[j]&&(t ==-1||dict[t]>dict[j])) {
                t = j;
            }
        }
        st[t] = true;
        
        for(int i = 1;i<=n;i++) {
            dict[j] = Math.min(dict[j],dict[t]+g[t][j]);
		}
    }
    if(dict[n]==0x3f3f3f3f) return -1;//不存在路径
    else return dict[n];
}
```

## 对于一个无权图，bfs可以用来求最短路，但是对于一个有权图,bfs就用不了了



# 并查集

```java
public static int find(int x) {
    if(p[x]!=x) p[x] = find(p[x]);
    return p[x];
}
//插入操作
p[find(a)] = find(b);

```

# java-------Arrary.sort 用 Comparator比较器定制排序方式（例如逆序） 

[(638条消息) java-------Arrary.sort 用 Comparator比较器定制排序方式（例如逆序）_arrays逆序排序_ZJE_ANDY的博客-CSDN博客](https://blog.csdn.net/u014453898/article/details/114681085)

> 这种方式主要是运用于这个图中的最短路问题，用于这个二维数组的排序

```java
public class Test5 {
 
    public static void main(String[] args) {
        int[][] arr = {{1,9},{2,5},{19,20},{10,11},{12,20},{0,3},{0,1},{0,2}};
        Arrays.sort(arr,new Comparator<int[]>(){
            @Override
            public int compare(int[] o1, int[] o2) {
                return o1[0]-o2[0];
            }
        });
 
 
        for(int[] a:arr){
            System.out.print(a[0]);
            System.out.print('-');
            System.out.print(a[1]);
            System.out.print(',');  //print:0-3,0-1,0-2,1-9,2-5,10-11,12-20,19-20,
        }
    }
}
```

![image-20230315213525130](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230315213525130.png)





# 字符串哈希

```java
public static long get(int l,int r) {
    return h[r]-h[l-1]*p[r-l+1];
}
//初始化
static int P = 131;
p[0] = 1;
for(int i = 1;i<=n;i++) {
    p[i] = p[i-1]*P;
    h[i] = h[i-1]*P+s.charAt(i-1);
}
```

# 单调栈

```java
while(n-->0) {
    int x  = in.nextInt();
    while(tt!=0&&arr[tt]>=x) tt--;
    if(tt = -1) System.out.println(-1);
    else System.out.println(arr[tt]);
    arr[++t] = x;
}

```

# kmp

```java
//初始化
char[] s//上面长的那根
char[] p//下面那根短的
int ne[]//next数组
//创建next[]数组
for(int i = 2,j = 0;i<=n;i++) {
    while(j!=0&&p[i]!=p[j+1]) j = ne[j];
    if(p[i]==p[j+1])  j++;
    ne[i] = j;
}

//使用
for(int i = 1,j = 0;i<=n;i++) {
    while(j!=0&&s[i]!=p[j+1]) j = ne[j];
    if(s[i]==p[j+1])  j++;
    if(j==n) {
        xxxxx
        j = ne
    }
}
```

# [背包九讲总结（背包问题） - AcWing](https://www.acwing.com/blog/content/5785/)

这里的背包问题非常详细

# 多重背包的二进制优化

```java
//首先对这个v[]和w[]数组进行一个重新分组赋值，从而转换为一个01背包问题
//要注意数组开多大的，先估算下2的多少次方等于体积的最大值x，用x*物品数的最大值就是N
static int v[] = new int[N];
static int w[] = new int[N];
static int cnt = 0;
//初始化
//遍历每一种物品，将每一种物品分为多个二进制组合，这样的话就等同于一个01背包问题，因为每个组合只能选择一次
while(n-->0) {
    int a = in.nextInt();
    int b = in.nextInt();
    int s = in.nextInt();
    int k = 1;
    while(s>=k) {
        cnt++;
        v[cnt] = a*k;
        w[cnt] = b*k;
        s-=k;
        k*=2;
    }
    if(s>0) {
        cnt++;
        v[cnt] = a*s;
        w[cnt] = b*s;
    }
    
}
n = cnt//总的组合数赋值给总的物品数，
    //这前面的都是在做一个转换的工作
//后面就是01背包问题
for(int i = 1;i<=n;i++) {
    for(int j = m;j>=v[i];j--) {
        f[j] = Math.max(f[j],f[j-v[i]]+w[i]);
    }
}
System.out.println(f[m]);
```

# 注意：对于一个数据量大小超过10^5时候，就可以使用java快读快写了

# 分组背包

> 每组物品有若干个，同一组内的物品最多只能选一个。

```java
//类似于01背包问题
//这里的v[i][k]表示第i组里面的第k个物品的价值
for(int i = 1;i<=n;i++) {
    for(int j = m;j>=0;j--) {
        for(int k = 0;k<=s[i];k++) {
            f[j] = Math.max(f[j],f[j-v[i][k]]+w[i][k]);
        }
    }
}
System.out.println(f[m]);
```

# 二进制枚举

[(631条消息) 二进制枚举--最通俗易懂的讲解_sugarbliss的博客-CSDN博客](https://blog.csdn.net/sugarbliss/article/details/81099340)

```java
for(int i = 0;i<(1<<n);i++) {//集合共有2^n个集合
    for(int j = 0;j<n;j++) {//遍历二进制的每一位
        if((i&(1<<j))==1) {//如果第j位置存在，则打印集合中的第j个元素
            System.out.println(a[j]);
        }
    }
}
```

![image-20230401200749950](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230401200749950.png)



利用二进制可以很好的解决组合问题和部分选择问题（多重背包的二进制优化）

```java
package 练习.binarysystem;

import java.util.Scanner;

public class boringdou {
    static int N = 17;
    static int v[] = new int[(1<<15)+1];
    static int a[] = new int[N];
    static int res;
    public static void main(String[] args) {
        Scanner in= new Scanner(System.in);
        int n= in.nextInt();
       for(int i = 0;i<n;i++) {
           a[i] = in.nextInt();
       }
       for(int i = 0;i<1<<n;i++) {
           for(int j = 0;j<n;j++) {
               if((i&(1<<j))!=0){
                   v[i]+=a[j];
               }
           }
       }
       for(int i = 0;i<(1<<n);i++){
           for(int j = 0;j<(1<<n);j++) {
               if((i&j)==0&&v[i]==v[j]) {
                   
                   res = Math.max(res,v[i]);
               }
           }
       }
        System.out.println(res);
    }
}

```

==上面的i&j等于0用来表示不同的组合==

# 单位换算

![image-20230305102951853](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230305102951853.png)

# 字典序

![image-20230305105407168](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230305105407168.png)

# eclipse快捷键

main方法快捷键： 输入main之后直接alt+/回车

System.out.println()快捷键：输入syso之后alt+/回车 

快速生成对象名字快捷键：alt+2按后，快速点击L

快速导入包：ctrl+alt+M



# 小知识点

Integer.toString(x,需要转到的进制数)快速的进制转换

![image-20230401200749950](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230401200749950.png)

[(696条消息) 利用包装类进行类型转换（包括进制转换）_包装类整数转进制_丶炜钦的博客-CSDN博客](https://blog.csdn.net/weixin_44521690/article/details/99705550)



## 最近刷题时，常遇到一个小问题，如何在不用标记的情况下，但在输入结束时，使scanner类自动结束呢？现在记下解决办法如下：

```java
import java.util.Scanner;



 *   按"Enter"，读取每行输入的有效数据及最后自动结束
      */
     public class ScannerEnd {
     public static void main(String[] args) {
     	Scanner sc = new Scanner(System.in);
     	while (sc.hasNextLine()) {
     		String str = sc.nextLine();
     		if (str.isEmpty())
     			break;
     		System.out.println(str);
     	}
     	sc.close();	
     }		
     }
```

## Java 如何输入多行字符串或者多个int数值？

# 树的由中序和（后序  or 先序）求遍历

```java
import java.util.*;

public class Main{
    static int N = 40;
    static int[] l = new int[N]; //存该节点的左子树的根
    static int[] r = new int[N];  //存该节点的右子树的根
    static int[] in = new int[N]; //存二叉树的中序遍历
    static int[] po = new int[N]; //存二叉树的后序遍历
    static Map<Integer, Integer> pos = new HashMap<>(); //存中序遍历每个节点的位置

    /**
     * il ，ir 表示 中序遍历区间的左右端点
     * pl ，pr 表示 后序遍历区间的左右端点
     **/

    public static int bulid(int il, int ir, int pl, int pr){
        int root = po[pr]; //后续遍历区间的最后一个节点就是根节点
        int k = pos.get(root); //找出根节点在中序遍历区间中的位置

        //如果中序遍历区间的左端点比根节点小，说明这个根节点有左子树，继续进行递归
        //判断右子树同理
        if (il < k) l[root] = bulid(il, k - 1, pl, k - 1 - il + pl);  
        if (ir > k) r[root] = bulid(k + 1, ir, k - il + pl, pr - 1); 

        return root; 
    }

    //bfs层序遍历
    public static void bfs(int root){
        Queue<Integer> q = new LinkedList<>();
        q.add(root);
        while (!q.isEmpty()){
            int t = q.poll();
            System.out.print(t + " ");
            if (l[t] != -1) q.add(l[t]);
            if (r[t] != -1) q.add(r[t]);
        }
    }

    public static void main(String[] args){
        Scanner scan = new Scanner(System.in);
        int n = scan.nextInt();
        for (int i = 0; i < n; i ++) po[i] = scan.nextInt();
        for (int i = 0; i < n; i ++){
            in[i] = scan.nextInt();
            pos.put(in[i], i);  //存下中序遍历每个节点的位置
        }
        Arrays.fill(l, -1);
        Arrays.fill(r, -1);
        int root = bulid(0, n - 1, 0, n - 1); //一开始中序跟后续都是整个区间
        bfs(root);
    }
}
```

# dfs和bfs

在两个方法都可以使用的问题上，bfs可以通过这个控制栈的大小来防止==爆栈==现象的发生

# 最小生成树

**如何用最小的“代价”用N-1条边连接N个点的问题**

```java
package zuixiaoshengchegnshu;

import java.util.Arrays;
import java.util.Scanner;

public class Kruskal {
	static int N=20010,n,m,res,cnt;
	static Edgs[] edgs=new Edgs[N];
	static int p[] =  new int[N];
	
	public static int find(int x) {
		if(p[x]!=x) p[x] = find(p[x]);
		return p[x];
	}
	
	public static void kruskal() {
		Arrays.sort(edgs,0,m);
		res = 0;
		cnt = 0;
		for(int i = 0;i<m;i++) {
			int a  = edgs[i].u;
			int b = edgs[i].v;
			int c = edgs[i].w;
			if(find(a)!=find(b)) {
				p[find(a)]= find(b);
				res +=c;
				cnt++;
			}
		}
		if(cnt<n-1) System.out.println("impossible");
		else System.out.println(res);
	}
	
	
	public static void main(String[] args) {
		Scanner in = new Scanner(System.in);
		n = in.nextInt();
		m = in.nextInt();
		for(int i = 0;i<m;i++) {
			int a = in.nextInt();
			int b = in.nextInt();
			int c = in.nextInt();
			edgs[i] = new Edgs(a, b, c);
		}
		for(int i =0;i<=m;i++ ) {
			p[i] = i;
		}
		kruskal();
	}

}
class Edgs implements Comparable<Edgs>{
	int u,v,w;
	
	public Edgs(int u,int v,int w) {
		// TODO Auto-generated constructor stub
		this.u = u;
		this.v = v;
		this.w = w;
	}
	
	
	
	@Override
	public int compareTo(Edgs o) {
		// TODO Auto-generated method stub
		return Integer.compare(w, o.w);
	}
	
}

```

# 最短路径 dijkstra

```java
package zuiduanlu;

import java.util.Arrays;
import java.util.PriorityQueue;
import java.util.Scanner;

public class dijkstra {
	static int n,m,max = 0x3f3f3f3f,N = 150010,idx = 0;
	static int[] h = new int[N],e = new int[N],ne = new int[N],w = new int[N];
	static int dict[] = new int[N];
	static boolean st[] = new boolean[N];
	
	public static void add(int a,int b,int c) {
		w[idx] = c;
		e[idx] = b;
		ne[idx] = h[a];
		h[a]= idx++;
	}
	public static int dijkstra() {
		PriorityQueue<PIIs> queue = new PriorityQueue<PIIs>();
		Arrays.fill(dict, max);
		dict[1] = 0;
		queue.add(new PIIs(0,1));
		while(!queue.isEmpty()) {
			PIIs p = queue.poll();
			int dis = p.getDis();
			int t = p.getBianhao();
			if(st[t]) continue;
			st[t] = true;
			for(int i = h[t];i!=-1;i=ne[i]) {
				int j = e[i];
				if(dict[j]>dis+w[i]) {
					dict[j] = dis+w[i];
					queue.add(new PIIs(dict[j],j));
				}
			}
		}
		
		if(dict[n]==max) return -1;
		return dict[n];
	}
	
	public static void main(String[] args) {
		Scanner in  = new Scanner(System.in);
		n = in.nextInt();
		m = in.nextInt();
		Arrays.fill(h,-1);
		while(m-->0) {
			int a = in.nextInt();
			int b= in.nextInt();
			int c= in.nextInt();
			add(a,b,c);
		}
		int res = dijkstra();
		System.out.println(res);
	}
	
}
class PIIs implements Comparable<PIIs>{
	private int dis;
	private int bianhao;
	
	public PIIs(int a,int b) {
		dis = a;
		bianhao  = b;
	}
	
	
	
	public int getDis() {
		return dis;
	}



	public int getBianhao() {
		return bianhao;
	}



	@Override
	public int compareTo(PIIs o) {
		// TODO Auto-generated method stub
		return Integer.compare(dis, o.dis);
	}
	
}

```



最短路和最小生成树的区别

![image-20230402094410098](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230402094410098.png)

# bfs和dfs算法

1.当问题一般为一些最短路径问题时候使用bfs是比较快的

区别：

一些参数u,blood传递参数

1，bfs中是将这些设置为Node的属性

2.dfs是在在方法中传参

# 邻接矩阵和邻接表的选择

一般来说，==稀疏图采用邻接表，稠密图采用邻接矩阵==

# 高精度

![image-20230513113320798](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230513113320798.png)

![image-20230513113328324](C:\Users\18249\AppData\Roaming\Typora\typora-user-images\image-20230513113328324.png)













