---
layout: post
title: 关于BFS和DFS算法
date: 2023-08-10 23:40:00 +0800
category: Algorithm
thumbnail: https://cdn.jsdelivr.net/gh/zwfhhh/media-library/image/algorithm.jpg
icon: note
---


* content
{:toc}
># 关于BFS和DFS算法
>
>># BFS
>## 广度优先搜索算法BFS是一种使用队列来保存中间状态并最终求解的算法，其搜索过程和 “湖面丢进一块石头激起层层涟漪” 类似。以走迷宫为例，最开始人在起点，首先把走一步能到的位置全都放在队列里，走一步所到达的位置全部放入完毕后，比如A，B两点，然后计算两步可以到达的位置，此时需要先从队列里取出头节点A，然后把A一走能走到的位置插入到队列中(假如是C、D)，然后再把B点取出，把B一步能走到的位置插入到队列中(假如是E、F)，此时队列变为C、D、E、F。此时队列中保存的是从起点两步可达的地方。以此类推。BFS就是利用队列先进先出的特性，把中间节点的信息保存在队列中，直到达到最优解。
>## 使用场景：一般用于求解最优解，如最小步数，最短路径等，但是现在也会有些题目会给程序员挖坑，比如求解最大的解，这时候脑海中需要判断这个最优解是不是涟漪最先到达的位置，如果相反是最后到达的位置，使用BFS显然就不对了，会造成计算的空间复杂度高而使得内存爆掉。  

## 特点：队列保存中间状态，状态的维护更新较为复杂，算法的空间复杂度高

>># DFS
## 深度优先搜索算法DFS是一种利用递归实现的搜索算法。其搜索过程和 “不撞南墙不回头” 类似。以走迷宫为例，DFS的走法是从起点一直走，假如从a-b-c-d，此时发现d点无路可走，需要退回c点继续走，假如是a-b-c-e-f，f是出口，就走出了迷宫。这个过程中就使用了递归技术。
## 使用场景：适合搜索全部的解，比如迷宫从起点走到终点的路径个数等等，这时显然需要遍历所有的解空间，而使用BFS需要记录很多的中间状态，使用DFS则不需要，使用递归非常简单的实现，空间复杂度低。
## 特点：空间复杂度低，递归效率差，可能爆栈

## 相同点
##BFS和DFS在求解过程中，一般都需要剪枝，而剪枝的好坏决定了程序运行的性能，剪枝的策略有很多，需要根据题目进行思考，一般的剪枝策略包含：无效值剪枝、最优性剪枝等。
##另外在BFS、DFS中一般需要记录状态的变化，如走迷宫，BFS需要记录当前位置是从起点走几步到的，如X点，可能走3 、4 、5步都可以到，但是只有第3步才有意义，因为第4、5步走的肯定不如第3步走到X点更优。在DFS走迷宫时，也需要记录该点是否走过。
##状态的记录及剪枝对于算法非常重要。
>>##DFS算法模板   
```void dfs(depth，...) 
{   if(目标解)  
    {  
        ...记录当前全局状态信息到结果中  
        return;  
    }  

    for(...)从当前状态扩展下一状态  
    {  
        if(非法状态 ) continue；
        if(剪枝条件) continue；
        修改全局状态变量；  
         dfs(depth+1, ...)；  
         还原全局状态变量；  
        }  
    }  }

>>##BFS算法模板  
```void bfs() 
{  
    queue<Node> q;
    q.push(head); //将起点插入到列表中
    isvisited[head]=true; // 标记首节点已经被访问： 
    while(!q.empty())
    {
        int temp=q.front();
        q.pop();
        for(...)//从当前状态扩展下一状态  
        {  
            if(非法状态 ) continue；
            if(剪枝条件)  continue；//比如被访问过
            Node next = ....;
            isvisited[next]=true;
            q.push(next);
    }}



#对于DFS和回溯的区别

##1.dfs只需要visited[i] = true,，不用visited[i]=false,保证每个点只遍历一次
##2.回溯既有visited[i]=true的操作，也有visited[i]=false的操作，保证在枚举另一种可能解的时候程序可以正确执行


#一般对于需要遍历所有情况的题
##1.visited[i] = false是有必要写的，平且一般只有写在dfs函数里面的if的最后，或者dfs函数的for结束之后


#一般来说，题目需要记录步数的时候就需要在dfs的形参上带上num记录步数的变量

#总结
##1.首先要确定输入的的方式，看输入的行之间有没有空格（注意：当这个输入单个字符的时候通常通过先输入一行的字符串，然后通过tocharArray(),再循环赋值给每行的数组， 反正基本上都要写两个for循环）
###（1）如果有空格，在写第二个for的时候记得i++换为i+=2,还要注意是从1开始输入的，还是0，我认为从1开始更方便
###（2）如果没有空格,第二个for还是i++
##2.其二，如果需要计算路径长度的最大或最小或遍历所有路径，都需要给dfs函数带上一个计算步数的形参step or num.
##3.其三，要看题目是否给出终点
###（1）如果给出了终点，那么dfs中就需要写上递归结束的条件
###（2）如果没有给出终点，那么dfs中就直接写上下左右的遍历
##4.其四，一般只有需要回溯的题目才需要visited[i]=false,只需要遍历的题目不需要，两者都需要加上visited[i] = true;
##5.当visited[i] = false是有必要写的情况，一般只有写在dfs函数里面的if的最后，或者dfs函数的for结束之后，如果不能分析理解，则带入尝试（我一般都是带入尝试）



对于bfs算法，了解的不多，这种算法一般只用于解最短路径和最小步数有奇效，一般用Ｌｉｎｋｅｄｌｉｓｔ来代表队列，里面的泛型需要设置一个节点类放在里面，这种算法用的较少，了解即可






#注意：
##1，当输入的是一个字符二维数组，每行之间没有空格，可以先将每行的字符串通过tochararray转化为字符数组，然后再输入二维数组中
##2，当输入的是一个字符二维数组，每行之间有空格，直接用两个for循环，然后直接map[i][j] = in.next().charAt(0);这种方法不会产生索引超出的异常
