# C语言数据结构系统

## 1.城市信息系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
//数据准备
struct inform
{
	char city[5];//城市名称
	int population;//人口
	int price;//房价
};
struct list
{
	struct inform* data[100];//存储所有城市数据
	int count;//表中已有数据条数
};
void init_L(struct list* L)//初始化数据
{
	char city[5][5] = { "北京","天津","重庆","深圳","浙江"};
	int people[5] = { 100,67,99,72,80 };
	int price[5] = { 200,130,100,250,170 };
	L->count = 5;
	for (int i = 0; i < 5; i++)
	{
		struct inform* new = (struct inform*)malloc(sizeof(struct inform));
		strcpy(new->city, city[i]);
		new->population = people[i];
		new->price = price[i];
		L->data[i] = new;
	}
}
//1、显示
void show(struct list* L)//显示数据
{
	printf("  城市  人口  房价\n");
	printf("========================\n");
	for (int i = 0; i < L->count; i++)
	{
		printf("  %s  ", L->data[i]->city);
		printf("%3d", L->data[i]->population);
		printf("%6d\n", L->data[i]->price);
	}
}
//2、添加
void add(struct list* L)//添加数据
{
	struct inform* new = (struct inform*)malloc(sizeof(struct inform));
	printf("输入添加的城市:");
	scanf("%s", &new->city);
	printf("输入添加城市的人口:");
	scanf("%d", &new->population);
	printf("输入添加城市的房价:");
	scanf("%d", &new->price);
	L->data[L->count] = new;//L的最后一条inform变为new
	L->count++;
}
//3、删除
void pop(struct list* L)//删除数据
{
	char s[5];
	printf("输入要删除的城市:");
	scanf("%s", s);
	for (int i = 0; i < L->count; i++)
	{
		if (strcmp(L->data[i]->city, s) == 0)//找到要删除的inform行
		{
			L->count--;//表L数据总数-1
			for (int j = i; j < L->count; j++)
			{
				L->data[j] = L->data[j + 1];//i之后所有数据前移
			}
			break;
		}
	}
	printf("删除成功!\n");
}
//4、修改
void modify(struct list* L)//修改数据
{
	printf("\n要修改数据的城市:");
	char s[5];
	scanf("%s", &s);
	int index = -1;
	for (int i = 0; i < L->count; i++)
	{
		if (strcmp(L->data[i]->city, s) == 0)//找到要修改的inform
		{
			index = i;
			break;
		}
	}
	if (index == -1)
	{
		printf("未找到该城市！");
	}
	else
	{
		printf("修改后的城市名:");
		scanf("%s", &L->data[index]->city);
		printf("修改后的人口数:");
		scanf("%d", &L->data[index]->population);
		printf("修改后的房价:");
		scanf("%d", &L->data[index]->price);
	}
}
//5、排序
void quick_sort(struct list*L,int low, int high)//按房价快速排序
{
	int i = low;
	int j = high;
	struct inform* tmp = L->data[low];//第一个元素任命为房价序列的中间分界线，左边比他小，右边比他大
	if (i > j)  //如果下标i大于下标j，函数结束运行
	{
		return;
	}
	while (i != j)
	{
		while (L->data[j]->price >= tmp->price && j > i)//j行数据的房价>分界房价
		{
			j--;
		}
		while (L->data[i]->price <= tmp->price && j > i)//j行数据的房价<分界房价
		{
			i++;
		}
		if (j > i)
		{
			//交换数据列
			struct inform* new = L->data[j];
			L->data[j] = L->data[i];
			L->data[i] = new;
		}
	}
	L->data[low] = L->data[i];
	L->data[i] = tmp;
	quick_sort(L, low, i - 1);
	quick_sort(L, i + 1, high);
}
void Bubble_sort(struct list* L)//按人口冒泡排序
{
	for (int i = 0; i < L->count-1; i++)
	{
		for (int j = 0; j < L->count - i - 1; j++)
		{
			if (L->data[j]->population > L->data[j+1]->population)
			{
				struct inform* new = L->data[j];
				L->data[j] = L->data[j+1];
				L->data[j+1] = new;
			}
		}
	}
}
void sort(struct list* L)//两种排序
{
	char s[5];
	printf("输入排序依据(房价/人口):");
	scanf("%s", &s);
	if (strcmp(s, "房价") == 0)//依据房价排序
	{
		quick_sort(L, 0, L->count-1);
		printf("\n按房价从小到大排列为:\n\n");
		show(L);
	}
	else//依据人口排序
	{
		Bubble_sort(L);
		printf("\n按人口从小到大排列为:\n");
		show(L);
	}
}
//6、查找
//6.1对分查找
void half_search(struct list* L)//对半查找L中房价为x的数据行
{
	int x;
	printf("查找房价为该值的数据项:");
	scanf("%d", &x);
	printf("\n");
	quick_sort(L, 0, L->count - 1);//先排序
	//reset_link(L);
	int left = 0; int right = L->count;
	while (left <= right)
	{
		if (L->data[(left + right) / 2]->price == x)
		{
			printf("  %s  ", L->data[(left + right) / 2]->city);
			printf("%3d", L->data[(left + right) / 2]->population);
			printf("%6d\n", L->data[(left + right) / 2]->price);
			return;
		}
		else if (L->data[(left + right) / 2]->price > x)//x在查找区间的左边
		{
			right = (left + right) / 2 - 1;
		}
		else if (L->data[(left + right) / 2]->price < x)//x在查找区间的右边
		{
			left = (left + right) / 2 + 1;
		}
	}
	printf("表中无房价为%d的城市！\n",x);
}
//6.2二叉查找
struct node//定义结点类型node
{
	struct inform* data;//结点值
	struct node* left;
	struct node* right;
};
void init_node(struct node* root, struct inform* item)//初始化结点
{
	root->left = NULL;
	root->right = NULL;
	root->data = item;
}
void creat_binary(struct node* root, struct inform* x)//构建/在二叉查找树中插入元素x
{
	if (x->population < root->data->population)//x人口小于root节点人口
	{
		if (root->left == NULL)//root节点无左儿子
		{
			struct node* new_left = (struct node*)malloc(sizeof(struct node));
			init_node(new_left, x);
			root->left = new_left;//让root结点的左儿子指向以x为data值构成的结点
		}
		else//root结点有左儿子
		{
			creat_binary(root->left, x);//在root左子树中寻找正确位置插入x
		}
	}
	else if (x->population > root->data->population)//x人口>root节点人口
	{
		if (root->right == NULL)//root节点无右儿子
		{
			struct node* new_right = (struct node*)malloc(sizeof(struct node));
			init_node(new_right, x);
			root->right = new_right;//让root结点的右儿子指向以x为data值构成的结点
		}
		else
		{
			creat_binary(root->right, x);//在root右子树中寻找正确位置插入x
		}
	}
}
struct inform* p[10];//存放所有大于指定房价的值
int count = 0;//大于指定房价的城市个数
void lager(struct node* root, int x)//查找二叉树中房价大于x的城市数据
{
	if (root == NULL)
	{
		return;
	}
	if (root->data->population > x)
	{
		p[count] = root->data;//该条数据添加到数组
		count++;
	}
	lager(root->left, x);
	lager(root->right, x);
}
void tree_search(struct list* L)
{
	//构建按人口大小查找二叉树
	struct node* root = (struct node*)malloc(sizeof(struct node));
	init_node(root, L->data[0]);
	for (int i = 1; i < L->count; i++)
	{
		creat_binary(root, L->data[i]);
	}
	int x;
	printf("查找人口大于该值的数据项:");
	scanf("%d", &x);
	lager(root, x);
	printf("\n");
	for (int i = 0; i < count; i++)
	{
		printf("  %s  ", p[i]->city);
		printf("%3d", p[i]->population);
		printf("%6d\n", p[i]->price);
	}
}
void search(struct list* L)//两种查找
{
	printf("输入1（查找房价为x的城市信息）/2（查找人口大于x的城市信息）");
	int x;
	scanf("%d", &x);
	if (x == 1)
	{
		half_search(L);
	}
	else
	{
		tree_search(L);
	}
}
int main() {
	int c;
	struct list* L = (struct list*)malloc(sizeof(struct list));
	init_L(L);
	printf("\t\t城市管理系统\n");
	printf("========================================\n");
	printf("1.显示已录入城市信息:\n");
	printf("2.添加城市信息:\n");
	printf("3.删除城市信息\n");
	printf("4.修改城市信息:\n");
	printf("5.按人口/房价对城市信息按从小到大排序:\n");
	printf("6.查找城市信息\n");
	printf("7.退出系统\n");
	printf("========================================\n");
	do {
		printf("\n请选择:");
		scanf("%d", &c);
		printf("\n");
		if (c == 1)
		{
			show(L);//1、显示
		}
		else if (c == 2)
		{
			add(L);//2、添加
		}
		else if(c==3)
		{
			pop(L);//3、删除
		}
		else if (c == 4)
		{
			modify(L);//4、修改
		}
		else if (c == 5)
		{
			sort(L);//5、排序
		}
		else if (c == 6)
		{
			search(L);//查找
		}
		printf("==============================\n");
	} while (c > 0 && c<7);
	return c;
}
```

[//]: # (![运行结果]&#40;./runpic/城市信息系统.png&#41;)


## 2.图结构解决外卖配送距离最优.txt
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
//邻接表实现dijkstra算法
#define INF 1000
#define maxn 10
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
	printf("请输入图的顶点数(奶茶店个数):");
	int N;
	scanf("%d", &N);
	A->n = N;
	for (int i = 0; i < A->n; i++)//遍历便链表中顶点表的所有结点
	{
		struct Vnode* V = (struct Vnode*)malloc(sizeof(struct Vnode));//创建一个顶点结点V
		V->nextV = NULL;
		V->data = i;
		A->node[i] = V;//图A的第i个结点为V
	}
}
void Insert(struct Adjgraph* A, int start, int end, int weight)//在邻接表中插入序号start到end的边
{
	struct Edgnode* e = (struct Edgnode*)malloc(sizeof(struct Edgnode));//新建邻接边结点e
	e->order = end;//b作为新的边结点e的序号
	e->link = NULL;//e的link值置空 
	e->weigth = weight;//边的权值
	if (A->node[start]->nextV == NULL || A->node[start]->nextV->order > end)//如果第a个顶点还没有邻接结点或者第一个邻接结点的序号大于e的序号，就把结点e插在顶点a后面
	{
		struct Edgnode* d = A->node[start]->nextV;//d指向第a个顶点的next域
		A->node[start]->nextV = e;//让A的第a个顶点的邻接结点指向e
		e->link = d;//e指向d
	}
	else//A的第一个邻接结点的序号小于结点e的序号b
	{
		struct Edgnode* d = A->node[start]->nextV;//原本A的邻接结点
		while (d != NULL)//遍历A之后的邻接结点d，当d序号小于e的序号就一直往后找
		{
			if (d->order < end)//如果A的当前邻接结点序号小于b
			{
				if (d->link == NULL || d->link->order > end)//d无下一邻接结点或下一个邻接结点序号大于e，就把结点e插在结点d后面
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
void creat_AdjGraph(struct Adjgraph* A)//创建邻接表
{
	printf("输入图的边数(两两奶茶店之间的单向直接通路数):");
	int m;
	scanf("%d", &m);
	A->m = m;//图的边数
	int count = 0;
	printf("输入始末奶茶店序号0-%d及其距离：\n", A->n - 1);
	int a, b, weight;
	while (count < m)//遍历m次
	{
		scanf_s("%d %d %d", &a, &b, &weight);//a:起始结点序号，b：终止结点序号,weigth:<a,b>的边权值
		Insert(A, a, b, weight);
		Insert(A, b, a, weight);
		count++;
	}
}
int get_weigth(struct Adjgraph* A, int start, int end)//获取序号start到end结点的距离
{
	if (A->node[start]->nextV == NULL)//start结点没有邻接结点
	{
		return INF;//start到end距离为无穷
	}
	else
	{
		struct Edgnode* e = A->node[start]->nextV;//e指向start的第一个邻接结点
		while (e != NULL)
		{
			if (e->order == end)//e的顺序等于end
			{
				return e->weigth;//返回边权值
			}
			e = e->link;
		}
	}
	return INF;
}
void Adj_dijkstra(struct Adjgraph* A,int start)//求start结点到其余个节点的最短路径
{
	int dis[7];
	int pre[7] = { 0 };//前驱节点
	int flag[7] = { 0 };//flag[i]=1表示i已确定最短路径
	pre[start] = -1;
	int target = -1;
	for (int i = 0; i < A->n; i++)
	{
		dis[i] = get_weigth(A, start, i);//初始化距离为边权值
	}
	dis[start] = 0;
	int count = 0;
	while (count < A->n)
	{
		int min = INF;
		for (int i = 0; i < A->n; i++)
		{
			if (i!=start&&flag[i] == 0 && dis[i] < min)
			{
				min = dis[i];
				target = i;
			}
		}
		flag[target] = 1;
		for (int i = 0; i < A->n; i++)
		{
			if (i!=start&&flag[i] == 0 && dis[target] + get_weigth(A, target, i) < dis[i])
			{
				dis[i] = dis[target] + get_weigth(A, target, i);
				pre[i] = target;
			}
		}
		count++;
	}
	for (int i = 0; i < A->n; i++)
	{
		printf("%d-%d ", start, i);
		printf("distance=%5d", dis[i]);
		printf("  path:%d", i);
		int pre_index = pre[i];
		while (pre_index > -1)
		{
			printf("<-%d", pre_index);
			pre_index = pre[pre_index];
		}
		printf("\n");
	}
}
void show_Adj(struct Adjgraph* A)//展示邻接表
{
	for (int i = 0; i < A->n; i++)
	{
		printf("\n奶茶店序号：%d ", A->node[i]->data);//第i+1个结点
		if (A->node[i]->nextV == NULL)
		{
			continue;//A的第i+1个顶点没有邻接结点，跳过输出该顶点的边链表过程
		}
		printf("--->相邻奶茶店: %d ", A->node[i]->nextV->order);//该结点的第一个邻接结点值
		printf("distance：%d ", A->node[i]->nextV->weigth);//该结点的第一个邻接边权值
		if (A->node[i]->nextV->link == NULL)
		{
			continue;//没有第二个邻接结点,该节点的边链表输出结束，进入下一结点
		}
		struct Edgnode* E = A->node[i]->nextV->link;//让边界点E指向该结点的第二个邻接结点
		while (E != NULL)
		{
			printf("--->%d ", E->order);//输出这个邻接结点序号
			printf("distance:%d ", E->weigth);//输出邻接边权值
			E = E->link;//E指向该顶点的下一个邻接结点
		}
	}
}
//深度优先搜索(递归实现)
flag[8] = { 0 };
void DFS(struct Adjgraph* A, int i)//深度优先搜索
{
	flag[i] = 1;//第i+1个结点已被访问，标记为1
	struct Edgnode* e = (struct Edgnode*)malloc(sizeof(struct Edgnode));
	printf("%d ", A->node[i]->data);
	e = A->node[i]->nextV;//e指向A的第一个邻接结点
	while (e != NULL)
	{
		if (flag[e->order] == 0)
		{
			DFS(A, e->order);//遍历A的邻接结点e
		}
		e = e->link;
	}
}
//广度优先搜索(队列实现)
#define Maxsize 10
struct Queue//定义队列结构体
{
	int front;//队首下标
	int rear;//要入队元素下标
	struct Vnode* node[10];
	int count;//队列元素个数
};
void initial_Queue(struct Queue* Q)//初始化队列
{
	Q->front = 0;
	Q->count = 0;
	Q->rear = 0;
}
void inque(struct Queue* Q, struct Vnode* root)//入队
{
	Q->node[Q->rear] = root;
	Q->rear = (Q->rear + 1) % Maxsize;
	Q->count++;
}
struct node* outque(struct Queue* Q)//出队并返回出队元素
{
	struct Vnode* out = Q->node[Q->front];
	Q->front = (Q->front + 1) % Maxsize;
	Q->count--;
	return out;
}
void BFS(struct Adjgraph* A,int start)//广度优先搜索
{
	int visit[8] = { 0 };
	struct Queue* Q = (struct Queue*)malloc(sizeof(struct Queue));
	initial_Queue(Q);//初始化队列
	inque(Q, A->node[start]);//A的第一个顶点入队
	while (Q->count > 0)
	{
		struct Vnode* out = outque(Q);//出队顶点
		printf("%d ", out->data);
		visit[out->data] = 1;//该顶点入队过标记为1
		struct Edgnode* e = out->nextV;
		while (e != NULL)
		{
			if (visit[e->order] == 0)//如果该邻接结点没入队过，则将其入队
			{
				inque(Q, A->node[e->order]);
				visit[e->order] = 1;//该邻接结点入队过标记为1
			}
			e = e->link;//e指向out的下一个邻接结点
		}
	}
}
int main()
{
	struct Adjgraph* adj = (struct Adjgraph*)malloc(sizeof(struct Adjgraph));
	//创建邻接表 
	printf("=======图结构解决外卖配送距离最优========\n\n");
	Init_AdjGraph(adj);
	creat_AdjGraph(adj);
	//展示邻接表
	printf("\n邻接表表示相邻奶茶店(有直接通路)：");
	show_Adj(adj);
	//dijkstra
	printf("\n\n以0号店铺为起点到终点最优配送路径(距离为1000表示不可达)：\n");
	/*int start;
	printf("输入起点店铺序号:");
	scanf("%d", &start);*/
	printf("\n");
	Adj_dijkstra(adj,0);
	//深度优先搜索
	printf("\n\n1、深度优先搜索:");
	DFS(adj, 0);
	//广度优先搜索
	printf("\n\n2、广度优先搜索:");
	BFS(adj, 0);
	printf("\n\n");
}
```

[//]: # (![运行结果]&#40;./runpic/外卖最优截图.png&#41;)


## 3.学生信息管理系统
### 3.1 版本1
```c
//数据准备
struct student
{
	char name[10];//姓名
	int number;//学号
	char major[10];//专业
	char gender[5];//性别
	int age;//年龄
	char phone[20];//联系方式
	int scores;
	struct student* next;
};
struct student* init_L()//初始化数据链表
{
	char name[5][10] = { "张三","李四","王五","小黑","大黄" };
	int number[5] = { 11,23,56,7,9 };
	char gender[5][5] = {"男","男","男","女","女"};
	int age[5] = { 19,18,18,17,19 };
	int scores[5] = { 72,59,88,93,82 };
	char major[5][10] = { "英语","化学","物理","数学","计算机" };
	char phone[5][20] = {"13977865421","14659727623","18976548907","17756342489","16233490878"};
	struct student* head = (struct student*)malloc(sizeof(struct student));//创建一个head结点
	head->next = NULL;//head的下一个结点置为空
	struct student* pre = head;//创建结点pre，标记上一个结点
	for (int i = 0; i < 5; i++)
	{
		struct student* cur= (struct student*)malloc(sizeof(struct student));//创建一个新节点
		//给当前学生结点赋值
		strcpy(cur->name, name[i]);
		strcpy(cur->major, major[i]);
		strcpy(cur->gender, gender[i]);
		strcpy(cur->phone, phone[i]);
		cur->number = number[i];
		cur->age = age[i];
		cur->scores = scores[i];
		cur->next = NULL;//当前结点的下一个结点先设为空
		if (pre != NULL)//上一个结点不为空
		{
			pre->next = cur;//让链表中的上一个结点pre指向当前结点cur
		}
		pre = cur;//当前节点cur作为下一结点的前驱节点pre
	}
	count = 5;//学生总数变为5
	return head;//返回链表头节点
}
//1、显示
void little_show(struct student* head)//展示姓名为s的单行数据
{
	printf("  %3s", head->name);
	printf("%5d", head->number);
	printf("%7s", head->major);
	printf("%5s", head->gender);
	printf("%6d", head->age);
	printf("%14s", head->phone);
	printf("%4d\n", head->scores);
}
void show(struct student* head)//传入链表的头结点,展示数据链表
{
	printf("\n");
	printf("  姓名  学号  专业  性别  年龄   联系方式   成绩\n");
	printf(" ================================================\n");
	while (head!=NULL)
	{
		little_show(head);
		if (head->next == NULL)//下一个结点为空，跳出循环
		{
			break;
		}
		head = head->next;//head指向head的下一个结点
	}
}
//2、添加
int judge_name(char s[])//判断姓名是否合法
{
	for (int i = 0; i < strlen(s); i++)
	{
		if ((s[i] >= 65 && s[i] <= 90) || (s[i] >= 97 && s[i] <= 122))//存在英文字符
		{
			return 0;//返回0，字符串有误
		}
	}
	return 1;//字符串合法
}
int judge_number(struct student* head,int number)//判断学号是否合法
{
	while (head!=NULL)
	{
		if (head->number == number)//遇到重复学号
		{
			return 0;//返回0，不合法
		}
		head = head->next;
	}
	return 1;
}
void add(struct student* head)//添加数据
{
	struct student* new = (struct student*)malloc(sizeof(struct student));
	printf("输入添加的姓名:");
	scanf("%s", &new->name);
	if (judge_name(new->name) == 0)
	{
		printf("不可输入英文姓名!");
		printf("请重新输入：");
		scanf("%s", &new->name);
	}
	printf("输入添加的学号:");
	scanf("%d", &new->number);
	if (judge_number(head,new->number) == 0)
	{
		printf("学号不可重复!");
		printf("请重新输入：");
		scanf("%s", &new->number);
	}
	printf("输入添加的专业:");
	scanf("%s", &new->major);
	printf("输入添加的性别:");
	scanf("%s", &new->gender);
	if (strcmp(new->gender, "男") != 0 && strcmp(new->gender, "女") != 0)//输入的性别非男、女
	{
		printf("性别仅限男女!");
		printf("请重新输入：");
		scanf("%s", &new->gender);
	}
	printf("输入添加的年龄:");
	scanf("%d", &new->age);
	if (new->age < 0 || new->age>100)
	{
		printf("年龄在0-100之间!");
		printf("请重新输入：");
		scanf("%s", &new->age);
	}
	printf("输入添加的联系方式:");
	scanf("%s", &new->phone);
	printf("输入添加的成绩:");
	scanf("%d", &new->scores);
	while (head->next!=NULL)
	{
		head = head->next;
	}
	head->next = new;//将该学生new添加到链表末尾
	new->next = NULL;
	count++;//学生总数+1；
}
//3、删除
void pop(struct student* head)//删除数据
{
	char s[5];
	printf("输入要删除的学生姓名:");
	scanf("%s", s);
	while (head!=NULL)
	{
		if (strcmp(head->next->name, s) == 0)//head的后继节点是要删除的结点
		{
			struct student* p = head->next->next;//p指向head的后继结点的后继节点
			head->next = p;//head的后继节点指向p
			printf("删除成功!");
			count--;//学生总数-1
			break;
		}
		head = head->next;
	}
}
//4、修改
void modify(struct student* head)//修改数据
{
	printf("\n要修改数据的学生姓名:");
	char s[10];
	scanf("%s", &s);
	while (head!=NULL)
	{
		if (strcmp(head->name, s) == 0)//找到要修改的学生
		{
			printf("\n");
			little_show(head);//显示修改行数据
			printf("\n");
			printf("输入修改后的姓名:");
			scanf("%s", &head->name);
			printf("输入修改后的学号:");
			scanf("%d", &head->number);
			printf("输入修改后的专业:");
			scanf("%s", &head->major);
			printf("输入修改后的性别:");
			scanf("%s", &head->gender);
			printf("输入修改后的年龄:");
			scanf("%d", &head->age);
			printf("输入修改后的联系方式:");
			scanf("%s", &head->phone);
			printf("输入修改后的成绩:");
			scanf("%d", &head->scores);
			return;
		}
		head = head->next;
	}
	printf("未找到该学生！");
}
//5、查找
void search(struct student* head)
{
	int way;
	int find = 0;//标记是否找到相关数据
	printf("输入1(按姓名查找)/2(按学号查找):");
	scanf("%d", &way);
	if (way == 1)
	{
		char s[10];
		printf("\n输入要查找的姓名:");
		scanf("%s", &s);
		printf("\n");
		while (head != NULL)
		{
			if (strcmp(head->name, s) == 0)
			{
				find = 1;//找到
				little_show(head);
			}
			head = head->next;
		}
	}
	else
	{
		int number;
		printf("\n输入要查找的学号:");
		scanf("%d", &number);
		printf("\n");
		while (head != NULL)
		{
			if (head->number==number)
			{
				find = 1;
				little_show(head);
				return;
			}
			head = head->next;
		}
	}
	if (find == 0)
	{
		printf("未找到相关数据!");
	}
}
//6、排序
void Bubble_sort(struct student* head)//按成绩从小到大排序
{
	struct student* p = head;
	for (int i = 0; i < count-1; i++)
	{
		head = p;
		while (head->next->next!=NULL)//head的下下个不为空
		{
			if (head->next->scores>head->next->next->scores)//head的下一个比head的下下个分数高
			{
				//交换head的下一个和下下个
				struct student* new = head->next->next->next;//new指向head的下下个的下一个
				struct student* q = head->next;//q指向head原本的下一个
				head->next = head->next->next;//head的下一个指向head原本的下下个
				q->next = new;//head原本的下一个指向new
				head->next->next = q;//head的现在的下下个指向原本的下一个
			}
			head = head->next;
		}
	}
}


int main() {
	int c;
	struct student* stu = init_L();//stu->next指向链表的第一个学生结点
	printf("\t\t学生信息管理系统\n");
	printf("==============================================\n");
	printf("1.显示已录入学生信息:\n");
	printf("2.添加学生信息:\n");
	printf("3.删除学生信息\n");
	printf("4.修改学生信息:\n");
	printf("5.按成绩将学生信息从小到大排序:\n");
	printf("6.查找学生信息\n");
	printf("7.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择:");
		scanf("%d", &c);
		printf("\n");
		if (c == 1)
		{
			show(stu->next);//1、显示
		}
		else if (c == 2)
		{
			add(stu->next);//2、添加
		}
		else if (c == 3)
		{
			pop(stu);//3、删除
		}
		else if (c == 4)
		{
			modify(stu);//4、修改
		}
		else if (c == 5)
		{
			Bubble_sort(stu);//5、排序
			show(stu->next);
		}
		else if (c == 6)
		{
			search(stu);//查找
		}
		printf("\n===============================================\n");
	} while (c > 0 && c < 7);
	return c;
}
```

### 3.2 版本2
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
//数据准备
struct student
{
	char name[10];//姓名
	int ID;//学号
	char zhuanye[10];//专业
	char sex[5];//性别
	int age;//年龄
	char number[20];//联系方式
	int score;//成绩
	struct student* next;//指向链表的下一个学生
};
//1、显示
void show_one(struct student* head)//显示单行数据
{
	printf(" %5s", head->name);
	printf("%5d", head->ID);
	printf("%8s", head->zhuanye);
	printf("%6s", head->sex);
	printf("%7d", head->age);
	printf("%14s", head->number);
	printf("%4d", head->score);
	printf("\n");
}
void show_list(struct student* head)
{
	printf("  姓名   学号   专业   性别   年龄   联系方式   成绩");
	printf("\n ===================================================\n");
	while (head != NULL)
	{
		show_one(head);
		head = head->next;
	}
}
//2、添加
int ID_ok(struct student* head,int ID)//判断学号是否合法
{
	while (head!=NULL)
	{
		if (head->ID == ID)//遇到重复学号
		{
			return 0;//返回0，不合法
		}
		head = head->next;
	}
	return 1;
}
void add_student(struct student* head)//添加数据
{
	struct student* new = (struct student*)malloc(sizeof(struct student));
	new->next = NULL;
	printf("输入要添加的信息:\n");
	printf("姓名:");
	scanf("%s", &new->name);
	for (int i = 0; i < strlen(new->name); i++)//遍历输入的每个字符，若出现英文，不合法，重新输入
	{
		if ((new->name[i] >= 65 && new->name[i] <= 90) || (new->name[i] >= 97 && new->name[i] <= 122))
		{
			printf("姓名必须为汉字!");
			printf("请重新输入：");
			scanf("%s", &new->name);
		}
	}
	printf("学号:");
	scanf("%d", &new->ID);
	if (ID_ok(head,new->ID) == 0)
	{
		printf("学号与已有信息重复!");
		printf("请重新输入：");
		scanf("%d", &new->ID);
	}
	printf("专业:");
	scanf("%s", &new->zhuanye);
	printf("性别:");
	scanf("%s", &new->sex);
	if (strcmp(new->sex, "男") != 0 && strcmp(new->sex, "女") != 0)//输入的性别非男、女
	{
		printf("性别仅限男女!");
		printf("请重新输入：");
		scanf("%s", &new->sex);
	}
	printf("年龄:");
	scanf("%d", &new->age);
	if (new->age < 0 || new->age>100)
	{
		printf("年龄超出范围(0-100)!");
		printf("请重新输入：");
		scanf("%d", &new->age);
	}
	printf("联系方式:");
	scanf("%s", &new->number);
	printf("成绩:");
	scanf("%d", &new->score);
	while (head->next!=NULL)
	{
		head = head->next;
	}
	head->next = new;//将该学生new添加到链表末尾
}
//3、删除
void delete_student(struct student* head)
{
	int ID;
	printf("要删除的学生的学号(ID):");
	scanf("%d", &ID);
	struct student* pre = NULL;//用来保存当前访问节点的前驱结点
	while (head!=NULL)
	{
		if (head->ID==ID)//找到了要删的数据行
		{
			pre->next = head->next;//让该学生的前驱结点指向该学生的后继节点
			printf("删除成功!\n");
			return;
		}
		pre = head;//head作为下一个结点的前驱结点
		head = head->next;
	}
	printf("删除失败,未找到该学生!\n");
}
//4、修改
void revise(struct student* head)
{
	int ID;
	printf("输入要修改的学生的学号(ID):");
	scanf("%d", &ID);
	while (head!=NULL)
	{
		if (head->ID == ID)
		{
			printf("\n");
			show_one(head);
			printf("\n输入修改后的信息:\n");
			printf("姓名:");
			scanf("%s", &head->name);
			printf("学号:");
			scanf("%d", &head->ID);
			printf("专业:");
			scanf("%s", &head->zhuanye);
			printf("性别:");
			scanf("%s", &head->sex);
			printf("年龄:");
			scanf("%d", &head->age);
			printf("联系方式:");
			scanf("%s", &head->number);
			printf("成绩:");
			scanf("%d", &head->score);
			printf("修改成功!\n");
			return;
		}
		head = head->next;
	}
	printf("修改失败，未找到该学生信息!\n");
}
//5、排序
int get_len(struct student* head)//获取链表长度
{
	int len = 0;
	while (head!=NULL)
	{
		len++;
		head = head->next;
	}
	return len;
}
void sort(struct student* head)
{
	struct student* q = head;//保存链表的原始头节点
	int len = get_len(head->next);//len：链表中的学生总数
	struct student* list[100];//创建一个学生数组用于存放所有的学生信息
	head = head->next;//head指向第一个有具体信息的学生结点
	//将学生节点依次添加进list数组
	for (int i = 0; i < len; i++)
	{
		list[i] = head;
		head = head->next;
	}
	//对数组中的信息按照score冒泡排序
	for (int i = 0; i < len - 1; i++)
	{
		for (int j = 0; j < len - i - 1; j++)
		{
			if (list[j]->score > list[j + 1]->score)
			{
				//交换list[j]和list[j+1]
				struct list* p = list[j];
				list[j] = list[j + 1];
				list[j + 1] = p;
			}
		}
	}
	q->next = list[0];//链表的原始头节点指向数组的第一个学生
	for (int i = 0; i < len-1; i++)
	{
		list[i]->next = list[i + 1];//数组中的学生按顺序依次将next域指向数组中的下一个学生
	}
	list[len - 1]->next = NULL;//最后一个学生的next域指向空
	printf("\n");
}
//6、查找
void search_student(struct student* head)
{
	int choose;
	printf("按学号查找输入1、按姓名查找输入2:");
	scanf("%d", &choose);
	if (choose == 1)
	{
		int ID;
		printf("输入查询的(学号)ID:");
		scanf("%d", &ID);
		while (head!=NULL)
		{
			if (head->ID == ID)
			{
				printf("\n");
				show_one(head);
				printf("\n");
				return;
			}
			head = head->next;
		}
	}
	else
	{
		char s[10];
		printf("输入查询的姓名:");
		scanf("%s", &s);
		while (head!=NULL)
		{
			if (strcmp(head->name, s) == 0)
			{
				printf("\n");
				show_one(head);
				printf("\n");
				return;
			}
			head = head->next;
		}
	}
	printf("为查询到相关信息!");
}
int main()
{
	struct student* s = (struct student*)malloc(sizeof(struct student));
	struct student s1 = { "美羊羊",19,"数学","女",20,"11324566789",87 };
	struct student s2 = { "喜羊羊",22,"物理","男",21,"15977432118",99 };
	struct student s3 = { "暖羊羊",15,"新闻","女",20,"11678923544",92 };
	struct student s4 = { "红太狼",66,"体育","女",32,"17789234561",79 };
	struct student s5 = { "灰太狼",99,"语文","男",32,"19965201314",97 };
	s->next = &s1;
	s1.next = &s2;
	s2.next = &s3;
	s3.next = &s4;
	s4.next = &s5;
	s5.next = NULL;
	int choose;
	printf("\t\t学生信息管理系统\n");
	printf("==============================================\n");
	printf("1.显示已添加学生信息:\n");
	printf("2.添加学生信息:\n");
	printf("3.删除学生信息\n");
	printf("4.修改学生信息:\n");
	printf("5.按成绩将学生信息从小到大排序:\n");
	printf("6.查找学生信息\n");
	printf("7.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择:");
		scanf("%d", &choose);
		printf("\n");
		if (choose == 1)
		{
			show_list(s->next);//1、显示
		}
		else if (choose == 2)
		{
			add_student(s);//2、添加
		}
		else if (choose == 3)
		{
			delete_student(s);//3、删除
		}
		else if (choose == 4)
		{
			revise(s);//4、修改
		}
		else if (choose == 5)
		{
			sort(s);//5、排序
			show_list(s->next);
		}
		else if (choose == 6)
		{
			search_student(s);//查找
		}
		printf("\n===============================================\n");
	} while (choose > 0 && choose < 7);
}
```

### 3.3 版本3
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <string.h>
#include <math.h>
struct Student {
	char name[10];
	int number;//学号
	int scorse;//成绩
};
void show(struct Student* S, int n) {
	printf("\n  姓名 学号 成绩\n");
	printf("====================\n");
	for (int i = 0; i < n; i++) {
		printf("%5s", S[i].name);
		printf("%5d", S[i].number);
		printf("%5d\n", S[i].scorse);
	}
}
void manage(struct Student* S,int n) {
	printf("\n成绩录入:\n");
	printf("依次输入每个人的姓名、学号、考试成绩:\n");
	for (int i = 0; i < n; i++) {
		printf("第%d个:", i + 1);
		scanf("%s", &S[i].name);
		scanf("%d", &S[i].number);
		scanf("%d", &S[i].scorse);
	}
}
void sort(struct Student* S,int n) {
	printf("\n成绩排序：\n");
	for (int i = 0; i < n-1; i++) {
		for (int j = 0; j < n-i-1; j++) {
			if (S[i].scorse > S[j].scorse) {
				struct Student stu = S[i];
				S[i] = S[j];
				S[j] = stu;	
			}
		}
	}
	show(S, n);
}
void addcount(struct Student * S,int n) {
	int A = 0;
	int B = 0;
	int C = 0;
	int D = 0;
	int E = 0;
	for (int i = 0; i < n; i++) {
		if (S[i].scorse >= 90) {
			A++;
		}
		else if (S[i].scorse < 90 && S[i].scorse >= 80) {
			B++;
		}
		else if(S[i].scorse < 80 && S[i].scorse >= 70)
		{
			C++;
		}
		else if (S[i].scorse < 70 && S[i].scorse >= 60)
		{
			D++;
		}
		else
		{
			E++;
		}
	}
	printf("\n成绩统计:\n");
	printf("        优秀  良好  中等  及格  不及格\n");
	printf("=======================================\n");
	printf("人数：  %3d%5d%6d%7d%6d\n", A, B, C, D, E);
	printf("百分比：%3.2f%5.2f%6.2f%7.2f%6.2f\n", A / (float)n, B / (float)n, C / (float)n, D / (float)n, E / (float)n);
}
int main() {
	int n;//人数
	printf("输入人数:");
	scanf("%d", &n);
	struct Student* students=(struct Student*)malloc(sizeof(struct Student)*n);
	manage(students,n);
	show(students, n);
	sort(students, n);
	addcount(students, n);
	return 0;
}
```

### 3.4 版本4
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
int count = 0;链表学生总数
数据准备
struct student
{
	char name[10];姓名
	int number;学号
	char major[10];专业
	char gender[5];性别
	int age;年龄
	char phone[20];联系方式
	int scores;
	struct student* next;
};
struct student* init_L()初始化数据链表
{
	char name[5][10] = { "张三","李四","王五","小黑","大黄" };
	int number[5] = { 11,23,56,7,9 };
	char gender[5][5] = {"男","男","男","女","女"};
	int age[5] = { 19,18,18,17,19 };
	int scores[5] = { 72,59,88,93,82 };
	char major[5][10] = { "英语","化学","物理","数学","计算机" };
	char phone[5][20] = {"13977865421","14659727623","18976548907","17756342489","16233490878"};
	struct student* head = (struct student*)malloc(sizeof(struct student));创建一个head结点
	head->next = NULL;head的下一个结点置为空
	struct student* pre = head;创建结点pre，标记上一个结点
	for (int i = 0; i < 5; i++)
	{
		struct student* cur= (struct student*)malloc(sizeof(struct student));创建一个新节点
		给当前学生结点赋值
		strcpy(cur->name, name[i]);
		strcpy(cur->major, major[i]);
		strcpy(cur->gender, gender[i]);
		strcpy(cur->phone, phone[i]);
		cur->number = number[i];
		cur->age = age[i];
		cur->scores = scores[i];
		cur->next = NULL;当前结点的下一个结点先设为空
		if (pre != NULL)上一个结点不为空
		{
			pre->next = cur;让链表中的上一个结点pre指向当前结点cur
		}
		pre = cur;当前节点cur作为下一结点的前驱节点pre
	}
	count = 5;学生总数变为5
	return head;返回链表头节点
}
1、显示
void little_show(struct student* head)展示姓名为s的单行数据
{
	printf("%6s", head->name);
	printf("%5d", head->number);
	printf("%7s", head->major);
	printf("%5s", head->gender);
	printf("%6d", head->age);
	printf("%14s", head->phone);
	printf("%4d\n", head->scores);
}
void show(struct student* head)传入链表的头结点,展示数据链表
{
	printf("\n");
	printf("  姓名  学号  专业  性别  年龄   联系方式   成绩\n");
	printf(" ================================================\n");
	while (head!=NULL)
	{
		little_show(head);
		if (head->next == NULL)下一个结点为空，跳出循环
		{
			break;
		}
		head = head->next;head指向head的下一个结点
	}
}
2、添加
int judge_name(char s[])判断姓名是否合法
{
	for (int i = 0; i < strlen(s); i++)
	{
		if ((s[i] >= 65 && s[i] <= 90) || (s[i] >= 97 && s[i] <= 122))存在英文字符
		{
			return 0;返回0，字符串有误
		}
	}
	return 1;字符串合法
}
int judge_number(struct student* head,int number)判断学号是否合法
{
	while (head!=NULL)
	{
		if (head->number == number)遇到重复学号
		{
			return 0;返回0，不合法
		}
		head = head->next;
	}
	return 1;
}
void add(struct student* head)添加数据
{
	struct student* new = (struct student*)malloc(sizeof(struct student));
	printf("输入添加的姓名:");
	scanf("%s", &new->name);
	if (judge_name(new->name) == 0)
	{
		printf("不可输入英文姓名!");
		printf("请重新输入：");
		scanf("%s", &new->name);
	}
	printf("输入添加的学号:");
	scanf("%d", &new->number);
	if (judge_number(head,new->number) == 0)
	{
		printf("学号不可重复!");
		printf("请重新输入：");
		scanf("%d", &new->number);
	}
	printf("输入添加的专业:");
	scanf("%s", &new->major);
	printf("输入添加的性别:");
	scanf("%s", &new->gender);
	if (strcmp(new->gender, "男") != 0 && strcmp(new->gender, "女") != 0)输入的性别非男、女
	{
		printf("性别仅限男女!");
		printf("请重新输入：");
		scanf("%s", &new->gender);
	}
	printf("输入添加的年龄:");
	scanf("%d", &new->age);
	if (new->age < 0 || new->age>100)
	{
		printf("年龄在0-100之间!");
		printf("请重新输入：");
		scanf("%d", &new->age);
	}
	printf("输入添加的联系方式:");
	scanf("%s", &new->phone);
	printf("输入添加的成绩:");
	scanf("%d", &new->scores);
	while (head->next!=NULL)
	{
		head = head->next;
	}
	head->next = new;将该学生new添加到链表末尾
	new->next = NULL;
	count++;学生总数+1；
}
3、删除
void pop(struct student* head)删除数据
{
	char s[5];
	printf("输入要删除的学生姓名:");
	scanf("%s", s);
	while (head!=NULL)
	{
		if (strcmp(head->next->name, s) == 0)head的后继节点是要删除的结点
		{
			struct student* p = head->next->next;p指向head的后继结点的后继节点
			head->next = p;head的后继节点指向p
			printf("删除成功!");
			count--;学生总数-1
			break;
		}
		head = head->next;
	}
}
4、修改
void modify(struct student* head)修改数据
{
	printf("\n要修改数据的学生姓名:");
	char s[10];
	scanf("%s", &s);
	while (head!=NULL)
	{
		if (strcmp(head->name, s) == 0)找到要修改的学生
		{
			printf("\n");
			little_show(head);显示修改行数据
			printf("\n");
			printf("输入修改后的姓名:");
			scanf("%s", &head->name);
			printf("输入修改后的学号:");
			scanf("%d", &head->number);
			printf("输入修改后的专业:");
			scanf("%s", &head->major);
			printf("输入修改后的性别:");
			scanf("%s", &head->gender);
			printf("输入修改后的年龄:");
			scanf("%d", &head->age);
			printf("输入修改后的联系方式:");
			scanf("%s", &head->phone);
			printf("输入修改后的成绩:");
			scanf("%d", &head->scores);
			return;
		}
		head = head->next;
	}
	printf("未找到该学生！");
}
5、查找
void search(struct student* head)
{
	int way;
	int find = 0;标记是否找到相关数据
	printf("输入1(按姓名查找)/2(按学号查找):");
	scanf("%d", &way);
	if (way == 1)
	{
		char s[10];
		printf("\n输入要查找的姓名:");
		scanf("%s", &s);
		printf("\n");
		while (head != NULL)
		{
			if (strcmp(head->name, s) == 0)
			{
				find = 1;找到
				little_show(head);
			}
			head = head->next;
		}
	}
	else
	{
		int number;
		printf("\n输入要查找的学号:");
		scanf("%d", &number);
		printf("\n");
		while (head != NULL)
		{
			if (head->number==number)
			{
				find = 1;
				little_show(head);
				return;
			}
			head = head->next;
		}
	}
	if (find == 0)
	{
		printf("未找到相关数据!");
	}
}
6、排序
void Bubble_sort(struct student* head)按成绩从小到大排序
{
	struct student* p = head;
	for (int i = 0; i < count-1; i++)
	{
		head = p;
		while (head->next->next!=NULL)head的下下个不为空
		{
			if (head->next->scores>head->next->next->scores)head的下一个比head的下下个分数高
			{
				交换head的下一个和下下个
				struct student* new = head->next->next->next;new指向head的下下个的下一个
				struct student* q = head->next;q指向head原本的下一个
				head->next = head->next->next;head的下一个指向head原本的下下个
				q->next = new;head原本的下一个指向new
				head->next->next = q;head的现在的下下个指向原本的下一个
			}
			head = head->next;
		}
	}
}


int main() {
	int c;
	struct student* stu = init_L();stu->next指向链表的第一个学生结点
	printf("\t\t学生信息管理系统\n");
	printf("==============================================\n");
	printf("1.显示已录入学生信息:\n");
	printf("2.添加学生信息:\n");
	printf("3.删除学生信息\n");
	printf("4.修改学生信息:\n");
	printf("5.按成绩将学生信息从小到大排序:\n");
	printf("6.查找学生信息\n");
	printf("7.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择:");
		scanf("%d", &c);
		printf("\n");
		if (c == 1)
		{
			show(stu->next);1、显示
		}
		else if (c == 2)
		{
			add(stu->next);2、添加
		}
		else if (c == 3)
		{
			pop(stu);3、删除
		}
		else if (c == 4)
		{
			modify(stu);4、修改
		}
		else if (c == 5)
		{
			Bubble_sort(stu);5、排序
			show(stu->next);
		}
		else if (c == 6)
		{
			search(stu);查找
		}
		printf("\n===============================================\n");
	} while (c > 0 && c < 7);
	return c;
}
```






## 4.宿舍管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
//数据准备
struct student
{
	char name[10];//姓名
	int ID;//学号
	int room;//房号
};
struct list
{
	struct student* data[20];//存储所有学生数据
	int count;//表中已有数据条数
};
//把链表中的每条学生数据写入文件
void write(struct list* L)
{
	FILE* fp;
	// 打开文件，没有文件自动创建
	fp = fopen("a.txt", "w+"); // b:表示以二进制写入
	if (fp == NULL)
	{
		printf("未找到该文件!");
		exit(0);
	}
	char list[5][50];
	for (int i = 0; i < L->count; i++)
	{
		char s[50] = "";
		strcpy(s, L->data[i]->name);//将姓名赋值给s
		strcat(s, "   ");
		char ID[10] = "";
		itoa(L->data[i]->ID, ID, 10);
		strcat(s, ID);//将ID的字符串形式添加进s
		strcat(s, "   ");
		char room[10] = "";
		itoa(L->data[i]->room, room, 10);
		strcat(s, room);//将room的字符串形式添加进s
		strcat(s, "\n");//在s末尾添加换行符
		fwrite(s, strlen(s), 1, fp);//将s写入文档
	}
	fclose(fp);
}
void init_L(struct list* L)//初始化数据
{
	char name[5][10] = { "孙悟空","猪八戒","唐三藏","白龙马","沙僧"};
	int ID[5] = { 100,67,99,72,80 };
	int room[5] = { 301,117,219,104,107 };
	L->count = 5;
	for (int i = 0; i < 5; i++)
	{
		struct student* new = (struct student*)malloc(sizeof(struct student));//创建一个新的学生
		strcpy(new->name, name[i]);
		new->ID = ID[i];
		new->room = room[i];
		L->data[i] = new;//链表第i个学生数据指向new
	}
	write(L);//将数据写入文件
}
//1、显示
void show_one(struct student* a)
{
	printf(" %6s", a->name);
	printf("%5d", a->ID);
	printf("%7d\n", a->room);
}
void show(struct list* L)//显示数据
{
	printf("  姓名   学号   房号\n");
	printf(" ====================\n");
	for (int i = 0; i < L->count; i++)
	{
		printf(" %6s", L->data[i]->name);
		printf("%5d", L->data[i]->ID);
		printf("%7d\n", L->data[i]->room);
	}
}
//2、添加
void add(struct list* L)//添加数据
{
	struct student* new = (struct student*)malloc(sizeof(struct student));
	printf("输入添加学生的姓名:");
	scanf("%s", &new->name);
	printf("输入添加学生的ID(学号):");
	scanf("%d", &new->ID);
	printf("输入添加城市的房号:");
	scanf("%d", &new->room);
	L->data[L->count] = new;//L的最后一条inform变为new
	L->count++;
}
//3、删除
void pop(struct list* L)//删除数据
{
	char s[10];
	printf("输入要删除学生的姓名:");
	scanf("%s", s);
	for (int i = 0; i < L->count; i++)
	{
		if (strcmp(L->data[i]->name, s) == 0)//找到要删除的inform行
		{
			L->count--;//表L数据总数-1
			for (int j = i; j < L->count; j++)
			{
				L->data[j] = L->data[j + 1];//i之后所有数据前移
			}
			break;
		}
	}
	printf("删除成功!\n");
}
//4、修改
void modify(struct list* L)//修改数据
{
	printf("\n要修学生的姓名:");
	char s[10];
	scanf("%s", &s);
	for (int i = 0; i < L->count; i++)
	{
		if (strcmp(L->data[i]->name, s) == 0)//找到要修改的inform
		{
			printf("修改后的学生姓名:");
			scanf("%s", &L->data[i]->name);
			printf("修改后的学生ID(学号):");
			scanf("%d", &L->data[i]->ID);
			printf("修改后的学生房号:");
			scanf("%d", &L->data[i]->room);
			return;
		}
	}
	printf("未找到该学生！\n");
}
//5、排序
//5.1快速排序(way=1:按姓名,way=2:按学号,way=3:按房号)
void quick_sort(struct list* L, int low, int high,int way)
{
	int i = low;
	int j = high;
	struct student* tmp = L->data[low];//第一个元素任命为房价序列的中间分界线，左边比他小，右边比他大
	if (i > j)  //如果下标i大于下标j，函数结束运行
	{
		return;
	}
	while (i != j)
	{
		while (j > i && ((way==1&&strcmp(L->data[j]->name,tmp->name)>=0)||(way==2&&L->data[j]->ID >= tmp->ID)||(way==3&&L->data[j]->room>=tmp->room)))//j行数据>分界数据
		{
			j--;
		}
		while (j > i && ((way == 1 && strcmp(L->data[i]->name, tmp->name) <= 0) || (way == 2 && L->data[i]->ID <= tmp->ID) || (way == 3 && L->data[i]->room <= tmp->room)))//j行数据<分界数据
		{
			i++;
		}
		if (j > i)
		{
			//交换数据列
			struct student* new = L->data[j];
			L->data[j] = L->data[i];
			L->data[i] = new;
		}
	}
	L->data[low] = L->data[i];
	L->data[i] = tmp;
	quick_sort(L, low, i - 1,way);
	quick_sort(L, i + 1, high,way);
}
//5.2归并排序(way=1:按姓名,way=2:按学号,way=3:按房号)
int merge(struct list* r, struct list* s, int left, int mid, int right,int way)//r为原数组，将r分为左右两半左右两半分别是已经排好序的数组，将左右两半分别排序，s为排序后的数组
{
	int i, j, k;
	i = left;//左边数组的数的索引
	j = mid + 1;//右边数组的数的索引
	k = left;//s中要添加的数的索引
	while ((i <= mid) && (j <= right))//循环条件左右两边的数均未完全参与排序
		if ((way==1&&strcmp(r->data[i]->name,r->data[j]->name)<0)||(way==2&&r->data[i]->ID <= r->data[j]->ID)||(way==3 && r->data[i]->room <=r->data[j]->room))//左边数组的当前访问数小于右边数组的当前访问数
		{
			s->data[k] = r->data[i];//r[i]进s
			i++;
			k++;//i、k后移
		}
		else
		{
			s->data[k] = r->data[j];//r[j]进s
			j++;
			k++;//j、k后移
		}
	while (i <= mid)//右边全参与排序，左边数组还有未参与排序的，全部添加进s
		s->data[k++] = r->data[i++];
	while (j <= right)//左边全参与排序，右边数组还未参与排序的，全部添加进s
		s->data[k++] = r->data[j++];
	return 0;
}
int merge_sort(struct list* r, struct list* s, int left, int right,int way)//way表示排序方式(1:姓名/2:学号/3:房号)
{
	int mid;
	struct list* t[20];//排序后的数组
	if (left == right)
	{
		s->data[left] = r->data[right];
	}
	else
	{
		mid = (left + right) / 2;//划分数组的中间索引号
		//对数组左右两边分别递归
		merge_sort(r, t, left, mid,way);
		merge_sort(r, t, mid + 1, right,way);
		//将得到的有序的左右两边合并成一个有序数组
		merge(t, s, left, mid, right,way);
	}
	return 0;
}
//5.3堆排序(way=1:按姓名,way=2:按学号,way=3:按房号)
void swap(struct list* a, int i, int j)//交换数组a中的第i个和第j个结点
{
	struct list* t = a->data[i];
	a->data[i] = a->data[j];
	a->data[j] = t;
}
int get_item(struct student* a,int way)//way=2 or way=3获取整数值
{
	if (way == 2)
	{
		return a->ID;//way=1返回a的ID
	}
	else if (way == 3)
	{
		return a->room;//way=1返回a的房号
	}
}
//way=2 or way=3
int get_max(struct list* a, int i, int j, int las,int way)//返回左右儿子中的较大值,如果有一个的序号超过可更改的最大序号las，选另一个，如果两个都超过则返回一个很小的值
{
	if (i >= las)//i结点左右儿子均超过可更改的最大序号las
	{
		return -1000;//返回一个很小的值
	}
	else if (j >= las)//i结点右儿子超过可更改的最大序号las
	{
		return get_item(a->data[i],way);//返回左儿子的值
	}
	else
	{
		return get_item(a->data[i], way) > get_item(a->data[j], way) ? get_item(a->data[i], way) : get_item(a->data[j], way);//返回左右儿子中较大的
	}
}
void creat_heap(struct list* a, int i, int las,int way)
{
	int father = get_item(a->data[i],way);//父节点的值
	int max = get_max(a, 2 * i + 1, 2 * i + 2, las,way);//max：父亲的两个儿子的较大值
	while (father < max)//父亲小于其中一个儿子
	{
		if (max == get_item(a->data[2 * i + 1],way))//max=左儿子
		{
			swap(a, i, 2 * i + 1);//交换父亲和左儿子
			i = 2 * i + 1;//父亲结点的序号变成原本左儿子的序号，继续判断左儿子的子树的变化
		}
		else if (max == get_item(a->data[2 * i + 2],way))//max=右儿子
		{
			swap(a, i, 2 * i + 2);//交换父亲和右儿子
			i = 2 * i + 2;//父亲结点的序号变成原本左儿子的序号，判断右儿子的子树的变化
		}
		if (i >= (las - 1) / 2)//第i个结点已排好序不可再更改
		{
			break;
		}
		max = get_max(a, 2 * i + 1, 2 * i + 2, las,way);//max：父亲的两个儿子的较大值
	}
}
void heap_sort(struct list* a, int len,int way)
{
	int i = len / 2 - 1;//最后一个非叶结点的序号
	int count = 0;
	//构建大顶堆
	while (i >= 0)
	{
		creat_heap(a, i, len,way);
		i--;
	}
	//交换首结点和未排序的末尾结点
	int j = len - 1;
	while (j > 0)
	{
		swap(a, 0, j);//交换a的第一个和第j个,此后第j个结点已经完成排序不可再被交换
		creat_heap(a, 0, j,way);//调整根节点的树
		j--;
	}
}
//way = 1;
char* get_max_name(struct list* a, int i, int j, int las)//返回左右儿子中的较大值,如果有一个的序号超过可更改的最大序号las，选另一个，如果两个都超过则返回一个很小的值
{
	if (i >= las)//i结点左右儿子均超过可更改的最大序号las
	{
		return "";//返回一个很小的值(空字符)
	}
	else if (j >= las)//i结点右儿子超过可更改的最大序号las
	{
		return a->data[i]->name;//返回左儿子的值
	}
	else
	{
		//返回左右儿子中较大的
		if (strcmp(a->data[i]->name, a->data[j]->name) > 0)
		{
			return a->data[i]->name;
		}
		else
		{
			return a->data[j]->name;
		}
	}
}
void creat_heap_name(struct list* a, int i, int las)
{
	char father[10];
	strcpy(father, a->data[i]->name);//父节点的值
	char max[10];
	strcpy(max,get_max_name(a, 2 * i + 1, 2 * i + 2, las));//max：父亲的两个儿子的较大值
	while (strcmp(father,max)<0)//父亲小于其中一个儿子
	{
		if (strcmp(max,a->data[2 * i + 1]->name)==0)//max=左儿子
		{
			swap(a, i, 2 * i + 1);//交换父亲和左儿子
			i = 2 * i + 1;//父亲结点的序号变成原本左儿子的序号，继续判断左儿子的子树的变化
		}
		else if (strcmp(max,a->data[2 * i + 2]->name)==0)//max=右儿子
		{
			swap(a, i, 2 * i + 2);//交换父亲和右儿子
			i = 2 * i + 2;//父亲结点的序号变成原本左儿子的序号，判断右儿子的子树的变化
		}
		if (i >= (las - 1) / 2)//第i个结点已排好序不可再更改
		{
			break;
		}
		strcpy(max,get_max_name(a, 2 * i + 1, 2 * i + 2, las));//max：父亲的两个儿子的较大值
	}
}
void heap_sort_name(struct list* a, int len)//way=3
{
	int i = len / 2 - 1;//最后一个非叶结点的序号
	int count = 0;
	//构建大顶堆
	while (i >= 0)
	{
		creat_heap_name(a, i, len);
		i--;
	}
	//交换首结点和未排序的末尾结点
	int j = len - 1;
	while (j > 0)
	{
		swap(a, 0, j);//交换a的第一个和第j个,此后第j个结点已经完成排序不可再被交换
		creat_heap_name(a, 0, j);//调整根节点的树
		j--;
	}
}
//6、查询
//6.1折半查找
void half_search(struct list* L)//对半查找L中的数据
{
	while (1)
	{
		int flag = 0;//标记是否查找到
		printf("\n");
		int way;
		printf("输入1(按姓名)、2(按学号(ID)、3(按房号)查找:");
		scanf("%d", &way);
		printf("\n");
		//先按输入的查询类型排序
		int x; char y[10];
		if (way == 1)
		{
			quick_sort(L, 0, L->count - 1, 1);
			printf("输入要查找的姓名:");
			scanf("%s", &y);
		}
		else if (way == 2)
		{
			quick_sort(L, 0, L->count - 1, 2);
			printf("输入要查找的学号:");
			scanf("%d", &x);
		}
		else
		{
			quick_sort(L, 0, L->count - 1, 3);
			printf("输入要查找的房号:");
			scanf("%d", &x);
		}
		int left = 0; int right = L->count-1;
		while (left <= right)
		{
			if (((left + right) / 2)<L->count&&((way == 1 && strcmp(L->data[(left + right) / 2]->name, y) == 0) || (way == 2 && L->data[(left + right) / 2]->ID == x) || (way == 3 && L->data[(left + right) / 2]->room == x)))
			{
				printf("  %s  ", L->data[(left + right) / 2]->name);
				printf("%3d", L->data[(left + right) / 2]->ID);
				printf("%6d\n", L->data[(left + right) / 2]->room);
				flag = 1;
				break;
			}
			else if ((way == 1 && strcmp(L->data[(left + right) / 2]->name, y) > 0) || (way == 2 && L->data[(left + right) / 2]->ID > x) || (way == 3 && L->data[(left + right) / 2]->room > x))//x在查找区间的左边
			{
				right = (left + right) / 2 - 1;
			}
			else if ((way == 1 && strcmp(L->data[(left + right) / 2]->name, y) < 0) || (way == 2 && L->data[(left + right) / 2]->ID < x) || (way == 3 && L->data[(left + right) / 2]->room < x))//x在查找区间的右边
			{
				left = (left + right) / 2 + 1;
			}
		}
		if (flag == 0)
		{
			printf("未查找到相关信息！\n");
		}
		printf("是否继续查找?yes/no:");
		char command[5];
		scanf("%s", &command);
		if (strcmp(command,"no")==0)
		{
			break;
		}
	}
}
//6.2二叉查找
//6.2二叉查找
int flag;//标记是否查询到
struct node//定义结点类型node
{
	struct student* data;//结点值
	struct node* left;
	struct node* right;
};
void init_node(struct node* root, struct student* item)//初始化结点
{
	root->left = NULL;
	root->right = NULL;
	root->data = item;
}
void creat_binary(struct node* root, struct student* x,int way)//构建/在二叉查找树中插入元素x
{
	if ((way==1&&strcmp(x->name,root->data->name)<0)||(way==2&&x->ID < root->data->ID)||(way==3&&x->room<root->data->room))//x人口小于root节点人口
	{
		if (root->left == NULL)//root节点无左儿子
		{
			struct node* new_left = (struct node*)malloc(sizeof(struct node));
			init_node(new_left, x);
			root->left = new_left;//让root结点的左儿子指向以x为data值构成的结点
		}
		else//root结点有左儿子
		{
			creat_binary(root->left, x,way);//在root左子树中寻找正确位置插入x
		}
	}
	else if ((way == 1 && strcmp(x->name, root->data->name) > 0) || (way == 2 && x->ID > root->data->ID) || (way == 3 && x->room > root->data->room))//x人口>root节点人口
	{
		if (root->right == NULL)//root节点无右儿子
		{
			struct node* new_right = (struct node*)malloc(sizeof(struct node));
			init_node(new_right, x);
			root->right = new_right;//让root结点的右儿子指向以x为data值构成的结点
		}
		else
		{
			creat_binary(root->right, x,way);//在root右子树中寻找正确位置插入x
		}
	}
}
//way=2 or way=3
void equal(struct node* root, int x, int way)//查找二叉树中房价等于x的城市数据
{
	if (root == NULL)
	{
		return;
	}
	if (((way==2)&&x==root->data->ID)||(way==3&&x==root->data->room))
	{
		flag = 1;
		printf("\n");
		printf("  %s  ", root->data->name);
		printf("%3d", root->data->ID);
		printf("%6d\n", root->data->room);
		return;
	}
	equal(root->left, x,way);
	equal(root->right, x,way);
}
//way=1
void equal_name(struct node* root, char x[])//查找二叉树中房价等于x的城市数据
{
	if (root == NULL)
	{
		return;
	}
	if (strcmp(x,root->data->name)==0)
	{
		flag = 1;
		printf("\n");
		printf("  %s  ", root->data->name);
		printf("%3d", root->data->ID);
		printf("%6d\n", root->data->room);
		return;
	}
	equal_name(root->left, x);
	equal_name(root->right, x);
}
void tree_search(struct list* L)
{
	while (1)
	{
		flag = 0;//标记是否查询到
		int way;
		printf("输入1(按姓名)、2(按学号(ID)、3(按房号)查找:");
		scanf("%d", &way);
		//构建按输入查询方式的大小查找二叉树
		struct node* root = (struct node*)malloc(sizeof(struct node));
		init_node(root, L->data[0]);
		for (int i = 1; i < L->count; i++)
		{
			creat_binary(root, L->data[i], way);//把各个学生结点依次按二叉排序树的规则插入到树中
		}
		//查询
		int x; char y[10];
		if (way == 1)
		{
			printf("输入查询的姓名:");
			scanf("%s", &y);
			equal_name(root, y);
		}
		else if (way == 2)
		{
			printf("输入查询的学号:");
			scanf("%d", &x);
			equal(root, x, way);
		}
		else
		{
			printf("输入查询的房号:");
			scanf("%d", &x);
			equal(root, x, way);
		}
		if (flag == 0)
		{
			printf("未查询到相关信息!");
		}
		printf("是否继续查找?yes/no:");
		char command[5];
		scanf("%s", &command);
		if (strcmp(command, "no") == 0)
		{
			break;
		}
	}
}
//6.3hash查找
#define N 20//表长
//自定义哈希函数
int hash(struct student* value,int way)
{
	if (way == 1)
	{
		return strlen(value->name) % 7;
	}
	if (way == 2)
	{
		return value->ID % 7;
	}
	else
	{
		return value->room % 7;
	}
}
//创建哈希表
void creatHash(struct list* arr, struct student* hashArr[N], int times[N],int way)
{
	int index;
	//将序列中每个元素存储到哈希表
	for (int i = 0; i < arr->count; i++)
	{
		int time = 1;
		index = hash(arr->data[i],way);//index表示arr[i]在哈希表中的索引位置
		while (hashArr[index % N] != NULL) //如果index位置已经有元素放置
		{
			index = (index + (int)pow(time, time)) % 10;//继续往后查找
			time++;//查找次数+1
		}
		hashArr[index] = arr->data[i];
		times[index] = time;//arr[i]的最终查找次数
	}
}
void hash_search(struct list* L)
{
	while (1)
	{
		flag = 0;
		int way;
		printf("输入1(按姓名)、2(按学号(ID)、3(按房号)查找:");
		scanf("%d", &way);
		struct student* hashArr[N];
		for (int i = 0; i < N; i++)
		{
			hashArr[i] = NULL;
		}
		int times[N] = { 0 };
		creatHash(L, hashArr, times, way);//构造hash表
		int x; char s[10];
		if (way == 1)
		{
			printf("姓名:");
			scanf("%s", &s);
		}
		else if(way==2)
		{
			printf("学号:");
			scanf("%d", &x);
		}
		else
		{
			printf("房号:");
			scanf("%d", &x);
		}
		for (int i = 0; i < N; i++)
		{
			if (hashArr[i]!=NULL&&((way == 1 && strcmp(s, hashArr[i]->name) == 0) || (way == 2 && hashArr[i]->ID == x) || (way == 3 && hashArr[i]->room == x)))
			{
				flag = 1;
				printf("\n");
				printf("  %s  ", hashArr[i]->name);
				printf("%3d", hashArr[i]->ID);
				printf("%6d", hashArr[i]->room);
				printf("\n  哈希表中的查询次数:%d\n", times[i]);
				break;
			}
		}
		if (flag == 0)
		{
			printf("未查询到相关信息！");
		}
		printf("\n是否继续查询?yes/no:");
		char command[10];
		scanf("%s", &command);
		if (strcmp(command, "no") == 0)
		{
			break;
		}
	}
}

int main() {
	int c;
	struct list* L = (struct list*)malloc(sizeof(struct list));
	init_L(L);
	printf("\t\t学生信息管理系统\n");
	printf("==============================================\n");
	printf("\t\t1.显示已录入学生信息:\n");
	printf("\t\t2.添加学生信息:\n");
	printf("\t\t3.删除学生信息\n");
	printf("\t\t4.修改学生信息:\n");
	printf("\t\t5.快速排序:\n");
	printf("\t\t6.归并排序\n");
	printf("\t\t7.堆排序:\n");
	printf("\t\t8.折半查找\n");
	printf("\t\t9.二叉树查找:\n");
	printf("\t\t10.哈希查找\n");
	printf("\t\t11.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择:");
		scanf("%d", &c);
		printf("\n");
		if (c == 1)
		{
			show(L);//1、显示
		}
		else if (c == 2)
		{
			add(L);//2、添加
		}
		else if (c == 3)
		{
			pop(L);//3、删除
		}
		else if (c == 4)
		{
			modify(L);//4、修改
		}
		else if (c == 5)
		{
			int way;
			printf("输入排序方式1(按姓名)、2(按学号(ID)、3(按房号):");
			scanf("%d", &way);
			quick_sort(L, 0, L->count - 1, way);//5、快速排序
			show(L);
		}
		else if (c == 6)
		{
			int way;
			printf("输入排序方式1(按姓名)、2(按学号(ID)、3(按房号):");
			scanf("%d", &way);//6、归并排序
			merge_sort(L, L, 0, L->count - 1, way);
			show(L);
		}
		else if (c == 7)
		{
			int way;
			printf("输入排序方式1(按姓名)、2(按学号(ID)、3(按房号):");
			scanf("%d", &way);//6、堆排序
			if (way == 1)
			{
				heap_sort_name(L, L->count);
				show(L);
			}
			else
			{
				heap_sort(L, L->count, way);
				show(L);
			}
		}
		else if (c == 8)//折半查找
		{
			half_search(L);
		}
		else if(c==9)//二叉查找
		{
			tree_search(L);
		}
		else if(c==10)
		{
			half_search(L);
		}
		write(L);//每次完成操作都将新的数据写入文件
		printf("\n===============================================\n");
	} while (c > 0 && c < 11);
	return c;
}
```

### 5.航班管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
struct flight {
	int number;//航班号
	char start[10];//出发地
	char end[10];//目的地
	int all;//总量
	int rest;//余量
};
struct fly {
	struct flight* data[50];
	int count;//航班数量
};
//1、航班信息输入
void input_fly(struct fly* F) {
	printf("输入航班总数:");
	int n;
	scanf("%d", &n);
	F->count = n;//F中的航班书=数量设为n
	printf("输入航班的:航班号、出发地、目的地、总数、(空格隔开)\n");
	for (int i = 0; i < n; i++) {
		printf("%d:", i + 1);
		struct flight* newfly = (struct fligth*)malloc(sizeof(struct flight));
		scanf("%d", &newfly->number);//newfly的航班号
		scanf("%s", &newfly->start);//newfly的出发地
		scanf("%s",&newfly->end);//newfly的目的地
		scanf("%d", &newfly->all);//newfly的总数
		newfly->rest = newfly->all;//初始余量和总量一样
		F->data[i] = newfly;//F的第i个数据指向newfly
	}
}
//2、航班信息输出
void output(struct fly* F) {
	printf("航班号\t 出发地\t  目的地   机票总量  机票余量\n");
	printf("==============================================\n");
	for (int i = 0; i < F->count; i++) {
		printf("%5d", F->data[i]->number);
		printf("%9s", F->data[i]->start);
		printf("%10s", F->data[i]->end);
		printf("%8d", F->data[i]->all);
		printf("%10d\n", F->data[i]->rest);
	}
}
//3、添加航班
void append(struct fly* F) {
	struct flight* newfly = (struct flight*)malloc(sizeof(struct flight));
	printf("输入添加的航班信息:\n");
	printf("航班号:");
	scanf("%d", &newfly->number);
	printf("出发地:");
	scanf("%s", &newfly->start);
	printf("目的地:");
	scanf("%s", &newfly->end);
	printf("机票总量:");
	scanf("%d", &newfly->all);
	newfly->rest = newfly->all;
	F->data[F->count] = newfly;//F的第F->count个数据指向newfly
	F->count++;//F中的航班信息数+1
}
//4、删除及售出
void del(struct fly* F) {
	printf("输入要删除的航班号:");
	int n;
	scanf("%d", &n);
	for (int i = 0; i < F->count; i++) {
		if (F->data[i]->number == n) {//找到了航班号
			//后面的数据前移覆盖该数据
			for (int j = i; j < F->count - 1; j++) {
				F->data[j] = F->data[j + 1];
			}
			F->count--;//F的航班数量-1
			printf("删除成功!");
			return;//删除成功退出函数
		}
	}
	printf("未找到相关航班！");
}
void sell(struct fly* F) {
	printf("输入要出售机票的航班号:");
	int n;
	scanf("%d", &n);
	for (int i = 0; i < F->count; i++) {
		if (F->data[i]->number == n) {//找到了航班号
			if (F->data[i]->rest == 0) {
				printf("该航班的机票已售罄！");
			}
			else
			{
				F->data[i]->rest--;//该航班的机票余量-1
				printf("出票成功!");
			}
			return;//退出系统
		}
	}
	printf("未找到相关航班！");
}
void delete_sell(struct fly* F) {
	printf("选择1(删除)/2(或售出):");
	int choose;
	scanf("%d", &choose);
	if (choose == 1) {
		del(F);//调用删除航班的函数
	}
	else
	{
		sell(F);//调用售出航班的函数
	}
}
//5、查询
void find(struct fly* F) {
	printf("输入查询方式1(航班号)/2(出发地)/3(目的地):");
	int c;
	scanf("%d", &c);
	char s[10]; int n; char s2[10];
	if (c == 1) {
		printf("航班号:");
		scanf("%d", &n);
	}
	else if (c == 2) {
		printf("出发地:");
		scanf("%s", &s);
	}
	else{
		printf("目的地:");
		scanf("%s", &s2);
	}
	int f = 0;
	for (int j = 0; j < F->count; j++) {
		int flag = 0;
		if (c == 1 && F->data[j]->number == n ) {//按航班号
			flag = 1;
		}
		else if (c == 2 && strcmp(F->data[j]->start, s) == 0) {//按出发地
			flag = 1;
		}
		else if (c == 3 && strcmp(F->data[j]->end , s2)==0) {//按目的地
			flag = 1;
		}
		if (flag == 1) {//找到航班信息
			f = 1;
			printf("航班号\t 出发地\t  目的地   机票总量  机票余量\n");
			printf("==============================================\n");
			printf("%5d", F->data[j]->number);
			printf("%9s", F->data[j]->start);
			printf("%10s", F->data[j]->end);
			printf("%8d", F->data[j]->all);
			printf("%10d\n", F->data[j]->rest);
		}
	}
	if (f == 0) {
		printf("未找到相关信息！");
	}
}
//6、保存文件
void write(struct fly* F){//把链表中的每条教室数据写入文件
	FILE* fp;
	// 打开文件，没有文件自动创建
	fp = fopen("航班.txt", "w+"); // b:表示以二进制写入
	if (fp == NULL)
	{
		printf("未找到该文件!");
		exit(0);
	}
	char list[30][50];//创建30个长度为50的字符数组，每个数组存放一个航班的相关信息
	for (int i = 0; i < F->count; i++)
	{
		char s[50] = "";//用于存放每个航班的"number、start、end、all"组成的字符串
		char number[10] = "";//航班号的字符串格式
		itoa(F->data[i]->number, number, 10);
		char all[10] = "";//航班总数的字符串格式
		itoa(F->data[i]->all, all, 10);

		strcpy(s, number);//将名称赋值给s
		strcat(s, "   ");//在s后面拼接几个空格
		strcat(s, F->data[i]->start);//将出发地添加进s
		strcat(s, "   ");//在s后面拼接几个空格
		strcat(s, F->data[i]->end);//将出发地添加进s
		strcat(s, "   ");//在s后面拼接几个空格
		strcat(s, all);//将总数添加进s

		strcat(s, "\n");//在s末尾添加换行符
		fwrite(s, strlen(s), 1, fp);//将s写入文档
	}
	fclose(fp);
}
//6.保存航班信息在文件航班.txt中
void save(struct fly* F) {
	write(F);
	printf("保存成功！");
}
int main()
{
	int c;
	struct fly* F = (struct fly*)malloc(sizeof(struct fly));
	F->count = 0;//初始化航班为0
	printf("\t\t航班管理系统\n");
	printf("==============================================\n");
	printf("\t\t1.航班信息输入\n");
	printf("\t\t2.航班信息输出\n");
	printf("\t\t3.添加航班\n");
	printf("\t\t4.删除及售出\n");
	printf("\t\t5.查询(按航班号，出发地，目的地)\n");
	printf("\t\t6.保存航班信息在文件中:\n");
	printf("\t\t7.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择功能:");
		scanf("%d", &c);
		printf("\n");
		if (c == 1)
		{
			input_fly(F);//输入
		}
		else if (c == 2)
		{
			output(F);//显示
		}
		else if (c == 3)
		{
			append(F);//添加
		}
		else if (c == 4)
		{
			delete_sell(F);//删除
		}
		else if (c == 5)
		{
			find(F);//查找
		}
		else if(c==6)
		{
			save(F);//查找
		}
		printf("\n===============================================\n");
	} while (c > 0 && c < 7);
}
```

## 5.课程信息管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
#include <malloc.h>
#define N 10
#define class_n 10  //课程数目
#define teacher_n 9 //教师数目
#define student_n 11//学生数目
struct class_inform {
	int number;//课程编号
	char name[10];//课程名称
	int hourse;//学时
	int point;//学分
	char ddl[10];//报名截止时间
	int class_num;//所开班数 
	int max_peo;//各班人数上限
	int stu_num;//已报名学生数目
	char teacher_name[10];//最终授课老师姓名
};
struct teacher_inform {
	char name[10];//教师姓名
	char gender[5];//教师性别
	int age;//教师年龄
	char** available;//可以教授的课程
	int ava_num;//可选课程数目
	struct teach_class* hope;//想教授的课程
	int hope_num;//想选课程数目
	char** already;//已选课程
	int already_num;//已选课程数目
};
struct student_inform {
	char name[10];//学生姓名
	struct student_hope* hope;//想选课程
	int hope_num;//想选的课程数目
};
struct student_hope {
	char name[10];//想选课程名称
	char teacher_name[2][10];//选择的教师名称
};
struct teach_class {
	char class_name[10];//想教授的课程
	int scores;//该教授的该门课程得票
};


struct class_inform course[class_n];//课程基本信息数组
struct teacher_inform teacher[teacher_n];//教师基本信息数组
struct student_inform student[student_n];//学生选课信息数组




/*
* teacher_inform-教师基本信息：
* init_teacher(course):初始化教师数组course中所有科目的相关信息
* showAll_teacher(course):展示course学科数组中所有科目的相关信息
* showAppoint_teacher(course，num):展示学科数组course中标号为num的科目的相关信息
*/
//初始化教师信息(√)
void init_teacher(struct teacher_inform* teacher) {
	char name[10][10] = { "赵亿","钱尔","孙山","李思","周五","吴柳","郑齐","王霸","冯酒","陈诗" };
	char gender[10][5] = { "女","男","男","女","男","女","男","男","男","女" };
	int age[10] = { 24,28,25,40,46,43,37,26,34,33 };
	for (int i = 0; i < teacher_n; i++) {
		strcpy(teacher[i].name, name[i]);
		strcpy(teacher[i].gender, gender[i]);
		teacher[i].age = i * 2 + 7;
	}
}
void showAppoint_teacher(struct teacher_inform* teacher, char name[]) {
	for (int i = 0; i < teacher_n; i++) {
		if (strcmp(teacher[i].name, name) == 0) {
			printf("%5s", teacher[i].name);
			printf("%5s", teacher[i].gender);
			printf("%5d\t", teacher[i].age);
			break;
		}
	}

}
//展示所有教师的基本信息(√)
void showAll_teacher(struct teacher_inform* teacher) {
	printf("教师姓名\t性别\t年龄\n");
	for (int i = 0; i < teacher_n; i++) {
		printf("%5s", teacher[i].name);
		printf("%13s", teacher[i].gender);
		printf("%10d\n", teacher[i].age);
	}
}
////设置指定教师可以教授的课程
//void set_teachclass(teacher_inform* teacher, char name[]) {
//	for (int i = 0; i < teacher_n; i++) {
//		if (strcmp(teacher[i].name, name) == 0) {
//			printf("输入可教授课程数目:");
//			int num;
//			scanf("%d", &num);
//			teacher[i].available = (char**)malloc(sizeof(char*) * num);
//			for (int j = 0; j < num; j++) {
//				printf("课程%d:", j + 1);
//				teacher[i].available[j] = (char*)malloc(sizeof(char) * 10);
//				char s[10];
//				scanf("%s", &s);
//				strcpy(teacher[i].available[j], s);
//			}
//			for (int j = 0; j < num; j++) {
//				printf("%s", teacher[i].available[j]);
//			}
//		}
//		break;
//	}
//}
//void show_class(teacher_inform* teacher, char name[]) {
//	for (int i = 0; i < teacher_n; i++) {
//		if (strcmp(teacher[i].name, name) == 0) {
//			int j = 0;
//			while (teacher[i].available != '\0')
//			{
//				printf("%s", teacher[i].available[j]);
//			}
//		}
//	}
//}




/*
* 学生系统：
* (1):展示备选课程及其信息
* (2):选择课程
*/
//展示指定科目信息(传入科目编号)(√)
void showAppoint_course(struct class_inform* course, int num) {
	printf("%2d", course[num - 1].number);
	printf("%11s", course[num - 1].name);
	printf("%6d", course[num - 1].hourse);
	printf("%7d", course[num - 1].point);
	printf("%12s", course[num - 1].ddl);
	printf("%15d", course[num - 1].class_num);
	printf("%17d\n", course[num - 1].max_peo);
}
//展示每门科目的信息(√)
void showAll_course(struct class_inform* course) {
	printf("编号\t名称\t学时\t学分\t截止时间\t所开班数\t各班人数上限\n");
	for (int i = 0; i < class_n; i++) {
		showAppoint_course(course, i + 1);
	}
}
void init_student() {
	char name[11][10] = { "杨过","周芷若","郭靖","木婉清","陈灵素","张无忌","王语嫣","任盈盈","段誉","苗人凤","萧峰" };
	for (int i = 0; i < student_n; i++) {
		strcpy(student[i].name, name[i]);
	}
}
//展示教师已选课程
void showAppoint_already(char teacher_name[]) {
	printf("已选课程");
	for (int i = 0; i < teacher_n; i++) {
		if (strcmp(teacher[i].name, teacher_name) == 0) {
			for (int j = 0; j < teacher[i].already_num; j++) {
				printf("%s\t", teacher[i].already[j]);
			}
		}
	}
}


/*
* class_inform-科目基本信息：
* init_class(course):初始化学科数组course中所有科目的相关信息
* show_Allcourse(course):展示course学科数组中所有科目的相关信息
* show_appointcourse(course，num):展示学科数组course中标号为num的科目的相关信息
*/
//初始化每门科目的信息(√)
void set_class(struct class_inform* course) {
	char name[10][20] = { "数据结构","高数","英语","体育","线性代数","java","python","c++","c语言","数据实践" };
	int hourse[] = { 45,48,50,50,45,56,48,50,45,45 };
	int point[] = { 3,4,5,4,3,2,3,5,4,4 };
	int class_num[] = { 7,8,6,9,11,9,8,8,10,12 };
	char ddl[][20] = { "4-11","4-11","4-20","4-20","4-11","4-11","4-11","4-11","4-11","4-11" };
	int max_peo[] = { 20,30,40,20,25,35,30,20,35,40 };
	for (int i = 0; i < class_n; i++)
	{
		strcpy(course[i].name, name[i]);
		course[i].hourse = hourse[i];
		course[i].point = point[i];
		course[i].number = i + 1;
		course[i].class_num = class_num[i];
		course[i].max_peo = max_peo[i];
		strcpy(course[i].ddl, ddl[i]);
	}
}
//修改课程信息(√)
void reset_class(struct class_inform* course) {
	showAll_course(course);
	int a, b, f;
	printf("请选择是否修改课程内容(0,1)：");
	scanf("%d", &f);
	while (f == 1)
	{
		printf("输入要修改的课程编号：");
		scanf("%d", &a);
		printf("输入要修改的课程内容( 名称：1 学时：2 学分：3 截止时间：4 所开班数:5 )：");
		scanf("%d", &b);
		switch (b)
		{
		case 1://修改名称
			char c1[10];
			printf("修改内容为：");
			scanf("%s", &c1);
			strcpy(course[a - 1].name, c1);
			break;
		case 2://修改学时
			int hour;
			printf("修改内容为：");
			scanf("%d", &hour);
			course[a - 1].hourse = hour;
			break;
		case 3://修改学分
			int p;
			printf("修改内容为：");
			scanf("%d", &p);
			course[a - 1].point = p;
			break;
		case 4://修改截止时间
			char c2[10];
			printf("修改内容为：");
			scanf("%s", &c2);
			strcpy(course[a - 1].ddl, c2);
			break;
		case 5://修改所开班数
			int n;
			printf("修改内容为：");
			scanf("%d", &n);
			course[a - 1].class_num = n;
			break;
		default:
			printf("输入错误，请重新输入！");
		}
		showAppoint_course(course, a);
		printf("请选择是否继续修改课程内容(0,1)：");
		scanf("%d", &f);
	}
}




//查看希望教授该课程的教师的信息
void show_hopeteacher(struct teacher_inform* teacher) {
	char course_name[10];
	printf("输入课程名称查看想选该课程的教师:");
	scanf("%s", &course_name);
	for (int i = 0; i < teacher_n; i++) {
		//教师基本信息表中第i个教师想选的课程数目
		int hope_num = teacher[i].hope_num;
		for (int j = 0; j < hope_num; j++) {
			if (strcmp(teacher[i].hope[j].class_name, course_name) == 0) {
				//展示该教师信息
				showAppoint_teacher(teacher, teacher[i].name);
			}
		}

	}
}
//判断某门课程是否满员
int check_full(struct class_inform* course,char course_name[]) {
	for (int i = 0; i < class_n; i++) {
		if (strcmp(course[i].name, course_name) == 0) {
			if (course[i].stu_num > course[i].class_num * course[i].max_peo) {
				return 1;//已满员
			}
		}
	}
	return 0;//未满员
}
//添加教师得票数
void change_point(struct class_inform* course, char course_name[], char teacher_name[]) {
	for (int i = 0; i < teacher_n; i++) {
		if (strcmp(teacher[i].name, teacher_name) == 0) {
			for (int j = 0; j < teacher[i].hope_num; j++) {
				if (strcmp(teacher[i].hope[j].class_name, course_name) == 0) {
					teacher[i].hope[j].scores += 1;//该教师的该门课程的票+1
					return;
				}
			}
		}
	}
}
//选择希望选修的课程
void choose_class(char stu_name[]) {
	for (int i = 0; i < student_n; i++) {
		if (strcpy(student[i].name, stu_name) == 0) {
			int num;
			printf("输入想选的课程数目:");
			scanf("%d", &num);
			for (int j = 0; j < num; j++) {
				choose:
				printf("选择课程:");
				char s[10];
				scanf("%s", &s);
				if (check_full(course, s)==1) {//该课程已满员
					goto choose;
				}
				printf("选择该课程任课教师：");
				char a[10];
				char b[10];
				scanf("%s %s", &a, &b);
				strcpy(student[i].hope[j].name, s);
				strcpy(student[i].hope[j].teacher_name[1], a);
				strcpy(student[i].hope[j].teacher_name[2], b);
				change_point(course, s, a);//教师a关于课程s的得票+1
				change_point(course, s, b);//教师b关于课程s的得票+1
				student[i].hope_num += 1;//想选课程+1
			}
		}
	}
}
void pop_class(char stu_name[], char course_name[]) {
	for (int i = 0; i < student_n; i++) {
		if (strcmp(student[i].name, stu_name) == 0) {
			for (int j = 0; j < student[i].hope_num; j++) {
				if (strcmp(student[i].hope[j].name,course_name) == 0) {
					for (int k = j; k < student[i].hope_num-1; k++) {
						student[i].hope[k] = student[i].hope[k + 1];
					}
					student[i].hope_num -= 1;//想选课程-1
					return;
				}
			}
		}
	}
}
//设置每门课程的最终授课老师
void setCourse_already(struct teacher_inform* teacher, char teacher_name[]) {
	//遍历每门科目
	for (int i = 0; i < class_n; i++) {
		//遍历每个教师该门课得分
		for (int j = 0; j < teacher_n; j++) {
			int score_max = 0;
			//遍历教师想选的每门课
			for (int k = 0; k < teacher[j].hope_num; k++) {
				if (strcmp(teacher[j].hope[k].class_name, course[i].name) == 0) {
					if (teacher[j].hope[k].scores > score_max) {
						score_max = teacher[j].hope[k].scores;
						strcpy(course[i].teacher_name, teacher[j].name);//将该门课程的授课老师名字设为第j个老师的名字
					}
				}
			}
		}
	}
}
//设置每个老师的最终授课科目
void setTeacher_already() {
	//遍历每个教师
	for (int i = 0; i < teacher_n; i++) {
		int already = 0;//该教师已选科目
		teacher[i].already = (char**)malloc(sizeof(char*));
		for (int j = 0; j < class_n; j++) {
			if (strcmp(course[j].teacher_name, teacher[i].name) == 0) {
				strcpy(teacher[i].already[already], course[j].name);
				already++;//已选授课数目+1
			}
		}
		teacher[i].already_num = already;
	}
}
//设置每个老师想选的课程
void set_hopeclass(teacher_inform* teacher, char name[]) {
	printf("姓名\t性别\t年龄\t可选课程\t想选课程\n");
	for (int i = 0; i < teacher_n; i++) {
		if (strcmp(teacher[i].name, name) == 0) {
			printf("%s", teacher[i].name);
			printf("可选科目：");
			for (int j = 0; j < teacher[i].ava_num; j++) {
				printf("%s", teacher[i].available[j]);
			}
			printf("输入想教授课程数目:");
			int num;
			scanf("%d", &num);
			teacher[i].hope = (struct teach_class*)malloc(sizeof(teach_class) * num);
			for (int j = 0; j < num; j++) {
				printf("课程%d:", j + 1);
				char s[10];
				scanf("%s", &s);
				strcpy(teacher[i].hope[j].class_name, s);
				teacher[i].hope[j].scores = 0;
			}
		}
		break;
	}
}





//1、学生系统（邓蕊）
void student_system() {
	//(1)查看备选课程及该课程信息(√)
	showAll_course(course);
	//(2)查看希望教授该课程的教师信息
	show_hopeteacher(teacher);
	//(3)选择希望选修的功能
	choose_class("小红");
	//(4)删除误选课程
	pop_class("小红","高数");
}
//2、教师系统(王富贵）
void teacher_system() {
	//(1)查看自己可选课程
	//(2)选择自己想选择的课程 
	set_hopeclass(teacher,"孙山");
	//(3)查看自己已选择的课程
	showAppoint_already("孙山");
}
//3、管理系统(胡婧雯)
void manager_system() {
	//(1)管理课程:确定学分学时等(√)
	reset_class(course);
	//(2)查看课程选修情况
	//(3)查看教师授课情况:教师信息，所选课程。设置教师可选课程
	//3.1查看教师信息
	showAll_teacher(teacher);
	//3.2设置教师可选课程
	choose_class("小红");
}


//初始化信息
void init_data() {
	init_teacher(teacher);
	set_class(course);
}
int main() {
	printf("============菜单==============\n");
	printf("1、学生系统\n");
	printf("2、教师系统\n");
	printf("3、管理系统\n");
	int system;
	printf("选择系统：");
	scanf("%d", &system);
	init_data();
	switch (system)
	{
	case 2:
		teacher_system();
		break;
	case 3:
		manager_system();
		break;
	case 1:
		student_system();
		break;
	}
}
```

## 6.机房预订系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
/// <summary>
/// 一共六个时间段
/// 8-10：8  10-12：10  12-14：12
/// 14-16：14 16-18：16 18-20：18
/// </summary>
struct machine {
	int order;//机器编号
	int time[6];//被预定的所有时间段组成的数组
	int count;//一天中被预定的次数
};
struct list {//20个机器构成一张表
	struct machine* data[20];//存放20个机器的信息
};
struct wait {
	int num;//等待的人数
	int choose[20];//初始选择的时间
	int suggestion[20];//建议选择时间
	char name[20][10];//存放等待的用户姓名
};
void init_machine(struct list* L) {
	printf("依次输入每个机器的被预定次数和预订时间(若为8-10时间段,输入8):\n");
	for (int i = 0; i < 20; i++) {
		printf("第%d个机器:", i + 1);
		struct machine* mac = (struct machine*)malloc(sizeof(struct machine));
		mac->order = i + 1;//设置每个机器的编号
		scanf("%d", &mac->count);
		for (int j = 0; j < mac->count; j++) {
			scanf("%d", &mac->time[j]);
		}
		L->data[i] = mac;//L的第i个数据指向mac
	}
}
//1、查询机位信息(输入时间段查询)
void show(struct list* L) {
	int x;
	printf("输入选择时间(如选择时间段8-10，输入8)：");
	scanf("%d", &x);
	printf("编号\t预订情况\n");
	for (int i = 0; i < 20; i++) {
		printf("%3d\t", L->data[i]->order);
		int flag = 0;//标记该机位在x时段是否被预订,0没被预订，1被预定(初始化0)
		for (int j = 0; j < L->data[i]->count; j++) {
			if (L->data[i]->time[j] == x) {//该机器在x时间被预定
				flag = 1;
				printf("被预定\n");
				break;
			}
		}
		if (flag == 0) {
			printf("空\n");
		}
	}
}
//查找离时间段x最近的有空机位的时间段
void search(struct list* L, struct wait* W,int x) {
	int i = 1;
	while (x >= 8 && x <= 20)
	{
		if (i % 2 == 1) {
			x = x + 2 * i;//找之后的时间
		}
		else
		{
			x = x - 2 * i;//找之前的时间
		}
		for (int i = 0; i < 20; i++) {
			int flag = 1;//标记第i个机器在x时段是否可预订,1可预订
			for (int j = 0; j < L->data[i]->count; j++) {
				if (L->data[i]->time[j] == x) {//该机器在此时段不可预订
					flag = 0;
					break;
				}
			}
			if (flag == 1) {//该机器在此时段可预订
				printf("最近的时间段:%d-%d", x, x + 2);
				W->suggestion[W->num] = x;//将x设为新增等待人的建议选择时间
				return;//提供了最近的时间段，退出
			}
		}
		i++;
	}
	printf("已经无可预订时间段");
}
//2、机位预订(有空机位则预订编号最小的机位)
void booking(struct list* L, struct wait* W) {
	char s[10];
	printf("输入预订机器的用户姓名：");
	scanf("%s", &s);
	int x;
	printf("输入预订时间(如选择时间段8-10，输入8)：");
	scanf("%d", &x);
	for (int i = 0; i < 20; i++) {
		int flag = 1;//标记该机位在x时段是否可预订,0不可，1可(初始化可)
		for (int j = 0; j < L->data[i]->count; j++) {
			if (L->data[i]->time[j] == x) {//该机器在x时间被预定
				flag = 0;//该机位不可预订
				break;
			}
		}
		if (flag == 1)
		{
			L->data[i]->time[L->data[i]->count] = x;//将时间段x添加进该机位的被预定数组time里面
			L->data[i]->count++;//该机位可预订,则预订该机位,该机位的被预定次数+1
			printf("预订机位编号为:%d\n", L->data[i]->order);
			printf("预订成功！");
			return;//预订完成，退出
		}
	}
	printf("该时间段无可预订机位!\n");
	//新增等待人数
	W->choose[W->num] = x;//新的等待用户的初始选择时间设为x
	strcpy(W->name[W->num], s);//新的等待用户的名字设为s
	search(L, W, x);//查找建议选择时间
	W->num++;//等待人数+1
}
//3、退出预订
void cancel(struct list* L) {
	int x;//时间
	int number;//机器号
	printf("输入取消预订的时间(若为时间段8-10，输入8)和机器号:");
	scanf("%d%d", &x, &number);
	for (int i = 0; i < 20; i++) {
		if (L->data[i]->order == number) {//找到了该机器
			for (int j = 0; j < L->data[i]->count; j++) {//遍历该机器的每个被预定时间
				if (L->data[i]->time[j] == x) {//找到了该机器预订的这个时间
					for (int k = j; k < L->data[i]->count - 1; k++) {//覆盖掉该预定时间
						L->data[i]->time[k] = L->data[i]->time[k + 1];
						printf("取消成功!\n");
						break;
					}
					L->data[i]->count--;//该机器的被预定次数-1
					return;
				}
			}
		}
	}
	printf("未查找到相应预订信息！\n");
}
//4、查询等待信息
void search_wait(struct list* L, struct wait* W) {
	if (W->num == 0) {
		printf("无等待用户!");
		return;
	}
	printf("等待用户\t初始选择时间\t建议选择时间\n");
	for (int i = 0; i < W->num; i++) {
		printf("%5s", W->name[i]);
		printf("%16d-%d\t", W->choose[i], W->choose[i]+2);
		printf("%5d-%d\n", W->suggestion[i], W->suggestion[i]+2);
	}
}
//5.查询机位被预定时间段
void search_machine(struct list* L) {
	printf("编号\t预订时间段\n");
	for (int i = 0; i < 20; i++) {
		int flag = 0;//标记是否有预定时间段
		printf("%3d", L->data[i]->order);
		for (int j = 0; j < L->data[i]->count; j++) {
			printf("\t%d-%d ", L->data[i]->time[j], L->data[i]->time[j]+2);
			flag = 1;
		}
		if (flag == 0) {
			printf("\t   无");
		}
		printf("\n");
	}
}
int main()
{
	int a;
	printf("\t\t机房机位预订系统\n");
	printf("==============================================\n");
	printf("\t\t1.输入各个机位的预订信息\n");//外加功能1
	printf("\t\t2.根据时间段查询机位信息\n");
	printf("\t\t3.机位预订\n");
	printf("\t\t4.退出预订\n");
	printf("\t\t5.查询等待信息\n");
	printf("\t\t6.查询机位被预定时间段\n");//外加功能2
	printf("\t\t7.退出系统\n");
	printf("==============================================\n");
	struct list* L = (struct list*)malloc(sizeof(struct list));//创建机器数组
	struct wait* W = (struct wait*)malloc(sizeof(struct wait));//创建等待表
	W->num = 0;//初始等待人数初始化为0
	//init_machine(L);
	do {
		printf("\n请选择功能:");
		scanf("%d", &a);
		switch (a) {
		case 1:
			init_machine(L);//1.输入各个机位的预订信息
			break;
		case 2:
			show(L);//2、查询机位信息
			break;
		case 3:
			booking(L,W);//3、机位预订
			break;
		case 4:
			cancel(L);//4、退出预订
			break;
		case 5:
			search_wait(L, W);//5、查询等待信息
			break;
		case 6:
			search_machine(L);//6、查询各机器的预订时间段
			break;
		}
		printf("\n===============================================\n");
	} while (a > 0 && a < 7);
}
```

## 7.教室信息管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
#define N 50 //教室数目
struct room {
	char name[10];//教室名称 
	int volum;//容量
	char classify[20];//类别
};
struct list
{
	struct room* data[N];//存储所有教室数据
	int count;//表中已有数据条数
};
void write(struct list* L){//把链表中的每条教室数据写入文件
	FILE* fp;
	// 打开文件，没有文件自动创建
	fp = fopen("教室.txt", "w+"); // b:表示以二进制写入
	if (fp == NULL)
	{
		printf("未找到该文件!");
		exit(0);
	}
	char list[50][50];//创建50个长度为50的字符数组，每个数组存放一个教室的相关信息
	for (int i = 0; i < L->count; i++)
	{
		char s[50] = "";//用于存放每个教室的"名称容量类别"组成的字符串
		strcpy(s, L->data[i]->name);//将名称赋值给s
		strcat(s, "   ");//在s后面拼接几个空格

		char v[10] = "";//存放教室容量的字符串格式
		itoa(L->data[i]->volum, v, 10);
		strcat(s, v);//将教室容量volum的字符串形式添加进s
		strcat(s, "   ");//在s后面拼接几个空格

		strcat(s, L->data[i]->classify);//将教室类别classify形式添加进s
		strcat(s, "\n");//在s末尾添加换行符
		fwrite(s, strlen(s), 1, fp);//将s写入文档
	}
	fclose(fp);
}
//1、输入教室信息
void init(struct list* L) {// 初始化数据
	int count;
	printf("输入初始教室数目:");
	scanf("%d", &count);
	printf("输入教室的名称、容量、类别:\n");
	for (int i = 0; i < count; i++) {
		printf("输入:");
		//开创一个新的教室空间
		struct room* new_room = (struct room*)malloc(sizeof(struct room));
		//给教师的名称容量类别赋值
		scanf("%s", &new_room->name);
		scanf("%d", &new_room->volum);
		scanf("%s", &new_room->classify);
		L->data[i] = new_room;//教室数据表L的第i个数据指向new_room
	}
	L->count = count;
	write(L);//将现有数据写入文件
}
// 2、输出教室信息
void show(struct list* L) {
	printf("教室名称    容量       类别\n");
	for (int i = 0; i < L->count; i++) {
		printf("%5s", L->data[i]->name);
		printf("%10d", L->data[i]->volum);
		printf("%15s\n", L->data[i]->classify);
	}
}
//3、添加教室信息
void add(struct list* L)//添加数据
{
	struct room* new_room = (struct room*)malloc(sizeof(struct room));
	printf("输入添加教室的名称:");
	scanf("%s", &new_room->name);
	printf("输入添加教室的容量:");
	scanf("%d", &new_room->volum);
	printf("输入添加教室的类别:");
	scanf("%s", &new_room->classify);
	L->data[L->count] = new_room;//L的最后一条inform变为new
	L->count++;
	write(L); //将现有数据写入文件
}
//4、删除教室信息
void pop(struct list* L)//删除数据
{
	char s[10];
	printf("输入要删除教室的名称:");
	scanf("%s", s);
	for (int i = 0; i < L->count; i++)
	{
		if (strcmp(L->data[i]->name, s) == 0)//找到要删除的inform行
		{
			L->count--;//表L数据总数-1
			for (int j = i; j < L->count; j++)
			{
				L->data[j] = L->data[j + 1];//i之后所有数据前移
			}
			printf("删除成功!\n");
			write(L);//将现有数据写入文件
			return;
		}
	}
	printf("没找到该教室!\n");
}
//5、教室排序
void sort_v(struct list* L) {//按容量排序
	for (int i = 0; i < L->count-1; i++) {
		for (int j = 0; j < L->count - i - 1; j++) {
			if (L->data[j]->volum > L->data[j + 1]->volum) {
				struct room* new_room = (struct room*)malloc(sizeof(struct room));
				new_room = L->data[j];
				L->data[j] = L->data[j + 1];
				L->data[j + 1] = new_room;
			}
		}
	}
}
void sort_cla(struct list* L) {//按分类排序
	for (int i = 0; i < L->count - 1; i++) {
		for (int j = 0; j < L->count - i - 1; j++) {
			if (strcmp(L->data[j]->classify , L->data[j + 1]->classify)>0) {
				struct room* new_room = (struct room*)malloc(sizeof(struct room));
				new_room = L->data[j];
				L->data[j] = L->data[j + 1];
				L->data[j + 1] = new_room;
			}
		}
	}
}
void sort(struct list* L) {
	int c;
	printf("选择排序方式1(容量)/2(类别):");
	scanf("%d", &c);
	if (c == 1) {//c=调用按容量排序的函数
		sort_v(L);
	}
	else//c=调用按类别排序的函数
	{
		sort_cla(L);
	}
	show(L);
}
//6、查找教室信息
void search(struct list* L) {
	char s[10];
	printf("输入查找的教室名称:");
	scanf("%s", &s);
	for (int i = 0; i < L->count; i++) {
		if (strcmp(L->data[i]->name, s) == 0) {
			printf("教室名称    容量       类别\n");
			printf("%5s", L->data[i]->name);
			printf("%10d", L->data[i]->volum);
			printf("%15s\n", L->data[i]->classify);
			return;
		}
	}
	printf("未查找到相关信息！");
}
int main()
{
	int choose;
	struct list* L = (struct list*)malloc(sizeof(struct list));
	L->count = 0;//初始化教室数目为0
	printf("\t\t教室信息管理系统\n");
	printf("==============================================\n");
	printf("\t\t1.输入教室信息:\n");
	printf("\t\t2.输出教室信息:\n");
	printf("\t\t3.添加教室信息:\n");
	printf("\t\t4.删除教室信息\n");
	printf("\t\t5.教室排序(容量、分类):\n");
	printf("\t\t6.查找教室信息\n");
	printf("\t\t7.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择功能:");
		scanf("%d", &choose);
		printf("\n");
		if (choose == 1)
		{
			init(L);//输入
		}
		else if (choose == 2)
		{
			show(L);//显示
		}
		else if (choose == 3)
		{
			add(L);//添加
		}
		else if (choose == 4)
		{
			pop(L);//删除
		}
		else if (choose == 5)
		{
			sort(L);//排序
		}
		else if(choose==6)
		{
			search(L);//查找
		}
		printf("\n===============================================\n");
	} while (choose > 0 && choose < 7);
}
```

## 8.图书信息管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
struct book {
	char name[10];//书名
	int order;//编号
	int sell;//售出数目
	int number;//数量
};
struct list {
	struct book* data[50];
	int num;//总的图书类
};
//7、将图书数据写入文件图书.txt并保存
void writebook(struct list* L){//把链表中的每条图书数据写入文件
	FILE* fp;
	// 打开文件，没有文件自动创建
	fp = fopen("图书.txt", "w+"); 
	if (fp == NULL)
	{
		printf("未找到该文件!");
		exit(0);
	}
	char list[50][50];//创建50个长度为50的字符数组，每个数组存放一个图书的相关信息
	for (int i = 0; i < L->num; i++)
	{
		char s[50] = "";//用于存放每种图书的"名称 编号 出售数目 总数"组成的字符串
		strcpy(s, L->data[i]->name);//将名称赋值给s
		strcat(s, "   ");//在s后面拼接几个空格

		char order[10] = "";//存放编号的字符串格式
		itoa(L->data[i]->order, order, 10);
		strcat(s, order);//将图书编号的字符串形式添加进s
		strcat(s, "   ");//在s后面拼接几个空格

		char sell[10] = "";//存放出售数目的字符串格式
		itoa(L->data[i]->sell, sell, 10);
		strcat(s, sell);//将出售数目的字符串形式添加进s
		strcat(s, "   ");//在s后面拼接几个空格

		char num[10] = "";//存放图书总数的字符串格式
		itoa(L->data[i]->number, num, 10);
		strcat(s, num);//将图书总数的字符串形式添加进s
		strcat(s, "   ");//在s后面拼接几个空格

		strcat(s, "\n");//在s末尾添加换行符
		fwrite(s, strlen(s), 1, fp);//将s写入文档
	}
	fclose(fp);
}
//6、输入图书信息
void input(struct list* L) {
	int num;//共有num本不同名字的书
	printf("输入要录入多少种类别的图书:");
	scanf("%d", &num);
	printf("依次输入每类图书的书名、编号、售出数目、总数:\n");
	for (int i = 0; i < num; i++) {
		printf("第%d类:",i+1);
		struct book* bk = (struct book*)malloc(sizeof(struct book));
		scanf("%s", &bk->name);
		scanf("%d", &bk->order);
		scanf("%d", &bk->sell);
		scanf("%d", &bk->number);
		L->data[i] = bk;//表L的第i个数据指向bk
	}
	L->num=num;//L中的图书类别等于录入的图书类别
}
//1、输出图书信息
void showbook(struct list* L) {
	printf("书名\t编号\t已售数目\t总数\n");
	printf("==============================================\n");
	for (int i = 0; i < L->num; i++) {
		printf("%5s", L->data[i]->name);
		printf("%5d", L->data[i]->order);
		printf("%10d", L->data[i]->sell);
		printf("%14d\n", L->data[i]->number);
	}
}
//2、添加(进书)
void addbook(struct list* L) {
	struct book* bk = (struct book*)malloc(sizeof(struct book));
	printf("添加的图书信息:\n");
	printf("1、名称:");
	scanf("%s", &bk->name);
	printf("2、编号:");
	scanf("%d", &bk->order);
	printf("3、售出数目:");
	scanf("%d", &bk->sell);
	printf("4、总数:");
	scanf("%d", &bk->number);
	L->data[L->num] = bk;//表L的最后一个图书数据项指向bk
	L->num++;//L中的图书类别+1
}
//3、删除图书信息(售卖)
void popbook(struct list* L)//删除数据
{
	char s[10];
	printf("输入要删除的图书名称:");
	scanf("%s", s);
	for (int i = 0; i < L->num; i++)
	{
		if (strcmp(L->data[i]->name, s) == 0)//找到要删除的inform行
		{
			L->num--;//表L的图书类别
			for (int j = i; j < L->num; j++)
			{
				L->data[j] = L->data[j + 1];//i之后所有数据前移
			}
			printf("删除成功!\n");
			return;
		}
	}
	printf("没找到该图书!\n");
}
void sellbook(struct list* L) {
	char s[10];
	printf("输入要售出的图书名称:");
	scanf("%s", s);
	for (int i = 0; i < L->num; i++)
	{
		if (strcmp(L->data[i]->name, s) == 0)//找到要删除的inform行
		{
			if (L->data[i]->sell == L->data[i]->number) {//售出数目=图书总数目
				printf("该图书已售罄！");
			}
			else
			{
				L->data[i]->sell++;//没售罄则售出数目+1
			}
		}
	}
}
void pop_sell(struct list* L) {
	int c;
	printf("选择1(删除图书信息)/2(出售图书):");
	scanf("%d", &c);
	if (c == 1) {//选择1调用删除函数
		popbook(L);
	}
	else
	{
		sellbook(L);//选择2调用出售函数
	}
}
//4、排序(编号、书名、数量)
void sortbook(struct list* L) {//排序
	int c;
	printf("选择排序依据1(编号)/2(书名)/3(数量):");
	scanf("%d", &c);
	for (int i = 0; i < L->num-1; i++) {
		for (int j = 0; j < L->num - i - 1; j++) {
			int flag = 0;//标记是否满足交换条件(1满足,0不满足)
			if (c == 1 && L->data[j]->order > L->data[j + 1]->order) {//按编号排序
				flag = 1;
			}
			else if (c == 2 && strcmp(L->data[j]->name, L->data[j + 1]->name) > 0) {//按书名排序
				flag = 1;
			}
			else if (c == 3 && L->data[j]->number > L->data[j + 1]->number){//按数量排序
				flag = 1;
			}
			if (flag==1) {//满足交换条件交换前后两类图书数据
				struct book* bk = (struct book*)malloc(sizeof(struct book));
				bk = L->data[j];
				L->data[j] = L->data[j + 1];
				L->data[j + 1] = bk;
			}
		}
	}
	showbook(L);//排序完输出排序后的数据
}
//5、查询(按书名)
void searchbook(struct list* L) {
	char s[10];
	printf("输入查找的图书名称:");
	scanf("%s", &s);
	for (int i = 0; i < L->num; i++) {
		if (strcmp(L->data[i]->name, s) == 0) {
			printf("书名\t编号\t已售数目\t总数\n");
			printf("%5s", L->data[i]->name);
			printf("%5d", L->data[i]->order);
			printf("%10d", L->data[i]->sell);
			printf("%15d\n", L->data[i]->number);
			return;
		}
	}
	printf("未查找到相关书籍！");
}
int main()
{
	int a;
	struct list* L = (struct list*)malloc(sizeof(struct list));
	L->num = 0;//L中的图书类别初始化为0，最开始没有图书
	printf("\t\tt图书信息管理系统\n");
	printf("==============================================\n");
	printf("\t\t1.输入图书信息\n");
	printf("\t\t2.输出图书信息:\n");
	printf("\t\t3.添加图书信息:\n");
	printf("\t\t4.删除or出售图书信息\n");
	printf("\t\t5.排序(编号、书名、数量):\n");
	printf("\t\t6.查询图书信息(按书名)\n");
	printf("\t\t7.将图书数据写入文件图书.txt并保存\n");
	printf("\t\t8.退出系统\n");
	printf("==============================================\n");
	do {
		printf("\n请选择功能:");
		scanf("%d", &a);
		switch (a) {
		case 1:
			printf("输入图书信息:\n\n");
			input(L);//1、输入
			break;
		case 2:
			printf("输出图书信息:\n\n");
			showbook(L);//2、输出
			break;
		case 3:
			printf("添加图书信息:\n\n");
			addbook(L);//3、添加
			break;
		case 4:
			printf("删除or出售图书信息:\n\n");
			pop_sell(L);//4、删除or出售
			break;
		case 5:
			printf("排序:\n\n");
			sortbook(L);//5、排序
			break;
		case 6:
			printf("查询图书信息:\n\n");
			searchbook(L);//查询
			break;
		case 7:
			printf("将图书数据写入文件图书.txt并保存:\n\n");
			writebook(L);//查询
			printf("保存成功！");
			break;
		}
		printf("\n===============================================\n");
	} while (a > 0 && a < 7);
}
```

## 9.商品信息管理系统
```c
#define _CRT_SECURE_NO_WARNINGS
#include <math.h>
#include <stdio.h>
#include <string.h>
struct goods 
{
	char name[10];//商品名称
	int rest;//库存
	int number;//编号
	int price;//单价
	int sell;//已售
};
struct list 
{
	struct goods* data[50];//商品列表
	int count;//商品总类
};
//1.商品上架
void write(struct list* L) 
{//把链表中的每条教室数据写入文件
	FILE* fp;
	// 打开文件，没有文件自动创建
	fp = fopen("商品信息.txt", "w+"); // b:表示以二进制写入
	if (fp == NULL)
	{
		printf("未找到该文件!");
		exit(0);
	}
	char list[50][50];//创建50个长度为50的字符数组，每个数组存放一个商品的相关信息
	for (int i = 0; i < L->count; i++)
	{
		char s[50] = "";
		char num[10] = "";
		itoa(L->data[i]->number, num, 10);
		strcat(s, num);
		strcat(s, " ");
		strcat(s, L->data[i]->name);
		strcat(s, " ");
		itoa(L->data[i]->price, num, 10);
		strcat(s, num);
		strcat(s, " ");
		itoa(L->data[i]->rest, num, 10);
		strcat(s, num);
		strcat(s, "\n");//在s末尾添加换行符
		fwrite(s, strlen(s), 1, fp);//将s写入文档
	}
	fclose(fp);
}
//输入商品
void input_goods(struct list* L) 
{
	int count;
	printf("输入添加的商品的总类:");
	scanf("%d", &count);
	printf("输入商品的编号、名称、价格、库存:\n");

	for (int i = 0; i < count; i++) 
	{
		printf("第%d件:",i+1);
		//开创一个新的空间
		struct goods* new_goods = (struct goods*)malloc(sizeof(struct goods));
		
		//给商品的名称容量类别赋值
		scanf("%d", &new_goods->number);
		scanf("%s", &new_goods->name);
		scanf("%d", &new_goods->price);
		scanf("%d", &new_goods->rest);
		new_goods->sell = 0;

		//商品数据表L的第i个数据指向new_room
		L->data[i] = new_goods;
	}
	L->count = count;
	//将现有数据写入文件
	write(L);
}
//4.商品浏览
void show_goods(struct list* L) {
	printf("\t商品编号   名称  价格\t库存\t已售");
	printf("\n=============================================\n");

	for (int i = 0; i < L->count; i++) 
	{
		printf("\t%5d", L->data[i]->number);
		printf("%10s", L->data[i]->name);
		printf("%5d", L->data[i]->price);
		printf("%7d", L->data[i]->rest);
		printf("%8d\n", L->data[i]->sell);
	}
}
//2.修改商品信息
void modify_goods(struct list* L) 
{
	int num;
	printf("输入要修改的商品的编号:");
	scanf("%d", &num);

	for (int i = 0; i < L->count; i++) 
	{
		//匹配到了输入编号对应的商品
		if (L->data[i]->number == num) 
		{
			printf("输入修改后的商品信息:\n");
			printf("名称:");
			scanf("%s", &L->data[i]->name);
			printf("价格:");
			scanf("%d", &L->data[i]->price);
			printf("库存:");
			scanf("%d", &L->data[i]->rest);
			printf("修改成功!\n");
			show_goods(L);
			return;
		}
	}
	printf("该商品编号不存在!\n");
}
//3.删除商品
void pop_goods(struct list* L) 
{
	int num;
	printf("输入要删除的商品的编号:");
	scanf("%d", &num);

	for (int i = 0; i < L->count; i++) 
	{
		//匹配到了输入编号对应的商品
		if (L->data[i]->number == num) 
		{
			//后面商品信息全部前移
			for (int j = i; j < L->count; j++) 
			{
				L->data[j] = L->data[j + 1];
			}
			//商品总类-1
			L->count--;
			printf("删除成功!\n");
			//将当前数据写入文件
			write(L);
			show_goods(L);
			return;
		}
	}
	printf("该商品编号不存在!\n");
}
//5.商品销售
void sell_goods(struct list* L) 
{
	int priceAll = 0;
	while (1)
	{
		printf("输入商品编号和购买数量(空格隔开):");
		int num;
		int need;
		scanf("%d%d", &num, &need);
		int price;
		int flag;
		price= 0;
		flag = 0;

		for (int i = 0; i < L->count; i++) 
		{
			//匹配到了输入编号对应的商品
			if (L->data[i]->number == num) 
			{
				//fag用于标记是否找到了对应标号的商品
				flag = 1;
				if (L->data[i]->rest < need) 
				{//库存小于需求
					printf("库存不足，仅售卖%d份,", L->data[i]->rest);
					//计算当前商品总消费
					price = L->data[i]->price * L->data[i]->rest;
					//更新销售量
					L->data[i]->sell += L->data[i]->rest;
					//更新库存
					L->data[i]->rest = 0;
				}
				else
				{
					//计算当前商品总消费
					price = L->data[i]->price * need;
					//更新销售量
					L->data[i]->sell += need;
					//更新库存
					L->data[i]->rest-=need;
				}
			}
		}
		if (flag == 0) 
		{
			printf("不存在该商品编号!");
		}
		priceAll+=price;

		printf("按y继续购买:");
		char choose[5];
		scanf("%s", &choose);
		if (strcmp(choose, "y") != 0) 
		{
			break;
		}
	}
	printf("需支付的总金额为:%d\n",priceAll);
}
//6.商品销售情况查询(按销售量排序)
void check_sell(struct list* L) 
{
	//按sell值冒泡排序
	for (int i = 0; i < L->count; i++) 
	{
		for (int j = 0; j < L->count - i - 1; j++) 
		{
			//当前商品的销售量小于后一商品销售量两者交换位置
			if (L->data[j]->sell < L->data[j + 1]->sell) 
			{
				struct goods* good = L->data[j];
				L->data[j] = L->data[j + 1];
				L->data[j + 1] = good;
			}
		}
	}
	show_goods(L);
}
void search_sell(struct list* L) 
{
	printf("请选择?1查看某商品销售信息/2查看商品销售量排行:");
	int c;
	scanf("%d", &c);
	if (c == 2)
	{
		//查看按销售量排好序的商品信息
		check_sell(L);
	}
	else
	{
		int num;
		printf("输入查询的商品编号:");
		scanf("%d", &num);
		for (int i = 0; i < L->count; i++)
		{
			//匹配到了输入编号对应的商品
			if (L->data[i]->number == num)
			{
				printf("编号:%d\n", L->data[i]->number);
				printf("名称:%s\n", L->data[i]->name);
				printf("价格:%d\n", L->data[i]->price);
				printf("库存:%d\n", L->data[i]->rest);
				printf("销售量:%d\n", L->data[i]->sell);
				return;
			}
		}
		printf("该商品编号不存在！");
	}
}
int main() 
{
	//初始化商品列表
	struct list* L = (struct list*)malloc(sizeof(struct list));
	L->count = 0;
	printf("\t\t商品信息管理系统\n");
	printf("==============================================\n");
	printf("\t\t1.商品上架:\n");
	printf("\t\t2.修改商品信息:\n");
	printf("\t\t3.删除商品信息:\n");
	printf("\t\t4.商品浏览\n");
	printf("\t\t5.商品销售:\n");
	printf("\t\t6.销售情况查询(按销售量排序)\n");
	printf("\t\t7.退出系统\n");
	printf("==============================================\n");
	int choose;
	do 
	{
		printf("\n请选择功能:");
		scanf("%d", &choose);
		printf("\n");
		if (choose == 1)
		{
			input_goods(L);
		}
		else if (choose == 2)
		{
			modify_goods(L);
		}
		else if (choose == 3)
		{
			pop_goods(L);
		}
		else if (choose == 4)
		{
			show_goods(L);
		}
		else if (choose == 5)
		{
			sell_goods(L);
		}
		else if (choose == 6)
		{
			search_sell(L);
		}
		printf("\n===============================================\n");
	} 
	while (choose > 0 && choose < 7);
	return 0;
}
```

[//]: # (![运行结果]&#40;./runpic/商品信息管理系统.png&#41;)

