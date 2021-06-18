## Reservoir_Sampling 蓄水池抽样



- **在不知道文件总行数的情况下，如何从文件中随机的抽取一行？**

- **在一个无线长N个元素的数据流中，选择k个数，使得每个数被选中的概率 k/N**



### 前言
问题起源于编程珠玑Column 12中的题目10，其描述如下：

How could you select one of n objects at random, where you see the objects sequentially but you do not know the value of n beforehand? 
For concreteness, how would you read a text file, and select and print one random line, when you don’t know the number of lines in advance?

问题定义可以简化如下：在不知道文件总行数的情况下，如何从文件中随机的抽取一行？

解决方案：
在扫描第i个行时，以1/i的概率选择该行，选择意味着选取当前行并替换前面已经选到的那行。即，扫描到第一行时，选择；扫描到第二个行时，以1/2的概率替换第一个行；扫描到第三个行时，以1/3的概率替换前面选取的行，以此类推，直至扫描完一遍后，选取到的行号恰好是等概率的。
	
证明：设行号为[1…N]，即证明选取每行的概率为1/N。设1<= i <=N，选取到第i行的事件为第i次选取了第i行且后面每次均没有选中。概率为


​	![概率推导图](https://img-blog.csdn.net/20131109154200265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWluanVuc2hpc2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)



所以当随机选取k行时，就是最开始提出的问题。从上面的解决方案中得到启发，给出下面的选取策略：先选取前面的k行，从第k+1行开始，以k/i的概率选取一行并替换前面选到的k行中的一行，其中i为当前行号，i >= k+1。这样的选取过程也可以做到随机等概率。

### 解决提出的问题

1. 先选取前面的k行，

2. 从第k+1行开始，以k/i的概率选取一行并替换前面选到的k行中的一行，其中i为当前行号，i >= k+1。这样的选取过程也可以做到随机等概率。

设行号为[1…N]，即证明选取每行的概率为k/N。

当i > k时，选取第i行的概率为k/i。计算最终扫描完后选取到第i行的概率，即第i行在最终选取的k行中的概率。

这一事件发生是第i次扫描时选中该行，并且以后每次扫描一行时，那一行没有选中；或者选中但替换的不是前k行中的一行，直至第N行。概率

![概率推导图](https://img-blog.csdn.net/20131109154014015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWluanVuc2hpc2h1aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
当1<= i <=k时，即，是取上面的i=k得p=k/n。

综上，可得随机选取k行是等概率。证毕。



上述问题，存在不同的变种，结合实际的背景出现不同的形式。但均可以抽象为蓄水池抽样问题，又叫随机抽样问题。下面给出一些其他的形式：

1、在一个长度N未知的链表中随机选取k个元素，要求仅扫描链表一次且选取k个元素是等概率的。

2、从实时的搜素词中随机抽出k个词。



### 代码模板



```c++

#include <cstdlib>
#include <cstdio>

const int N = 1000000;
const int k = 10;

int m[N]; 
int res[k];

int main(){
	for(int i = 0; i < N; i++){
		m[i] = i;
	}

	for(int i = 0; i < k; i++){
		res[i] = i;
	}

	for(int i = k; i < N; i++){
		int r = rand() % (i+1);
		if(r < k){
			//printf("%d ,%d ,%d\n",r,i,m[i]);
			res[r] = m[i];
		}
	}

	//print res
	for(int i =0 ;i < k ;i++){
		printf("%d ",res[i]);
	}

	return 0;
}


```





[398. 随机数索引](https://leetcode-cn.com/problems/random-pick-index/)

[382. 链表随机节点](https://leetcode-cn.com/problems/linked-list-random-node/)