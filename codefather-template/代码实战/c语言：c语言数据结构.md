# C语言数据结构题目

## 1.单链表
设计一算法，逆置带头结点的动态单链表L。
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
struct list
{
	int data;
	struct list* next;
};
//问题一：链表
void showlist(struct list* head)
{
	while (head->next != NULL)
	{
		printf("%d->", head->data);
		head = head->next;
	}
	printf("%d", head->data);
	printf("\n");
}
void ininode(struct list* new, int item)//初始化结点
{
	new->data = item;
	new->next = NULL;
}
void reverse(struct list* head)
{
	struct list* pre = NULL;
	struct list* cur = head;
	while (cur!=NULL)
	{
		struct list* p= cur->next;//p为当前结点的后继结点
		cur->next = pre;//当前节点的指针指向该节点的前驱结点(头节点没有前驱结点，头节点指向NULL)
		pre = cur;//当前节点成为下一结点的前驱结点
		cur = p;//当前结点变为当前节点原本的后继节点
	}
}
int main()
{
	//1、创建一个递增链表
	struct list* head;
	struct list L[6];
	for (int i = 0; i < 6; i++)
	{
		L[i].data = 2*i;
		if (i == 5)
		{
			L[i].next = NULL;
			break;
		}
		L[i].next = &L[i + 1];
	}
	printf("1、初始链表:");
	showlist(&L[0]);
	printf("\n");
	printf("2、逆序之后的链表:");
	reverse(&L[0]);
	showlist(&L[5]);
}
```


## 2.10-16进制栈
用栈实现将10进制数16转换成二进制数并输出
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
#define Maxsize 10
struct Stack//定义栈
{
	int data[10];
	int top;
};
void push(struct Stack *sta,int item)//item进栈
{
	if (sta->top <= Maxsize - 1)//栈没满
	{
		sta->top++;
		sta->data[sta->top] = item;
	}
}
int pop(struct Stack *sta)//栈顶元素出栈，并返回栈顶元素的值
{
	if (sta->top > -1)//栈非空
	{
		return sta->data[sta->top--];
	}
}
int main()
{
	int x;//原始十进制数
	printf("原十进制数:");
	scanf_s("%d", &x);
	printf("\n");
	int d;//要转的进制数
	printf("要转换的进制:");
	scanf_s("%d", &d);
	printf("\n");
	struct Stack stack1;
	stack1.top=-1;//初始化栈stack1
	while (x>0)
	{
		push(&stack1, x % d);//x对d的余数依次进栈
		x = x / d;
	}
	while (stack1.top!=-1)
	{
		x = x * 10 + pop(&stack1);//栈内元素依次出栈加到x中
	}
	printf("转为%d进制后的数：%d", d, x);
	printf("\n");
	return 0;
}
```


## 3.行逻辑连接求MxN
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
#include <stdio.h>
#define MAXSIZE 10
struct Traid//三元组的每一个小组(即三元矩阵的每一行)
{
	int m, n;//非0元素的行列号
	int weight;//非0元素值
};
struct list
{
	struct Traid* data[10];//多个三元组构成的顺序表
	int firindex[10];//每行的第一个元素的序号
	int row, column, N;//原矩阵行数、列数、非0元素总数
};
void creat(struct list* L)//构造三元组矩阵
{
	printf("输入原矩阵行数、列数、非0元个数:");
	scanf("%d%d%d", &L->row, &L->column, &L->N);
	printf("依次输入每个非0元行号、列号、值:\n");
	int count = 0;
	while (count<L->N)
	{
		struct Traid* new = (struct Traid*)malloc(sizeof(struct Traid));
		scanf("%d%d%d", &new->m, &new->n, &new->weight);
		L->data[count] = new;//L的第count行指向new
		count++;
	}
}
void show(struct list* L)
{
	int count = 0;
	printf("三元组矩阵:\n");
	for (int i = 0; i < L->N; i++)
	{
		printf("%d %d %d\n", L->data[i]->m, L->data[i]->n, L->data[i]->weight);
	}
}
struct list* multip(struct list* M, struct list* N)
{
	int ctemp[10];
	struct list* Q = (struct list*)malloc(sizeof(struct list));
	Q->row = M->row;//乘积矩阵Q的原矩阵行数等于M的行数
	Q->column = M->column;//乘积矩阵Q的原矩阵行数等于M的行数
	Q->N = 0;//Q的非0元个数初始化为0
	//从M的二元矩阵第一行开始到最后一行，i是M的当前行
	for (int i = 0; i < M->N; i++)
	{
		for (int j = 0; j < N->N; j++)
		{
			if (M->data[i]->n==N->data[j]->m)
			{
				int sum = M->data[i]->weight * N->data[j]->weight;
				if (sum != 0)
				{
					struct Traid* new = (struct Traid*)malloc(sizeof(struct Traid));
					new->m = M->data[i]->m;
					new->n = N->data[j]->n;
					new->weight = sum;
					Q->data[Q->N] = new;
					Q->N++;
				}
			}
		}
	}	
	return Q;
}
int main()
{
	printf("M:\n");
	struct list* M = (struct list*)malloc(sizeof(struct list));
	creat(M);
	//show(M);
	printf("N:\n");
	struct list* N = (struct list*)malloc(sizeof(struct list));
	creat(N);
	//show(N);
	printf("结果Q:\n");
	struct list* Q = multip(M, N);
	show(Q);
	return 0;
}
```

## 4.用二叉链表表示的二叉树T，结点的值为整型。
请设计算法，求T中结点值大于0的结点数，并作为算法的返回值
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#include <string.h>
struct node//定义结点类型node
{
	char data;//结点值
	struct node* left;
	struct node* right;
};
struct node* creat_tree(struct node *tree)//输入先序序列构建二叉树,#表示空
{
	char s;
	scanf("%c", &s);
	if (s == '#')
	{
		tree = NULL;
	}
	else
	{
		tree = (struct node*)malloc(sizeof(struct node));
		tree->data = s;
		tree->left=creat_tree(tree->left);//建立左子树
		tree->right=creat_tree(tree->right);//建立右子树
	}
	return tree;
}
struct node* initia_node(struct node* root)//初始化结点
{
	root->data = "";
	root->left = NULL;
	root->right = NULL;
	return root;
}
void mid_read(struct node* root)//中序遍历
{
	if(root != NULL)
	{
		mid_read(root->left);
		printf("%c ", root->data);
		mid_read(root->right);
	}
}
int a[10];//存放所有大于0的结点值
int i = 0;
void lager(struct node* root)
{
	if (root == NULL)
	{
		return;
	}
	if (root->data > '0')
	{
		a[i] = root->data-'0';//输入的是字符形式，数字的字符-‘0’转为数字
		i++;
	}
	lager(root->left);
	lager(root->right);
}
int main()
{
	struct node* root = (struct node*)malloc(sizeof(struct node));
	root = initia_node(root);
	printf("1、输入先序遍历序列构建二叉树:");
	root = creat_tree(root);
	printf("\n中序遍历树:");
	mid_read(root);
	lager(root);
	printf("\n\n树中节点值大于0的值有:");
	for (int j = 0; j < i; j++)
	{
		printf("%d ", a[j]);
	}
	printf("\n");
	return 0;
}
```

## 5.前序线索二叉树后继节点
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#include <string.h>
struct node//定义结点类型node
{
	char data;//结点值
	struct node* left;
	struct node* right;
	int lflag, rflag;//标记是否找到前驱结点和后继结点(1找到、0没找到)
};
struct node* creat_tree(struct node *tree)//输入先序序列构建二叉树,#表示空
{
	char s;
	scanf("%c", &s);
	if (s == '#')
	{
		tree = NULL;
	}
	else
	{
		tree = (struct node*)malloc(sizeof(struct node));
		tree->data = s;
		tree->lflag = 0;//左标记为0
		tree->rflag = 0;//右标记为0
		tree->left=creat_tree(tree->left);//建立左子树
		tree->right=creat_tree(tree->right);//建立右子树
	}
	return tree;
}
struct node* pre;//上一个访问的结点
struct node* next;
void PreThreading(struct node* p)//线索化
{
	if (p==NULL)
	{
		return;
	}
	if (p->left==NULL) //p左儿子空
	{
		p->left = pre;//左儿子指向上一个访问的结点，即pre作为p的前驱结点
		p->lflag = 1;//p标记为已找到前驱结点
	}
	else//左儿子不为空
	{
		p->lflag = 0;//p标记为没找到前驱结点
	}
	if (pre!=NULL && pre->left==NULL) {//上一个访问的结点不为空且左儿子为空
		pre->right = p;//前驱节点的右儿子指向p，即p作为pre的后继节点
		pre->rflag = 1;//pre标记为已找到后继结点
	}
	else
	{
		p->rflag = 0;//p标记为没找到后继节点
	}
	pre = p;//p作为下一个访问结点
	if (p->lflag == 0)//p没找到前驱节点
	{
		PreThreading(p->left);//递归p的左儿子
	}
	if (p->rflag==0)//p没找到后继结点
	{
		PreThreading(p->right);
	}
}
struct node* PreNext(struct node* p)//找后继结点
{
	struct node* q;
	if (p->lflag == 0)//p没有前驱结点
	{
		q = p->left;//后继节点为左儿子
	}
	else//p有前驱结点
	{
		q = p->right;//后继结点为p的右儿子
	}
	return q;
}
struct node* initia_node(struct node* root)//初始化结点
{
	root->data = "";
	root->left = NULL;
	root->right = NULL;
	return root;
}
int main()
{
	struct node* root = (struct node*)malloc(sizeof(struct node));
	root = initia_node(root);
	printf("1、输入先序遍历序列构建二叉树:");
	root = creat_tree(root);
	printf("\n\n找A的后继结点:");
	printf("%c", PreNext(root)->data);
	printf("\n");
	return 0;
}
```

## 6.深度优先搜索
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#define maxn 10
int flag[8] = { 0 };
struct Edgnode//边结点
{
	int order;//该结点序号
	int weigth;//顶点v到该点的边权值
	struct Edgnode* link;//指向下一个v的邻接顶点
};
struct Vnode//顶点结点
{
	int data;//顶点序号
	struct Edgnode* nextV;//指向该顶点的第1个邻接顶点
};
struct Adjgraph//邻接表
{
	int n;//图的顶点数
	int m;//图的边数
	struct Vnode* node[maxn];//顶点表,存放图的所有顶点
};
//准备部分：邻接表的初始化创建及展示
void Init_AdjGraph(struct Adjgraph* A)//初始化邻接表
{
	printf("请输入图的顶点数:");
	int N;
	scanf("%d", &N);
	A->n = N;
	for (int i = 0; i < A->n; i++)//遍历便链表中顶点表的所有结点
	{
		struct Vnode* V = (struct Vnode*)malloc(sizeof(struct Vnode));//创建一个顶点结点V
		V->nextV = NULL;
		V->data = i + 1;
		A->node[i] = V;//图A的第i个结点为V
	}
}
void Insert(struct Adjgraph* A,int start,int end,int weight)
{
	struct Edgnode* e = (struct Edgnode*)malloc(sizeof(struct Edgnode));//新建邻接边结点e
	e->order = end;//b作为新的边结点e的序号
	e->link = NULL;//e的link值置空 
	e->weigth = weight;//边的权值
	struct Edgnode* d = A->node[start - 1]->nextV;//d指向第a个顶点的next域
	A->node[start - 1]->nextV = e;//让A的第a个顶点的邻接结点指向e
	e->link = d;//e指向d
}
void creat_AdjGraph(struct Adjgraph* A)
{
	printf("输入图的边数:");
	int m;
	scanf("%d", &m);
	A->m = m;//图的边数
	int count = 0;
	printf("输入始末顶点及其权重：\n");
	int a, b, weight;
	while (count < m)//遍历m次
	{
		scanf_s("%d %d %d", &a, &b, &weight);//a:起始结点序号，b：终止结点序号,weigth:<a,b>的边权值
		Insert(A, a, b, weight);
		count++;
	}
}
void show_Adj(struct Adjgraph* A)//展示邻接表
{
	for (int i = 0; i < A->n; i++)
	{
		printf("\n结点值：%d ", A->node[i]->data);//第i+1个结点
		if (A->node[i]->nextV == NULL)
		{
			continue;//A的第i+1个顶点没有邻接结点，跳过输出该顶点的边链表过程
		}
		printf("--->边链表%d ", A->node[i]->nextV->order);//该结点的第一个邻接结点值
		printf("weight：%d ", A->node[i]->nextV->weigth);//该结点的第一个邻接边权值
		if (A->node[i]->nextV->link == NULL)
		{
			continue;//没有第二个邻接结点,该节点的边链表输出结束，进入下一结点
		}
		struct Edgnode *E = A->node[i]->nextV->link;//让边界点E指向该结点的第二个邻接结点
		while (E!=NULL)
		{
			printf("--->%d ", E->order);//输出这个邻接结点序号
			printf("weigth:%d ", E->weigth);//输出邻接边权值
			E = E->link;//E指向该顶点的下一个邻接结点
		}
	}
}
//深度优先搜索(递归实现)
void DFS(struct Adjgraph* A,int i)//深度优先搜索
{
	flag[i] = 1;//第i+1个结点已被访问，标记为1
	struct Edgnode* e = (struct Edgnode*)malloc(sizeof(struct Edgnode));
	printf("%d ", A->node[i]->data);
	e = A->node[i]->nextV;//e指向A的第一个邻接结点
	while (e!=NULL)
	{
		if (flag[e->order - 1] == 0)
		{
			DFS(A, e->order - 1);//遍历A的邻接结点e
		}
		e = e->link;
	}
}
int main()
{
	struct Adjgraph* adj = (struct Adjgraph*)malloc(sizeof(struct Adjgraph));
	//创建邻接表
	Init_AdjGraph(adj);
	creat_AdjGraph(adj);
	//展示邻接表
	printf("\n邻接表：");
	show_Adj(adj);
	//深度优先搜索
	printf("\n\n1、深度优先搜索:");
	DFS(adj,0);
	printf("\n\n");
}
```

## 7.哈希表
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
#define N 10   //指定哈希表的长度
//自定义哈希函数
int hash(int value) 
{
    return value % 7;
}
//创建哈希表
void creatHash(int arr[8], int hashArr[N],int times[8]) 
{
    int index;
    //将序列中每个元素存储到哈希表
    for (int i = 0; i < 8; i++) 
    {
        int time = 1;
        index = hash(arr[i]);//index表示arr[i]在哈希表中的索引位置
        while (hashArr[index % N] != 0) //如果index位置已经有元素放置
        {
            index=(index+(int)pow(time,time))%10;//继续往后查找
            time++;//查找次数+1
        }
        hashArr[index] = arr[i];
        times[index] = time;//arr[i]的最终查找次数
    }
    printf("\nhash:");
    for (int i = 0; i < 10; i++)
    {
        printf("%d ", hashArr[i]);
    }
}
int main()
{
    int hashAdd;
    int hashArr[N] = { 0 };
    int arr[8] = { 9,01,23,14,55,20,84,27 };
    printf("arr:");
    for (int i = 0; i < 8; i++)
    {
        printf("%d ", arr[i]);
    }
    int times[8] = { 0 };
    creatHash(arr, hashArr,times);//构造hash表
    for (int i = 0; i < 8; i++)
    {
        if (hashArr[i] == 84)
        {
            printf("\n84的查找次数:%d", times[i]);
        }
    }
    return 0;
}
```


## 8.希尔排序
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
void shellSort(int* a, int len)//传入要排序的数组a，数组长度len
{
    int i, j, pre, t, gap;  // gap 为步长
    int h[3] = { 5,3,1 };
    for (int k = 0; k < 3; k++)// 进行三次分组插入排序
    {
        gap = h[k];//每一次分组的数组间隔（增量）
        for (int i = 0; i < gap; i++) // 变量 i 为每次分组的第一个元素下标 
        {
            for (j = i + gap; j < len; j += gap) //对每个小组进行直插排序，当gap为1时，就是直插排序
            {
                t = a[j];  // 备份a[j]的值
                pre = j - gap;  // pre初始化为该组a[j]的前一个元素的索引（与i相差gap长度）
                while (pre >= 0 && a[pre] > t)//前一个元素的索引>=0且前一个元素大于t
                {
                    a[pre + gap] = a[pre]; // 将在a[j]前且比a[j]大的元素向后移动一位
                    pre -= gap;
                }
                a[pre + gap] = t;
            }
        }
        printf("\n增量%d:", gap);
        for (i = 0; i < len; i++) // 排序后的结果的输出
        {
            printf("%d ", a[i]);
        }
        printf("\n");
    }
}
int main(void)
{
    int i, len, * a;
    printf("请输入要排的数的个数：");
    scanf("%d", &len);
    a = (int*)malloc(len * sizeof(int)); // 动态定义数组
    printf("请输入要排的数：\n");
    for (i = 0; i < len; i++)// 数组值的输入
    { 
        scanf("%d", &a[i]);
    }
    shellSort(a, len); // 调用希尔排序函数
    printf("\n");
    return 0;
}
```

## 9.顺序表:
已知L为非递减的顺序表，请设计算法删除L中重复的元素（即删除后使L表变为一递增表）
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
void input(int a[],int len)
{
	for (int i = 0; i < len; i++)
	{
		printf("%d ", a[i]);
	}
	printf("\n");
}
int main()
{
	printf("原顺序表:");
	int a[15] = { 1,1,2,2,3,3,3,5,7,7,7,8,9,10,11};
	input(a, 15);
	int i = 0;
	int len = 15;
	for (int i = 0; i < len; i++)//遍历还在数组中的每一个元素
	{
		for (int j = i + 1; j < len; j++)//遍历a[i]之后数组中的元素看是否等于a[i]
		{
			if (a[i] == a[j])//a[j]等于a[i]
			{
				for (int k = j; k < len-1; k++)
				{
					a[k] = a[k + 1];//让j到最后一个元素全部前移一个位置
				}
				j--;//原本j位置元素前移,为保证下一次判断仍从该j位置开始，j--
				len--;//前移一次，即出现一个重复，数组长度-1
			}
		}
	}
	printf("\n\n删除后顺序表:");
	input(a, len);
	return 0;
}
```

## 10.链表:
已知带头结点的动态单链表L中的结点是按整数值递增排列的，试写一算法将值x为的结点插入到表L中，使L仍然有序。
```c
struct list
{
	int data;
	struct list* next;
};
//问题一：链表
void showlist(struct list* head)
{
	while (head->next != NULL)
	{
		printf("%d ", head->data);
		head = head->next;
	}
	printf("%d", head->data);
	printf("\n");
}
void ininode(struct list* new, int item)//初始化结点
{
	new->data = item;
	new->next = NULL;
}
void add(struct list* head, int x)//插入结点值为x的结点到链表中
{
	while (head != NULL)
	{
		if ((head->data<x)&&(head->next==NULL||head->next->data>x))//x大于该节点的值且(该节点无下一结点or该节点的下一结点值大于x）则将x插在该节点后面
		{
			struct list* new=(struct list*)malloc(sizeof(struct list));
			ininode(new, x);//创建一个值为x的结点

			struct list* p;
			p = head->next;//指针p指向head结点原本的next域
			head->next = new;//head结点的next域指向值为x的结点
			new->next = p;//值为x的结点的next域指向head原本的next域
			return;
		}
		head = head->next;//head指向下一节点
	}
}
int main()
{
	//1、创建一个递增链表
	struct list* head;
	struct list L[6];
	for (int i = 0; i < 6; i++)
	{
		L[i].data = 2*i;
		if (i == 5)
		{
			L[i].next = NULL;
			break;
		}
		L[i].next = &L[i + 1];
	}
	printf("1、初始链表:");
	showlist(&L[0]);
	printf("\n");
	printf("2、插入x=7之后的链表:");
	add(&L[0], 7);
	showlist(&L[0]);
}
```

## 11.线性表:
在长度大于1的单循环链表中，既无头结点也无头指针。s为指向链表中某个结点的指针，试编写算法删除结点*s的直接前趋结点。
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
struct list
{
	int data;
	struct list* next;
};
void showlist(struct list* head)//展示循环链表，从开始到开始
{
	struct list* flag = head;
	int i = 0;
	while (head->next != NULL)
	{
		printf("%d ", head->data);
		if (i>0&&head == flag)
		{
			break;
		}
		head = head->next;
		i++;
	}
	printf("\n\n");
}
void pop(struct list* s)//删去s的直接前驱结点
{
	struct list* new = s;
	while (s!=NULL)
	{
		if (s->next->next == new)
		{
			s->next = new;
			break;
		}
		s = s->next;
	}
}
int main()
{
	//创建循环链表
	struct list peo[6];//peo[m]
	for (int i = 0; i < 6; i++)
	{
		peo[i].data = i;
		if (i == 5)
		{
			peo[i].next = &peo[0];
			break;
		}
		peo[i].next = &peo[i + 1];
	}
	printf("原始循环链表:(开始到开始):");
	showlist(&peo[0]);
	printf("删除1的直接前驱结点的循环链表(开始到开始):");
	pop(&peo[1]);
	showlist(&peo[1]);
}
```

## 12.邻接矩阵储存图
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <stdlib.h>
#include <string.h>
#define maxn 10
struct graph//定义图类
{
	int n;//顶点数
	int m;//边数
	int data[maxn];//顶点值
	int weigth[maxn][maxn];//权值
};
void ini_graph(struct graph* gra, int N,int M)//初始化有N个顶点的图gra
{
	gra->n = N;//顶点数为N；
	gra->m = M;//边数为0
	for (int i = 0; i < N; i++)
	{
		for (int j = 0; j < N; j++)
		{
			gra->weigth[i][j] = 0;//两顶点边权初始化为0
		}
	}
}
void show_graph(struct graph* gra)//展示图的邻接矩阵
{
	printf("图的邻接矩阵：\n");
	for (int i = 0; i < gra->n; i++)
	{
		for (int j = 0; j < gra->n; j++)
		{
			printf("%3d", gra->weigth[i][j]);
		}
		printf("\n");
	}
}
void creat_graph(struct graph* G)
{
	int count = 0;
	printf("输入顶点：\n");
	while (count < G->m)
	{
		int V1, V2;
		scanf("%d %d", &V1, &V2);
		G->weigth[V1 - 1][V2 - 1] = 1;
		G->weigth[V2 - 1][V1 - 1] = 1;
		count++;
	}
}
```

## 13.邻接表
```c
#define maxn 10
int flag[6] = { 0 };
struct Edgnode//边结点
{
	int order;//该结点序号
	int weigth;//顶点v到该点的边权值
	struct Edgnode* link;//指向下一个v的邻接顶点
};
struct Vnode//顶点结点
{
	int data;//顶点序号
	struct Edgnode* nextV;//指向该顶点的第1个邻接顶点
};
struct Adjgraph//邻接表
{
	int n;//图的顶点数
	int m;//图的边数
	struct Vnode* node[maxn];//顶点表,存放图的所有顶点
};
//准备部分：邻接表的初始化创建及展示
void Init_AdjGraph(struct Adjgraph* A)//初始化邻接表
{
	printf("请输入图的顶点数:");
	int N;
	scanf("%d", &N);
	A->n = N;
	for (int i = 0; i < A->n; i++)//遍历便链表中顶点表的所有结点
	{
		struct Vnode* V = (struct Vnode*)malloc(sizeof(struct Vnode));//创建一个顶点结点V
		V->nextV = NULL;
		V->data = i + 1;
		A->node[i] = V;//图A的第i个结点为V
	}
}
void Insert(struct Adjgraph* A,int start,int end,int weight)
{
	struct Edgnode* e = (struct Edgnode*)malloc(sizeof(struct Edgnode));//新建邻接边结点e
	e->order = end;//b作为新的边结点e的序号
	e->link = NULL;//e的link值置空 
	e->weigth = weight;//边的权值
	if (A->node[start - 1]->nextV == NULL || A->node[start - 1]->nextV->order < end)//如果第a个顶点还没有邻接结点或者第一个邻接结点的序号<e的序号，就把结点e插在顶点a后面
	{
		struct Edgnode* d = A->node[start - 1]->nextV;//d指向第a个顶点的next域
		A->node[start - 1]->nextV = e;//让A的第a个顶点的邻接结点指向e
		e->link = d;//e指向d
	}
	else//A的第一个邻接结点的序号小于结点e的序号b
	{
		struct Edgnode* d = A->node[start - 1]->nextV;//原本A的邻接结点
		while (d != NULL)//遍历A之后的邻接结点d，当d序号小于e的序号就一直往后找
		{
			if (d->order > end)//如果A的当前邻接结点序号>b
			{
				if (d->link == NULL || d->link->order < end)//d无下一邻接结点或下一个邻接结点序号<e，就把结点e插在结点d后面
				{
					struct Edgnode* f = d->link;//f指向结点d的下一结点
					d->link = e;//d的下一结点指向e
					e->link = f;//e的下一结点指向f
					break;
				}
			}
			d = d->link;//遍历下一个邻接结点
		}
	}
}
void creat_AdjGraph(struct Adjgraph* A)
{
	printf("输入图的边数:");
	int m;
	scanf("%d", &m);
	A->m = m;//图的边数
	int count = 0;
	printf("输入始末顶点及其权重：\n");
	int a, b, weight;
	while (count < m)//遍历m次
	{
		scanf_s("%d %d", &a, &b);//a:起始结点序号，b：终止结点序号,weigth:<a,b>的边权值
		Insert(A, a, b, 1);
		Insert(A, b, a, 1);
		count++;
	}
}
void show_Adj(struct Adjgraph* A)//展示邻接表
{
	for (int i = 0; i < A->n; i++)
	{
		printf("\n结点值：%d ", A->node[i]->data);//第i+1个结点
		if (A->node[i]->nextV == NULL)
		{
			continue;//A的第i+1个顶点没有邻接结点，跳过输出该顶点的边链表过程
		}
		printf("--->边链表%d ", A->node[i]->nextV->order);//该结点的第一个邻接结点值
		if (A->node[i]->nextV->link == NULL)
		{
			continue;//没有第二个邻接结点,该节点的边链表输出结束，进入下一结点
		}
		struct Edgnode *E = A->node[i]->nextV->link;//让边界点E指向该结点的第二个邻接结点
		while (E!=NULL)
		{
			printf("--->%d ", E->order);//输出这个邻接结点序号
			E = E->link;//E指向该顶点的下一个邻接结点
		}
	}
}
int main()
{
	struct graph* G = (struct graph*)malloc(sizeof(struct graph));
	ini_graph(G, 6,9);//初始画图:五个顶点
	//1）创建一个邻接矩阵；
	printf("1）创建一个邻接矩阵存储的图；\n");
	creat_graph(G);
	show_graph(G);
	//2）邻接表
	struct Adjgraph* adj = (struct Adjgraph*)malloc(sizeof(struct Adjgraph));
	//创建邻接表
	Init_AdjGraph(adj);
	creat_AdjGraph(adj);
	//展示邻接表
	printf("\n邻接表：");
	show_Adj(adj);
}
```

## 14.冒泡排序
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
#include <time.h>
//随机生成1-10000内的数字添加进数组
void srand(unsigned int seed);//生成随机种子
void input(int a[],int len)
{
    printf("数组元素:");
    for (int i = 0; i < len; i++)
    {
        printf("%d ", a[i]);
    }
    printf("\n");
}
void random_x(int arr[],int len)//生成随机数x
{
    srand((unsigned)time(NULL));//每次随机生成的时间不同，生成的种子也不同
    for (int i = 0; i < len; i++)
    {
        arr[i] = rand() % 10000 + 1;//根据随机生成的种子得到1-10000内的随机数x
    }
}
//复制数组
void copy(int p[], int a[],int len)
{
    for (int i = 0; i < len; i++)
    {
        p[i] = a[i];
    }
}
//第一组：冒泡排序
void bubble_sort(int a[],int len)
{
    for (int i = 0; i < len-1; i++)
    {
        for (int j = 0; j < len - i - 1; j++)
        {
            if (a[j] > a[j + 1])
            {
                int t = a[j];
                a[j] = a[j + 1];
                a[j + 1] = t;
            }
        }
    }
}
//第二组：快速排序
void quick_sort(int a[], int low, int high)//按房价快速排序
{
    int i = low;
    int j = high;
    int tmp = a[low];//第一个元素任命为房价序列的中间分界线，左边比他小，右边比他大
    if (i > j)  //如果下标i大于下标j，函数结束运行
    {
        return;
    }
    while (i != j)
    {
        while (a[j] >= tmp && j > i)//j行数据的房价>分界房价
        {
            j--;
        }
        while (a[i] <= tmp && j > i)//j行数据的房价<分界房价
        {
            i++;
        }
        if (j > i)
        {
            //交换数据列
            int new = a[j];
            a[j] = a[i];
            a[i] = new;
        }
    }
    a[low] = a[i];
    a[i] = tmp;
    quick_sort(a, low, i - 1);
    quick_sort(a, i + 1, high);
}
//第三组：堆排序
void swap(int* a, int i, int j)//交换数组a中的第i个和第j个结点
{
    int t = a[i];
    a[i] = a[j];
    a[j] = t;
}
int get_max(int* a, int i, int j, int las)//返回左右儿子中的较大值,如果有一个的序号超过可更改的最大序号las，选另一个，如果两个都超过则返回一个很小的值
{
    if (i >= las)//i结点左右儿子均超过可更改的最大序号las
    {
        return -1000;//返回一个很小的值
    }
    else if (j >= las)//i结点右儿子超过可更改的最大序号las
    {
        return a[i];//返回左儿子的值
    }
    else
    {
        return a[i] > a[j] ? a[i] : a[j];//返回左右儿子中较大的
    }
}
void creat_heap(int* a, int i, int las)
{
    int father = a[i];//父节点的值
    int max = get_max(a, 2 * i + 1, 2 * i + 2, las);//max：父亲的两个儿子的较大值
    while (father < max)//父亲小于其中一个儿子
    {
        if (max == a[2 * i + 1])//max=左儿子
        {
            swap(a, i, 2 * i + 1);//交换父亲和左儿子
            i = 2 * i + 1;//父亲结点的序号变成原本左儿子的序号，继续判断左儿子的子树的变化
        }
        else if (max == a[2 * i + 2])//max=右儿子
        {
            swap(a, i, 2 * i + 2);//交换父亲和右儿子
            i = 2 * i + 2;//父亲结点的序号变成原本左儿子的序号，判断右儿子的子树的变化
        }
        if (i >= (las - 1) / 2)//第i个结点已排好序不可再更改
        {
            break;
        }
        max = get_max(a, 2 * i + 1, 2 * i + 2, las);//max：父亲的两个儿子的较大值
    }
}
void heap_sort(int a[],int len)
{
    int i = len / 2 - 1;//最后一个非叶结点的序号
    int count = 0;
    //构建大顶堆
    while (i >= 0)
    {
        creat_heap(a, i, len);
        i--;
    }
    //交换首结点和未排序的末尾结点
    int j = len - 1;
    while (j > 0)
    {
        swap(a, 0, j);//交换a的第一个和第j个,此后第j个结点已经完成排序不可再被交换
        creat_heap(a, 0, j);//调整根节点的树
        j--;
    }
}

void sort(int len)//len<时输出排序前后的数组并输出相应耗时,len>50仅输出耗时
{
    int* a = (int*)malloc(len*sizeof(int));
    clock_t t0, t1;//分别开始、结束的时刻
    random_x(a, len);
    int* p = (int*)malloc(len * sizeof(int));//用于储存未排序数组
    copy(p, a,len);//将生成的随机数组的指拷贝到p中
    if (len < 50)
    {
        printf("原始");
        input(a, len);
    }
    //冒泡
    t0 = clock();
    bubble_sort(a, len);
    t1 = clock();
    if (len < 50)
    {
        printf("\nA、冒泡排序后");
        input(a, len);
    }
    printf("\n冒泡排序用时%d", t1 - t0);
    //快排
    copy(a, p, len);//将有序数组a重置未原始未排序数组
    t0 = clock();
    quick_sort(a, 0, len-1);
    t1 = clock();
    if (len < 50)
    {
        printf("\n\nB、快速排序后");
        input(a, len);
    }
    printf("\n快速排序用时%d", t1 - t0);
    //堆排序
    copy(a, p, len);//将有序数组a重置未原始未排序数组
    t0 = clock();
    heap_sort(a, len);
    t1 = clock();
    if (len < 50)
    {
        printf("\n\nC、堆排序后");
        input(a, len);
    }
    printf("\n堆排序用时%d", t1 - t0);
}
int main()
{
    printf("1、数组长度为20\n");
    sort(20);
    printf("\n==========================================================\n");
    printf("\n2、数组长度为5000\n");
    sort(5000);
    printf("\n==========================================================\n");
    printf("\n3、数组长度为10000\n");
    sort(10000);
    printf("\n==========================================================\n");
    printf("\n4、数组长度为50000\n");
    sort(50000);
    printf("\n==========================================================\n");
    return 0;
}
```

## 15.10-16进制互转
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include<stdio.h>
#include<string.h>
#include <stdio.h>
#include <string.h>
//10进制转16进制的字符串形式再转为10进制
int inttohex(int  aa)
{
    char  buffer[3];  //用于存放转换好的十六进制字符串。由于只要支持2位整数，所以长度3即可
    if (aa / 16 < 10)    //计算十位，并转换成字符 
        buffer[0] = aa / 16 + '0';
    else
        buffer[0] = aa / 16 - 10 + 'A';
    if (aa % 16 < 10)    //计算个位，并转换成字符
        buffer[1] = aa % 16 + '0';
    else
        buffer[1] = aa % 16 - 10 + 'A';
    buffer[2] = '\0';    //字符串结束标志
    return  atoi(buffer);
}
//16进制字符串转10进制整数
int toten(char a[]) {
    int i = 0, re = 0;
    while (a[i])       //当读取到输入的最后一个字符之后，读取到0，跳出循环
    {
        if (a[i] >= '0' && a[i] <= '9') {
            re = re * 16 + a[i] - '0';
        }          //a[i]-'0'利用ASCII码值差值求该位的值
        else if (a[i] >= 'a' && a[i] <= 'f') {
            re = re * 16 + a[i] + 10 - 'a';
        }          //a[i]-'a'利用ASCII码值差值求该位的值
        else if (a[i] >= 'A' && a[i] <= 'F') {
            re = re * 16 + a[i] + 10 - 'A';
        }          //a[i]-'A'利用ASCII码值差值求该位的值
        i++;       //从最高位依次往低一位遍历
    }
    return re;
}
```


