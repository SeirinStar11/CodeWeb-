# C语言实战题目
### 1.函数求值


## 1.函数求值
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
int F(int x)//求各个位置的乘积
{
	int p = 1;
	while (x > 0)
	{
		p *= x % 10;
		x = x / 10;
	}
	return p;
}
int G(int x,int n)
{
	if (n == 0)//i为0直接返回x
	{
		return x;
	}
	int p=F(x);//i！=0，求i次F（x）
	while (n>1)
	{
		p = F(p);
		n--;//完成一次，剩下求解次数-1
	}
	return p;//返回乘积
}
int main()
{
	int T;
	int a[10];//放置每行输入的结果
	printf("测试数量:");
	scanf_s("%d", &T);
	int x, n;
	printf("输入x、n:\n");
	for (int i = 0; i < T; i++)
	{
		scanf_s("%d%d", &x, &n);
		a[i] = G(x, n);
	}
	printf("输出:\n");
	for (int i = 0; i < T; i++)
	{
		printf("%d\n", a[i]);
	}
}
```

## 2.交点数量
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
int intersection(int x,int y,int R)
{
	int tox = fabs(y);//到x轴的距离
	int toy = fabs(x);//圆心到y轴的距离
	if (tox > R && toy > R)//两坐标轴均与圆心相离
	{
		return 0;
	}
	else if ((tox > R && toy == R) || (tox == R && toy > R))//与一条坐标轴相切，与另一条相离
	{
		return 1;
	}
	else if (tox == R && toy == R)//与两轴相切
	{
		return 2;
	}
	else if ((tox<R && toy>R) || (tox > R && toy < R))//一相交一相离
	{
		return 2;
	}
	else if ((tox == R && toy < R) || (tox < R && toy == R))//一相交一相切
	{
		return 3;
	}
	else
	{
		return 4;
	}
}
int main()
{
	int T;
	int a[10];//保存每次的输出结果
	printf("测试次数:");
	scanf("%d", &T);
	int x, y, R;
	printf("输入x，y，R：\n");
	for (int i = 0; i < T; i++)
	{
		scanf("%d%d%d", &x, &y, &R);
		a[i] = intersection(x, y, R);
	}
	printf("输出:\n");
	for (int i = 0; i < T; i++)
	{
		printf("%d\n", a[i]);
	}
}
```

## 3.谁是小明
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#define Max 100
int input(int digit[])//传入存放所有学号的数组,返回输入数字的个数
{
	int i = 0;//输入的数字个数
	char ch;//每一次键盘的输入值
	printf("输入不超过5位数的学号(空格隔开，不超过100个数):");
	do {
		scanf("%d", &digit[i]);//将输入的数作为digit[i]的值，空格结束一个数的输入
		i++;//输入的个数+1
	} while ((ch = getchar()) != '\n');//当键盘输入不是回车时继续输入下一个数，回车时跳出循环
	return i;
}
void judge(int digit[],int n,int real)//n为digit数组里的整数个数，real表示小明真实学号
{
	int index = -1;//标记小明的位置
	for (int i = 0; i < n; i++)
	{
		if (index == -1 && digit[i] == real)//digit[i]之前没有和小明学号一样的且digit[i]和小明学号一样
		{
			index = i + 1;//小明的位置标记为i+1
		}
		else if (index != -1 && digit[i] == real)//digit[i]之前已经有和小明学号一样的且digit[i]和小明学号一样
		{
			printf("-1");//则无法判断小明，输出-1;
			return;//结束判断
		}
	}
	if (index == -1)//完成以上搜索之后index仍等于-1即没有和小明学号一样的数字
	{
		printf("NOT FOUND");//输出找不到
	}
	else
	{
		printf("%d", index);//找到了，输出小明的位置
	}
}
int main()
{
	int digit[Max];//存放输入的所有位数不超过5的学号
	int n=input(digit);//获取输入数字的个数
	judge(digit, n, 18032);
}
```

## 4.圣诞树
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
int main()
{
	int n;
	printf("圣诞树棵树：");
	scanf_s("%d", &n);
	for (int k = 0; k<n; k++)//每棵小树
	{
		for (int i = 0; i < k+2; i++)//每棵树的每一层数
		{
			for (int j = 0; j < n-i; j++)
			{
				printf(" ");//左空格
			}
			printf("/");//右斜线
			for (int j = n - i + 1; j < (n + 3) * n / 2 -(n-i+2); j++)
			{
				if (i == k + 1)
				{
					printf("_");
				}
				else
				{
					printf(" ");//中间空格
				}
			}
			printf("\\");//左斜线
			printf("\n");
		}
	}
	//画下面的树根
	for (int i = 0; i < n - 1; i++)//遍历每一层
	{
		for (int i = 0; i < n; i++)
		{
			printf(" ");
		}
		printf("||\n");
	}
}
```

## 5.正方形数量
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
int Square(int a, int b)
{
	//边长为1的正方形有axb个，为2的有(a-1)*(b-1)个，为3的有(a-2)*(b-2)个...
	int s = 0;//正方形总个数
	while (a > 0 && b > 0)
	{
		s += a * b;//a*b为每种不同大小的正方形的个数
		a--;
		b--;
	}
	return s;
}
int main()
{
	int T, a, b;
	printf("输入T：");
	scanf("%d", &T);
	printf("输入a、b:\n");
	int arr[10];//存放每次的结果
	for (int i = 0; i < T; i++)
	{
		scanf("%d %d", &a, &b);
		arr[i] = Square(a, b);
	}
	printf("输出\n");
	for (int i = 0; i < T; i++)
	{
		printf("%d\n", arr[i]);
	}
}
```

## 6.走方格
```c
#define _CRT_SECURE_NO_WARNINGS
#include <stdio.h>
#include <math.h>
#include <string.h>
#include<malloc.h> 
#define maxn 20
int len = 100;
char min[10];
struct graph//定义方格类
{
	int n;//行数
	int m;//列数
    char weight[maxn][maxn];//方格各个位置的值
    int visit[maxn][maxn];//用来标记方格各个位置是已经添加在路径中
};
void init(struct graph* G)//初始化方格
{
    int n, m;//行数和列数。
    printf("输入行列数:");
    scanf("%d%d", &n, &m); //输入行数(n>=2)和列数(m<=20)
    int** W; //二维指针W用于表示nxm的方格。W[i][j]表示第i行j列的值
    W = (int**)malloc(sizeof(int*) * n);//申请一组一维指针空间。
    for (int i = 0; i < n; i++)
    {
        W[i] = (int*)malloc(sizeof(int) * m); //对于每个一维指针，申请一行数据的空间。
    }
    printf("\n输入方格:\n");
    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            scanf("%s", &W[i][j]);//输入第i行第j列的数据。其中&p[i][j]也可以写作p[i]+j或者是 *(p+i) + j. 功能相同。
            G->weight[i][j] = W[i][j];
            G->visit[i][j] = 0;//初始方格中的每个位置都不在最短路径中
        }
    }
    G->n = n; G->m = m;
}
void walk(struct graph* G,int i,int j,char path[])//i，j为当前位置，path用来记录路径
{
    if (G->weight[i][j] == 'E')//如果当前位置的值为E，则已经找到一条路径
    {
        if (strlen(path) < len)//如果这条路径的距离最小
        {
            strcpy(min, path);//将最小的路径赋值给最短路径min
            len = strlen(path);
        }
        path[strlen(path) - 1] = '\0';//把路径中最后一步删掉回溯到上一步
        printf("\n");
        return;
    }
    if (i-1>=0 && G->visit[i-1][j]==0 && G->weight[i - 1][j] != '#')//上方位置没添加到路径中且i、j没超出方格范围
    {
        path[strlen(path)] = 'U';//向上U添加到路径
        G->visit[i-1][j] = 1;//已经添加过，标记为1
        walk(G, i - 1, j,path);
        G->visit[i - 1][j] = 0;
    }
    if (i+1<G->n && G->visit[i + 1][j]==0 && G->weight[i + 1][j] != '#')//下方位置没添加到路径中且i、j没超出方格范围
    {
        path[strlen(path)] = 'D';//向下D添加到路径
        G->visit[i + 1][j] = 1;
        walk(G, i+1, j, path);
        G->visit[i + 1][j] = 0;
    }
    if (j-1>=0 && G->visit[i][j - 1] ==0 && G->weight[i][j-1] != '#')//左方位置没添加到路径中且i、j没超出方格范围
    {
        path[strlen(path)] = 'L';//向左添加到路径
        G->visit[i][j - 1] = 1;
        walk(G, i, j-1, path);
        G->visit[i][j - 1] = 0;
    }
    if (j+1<G->m && G->visit[i][j + 1] ==0 && G->weight[i][j+1] != '#')//右方位置没添加到路径中且i、j没超出方格范围
    {
        path[strlen(path)] = 'R';//向右添加到路径
        G->visit[i][j + 1] = 1;
        walk(G, i, j+1, path);
        G->visit[i][j + 1] = 0;
    }
    path[strlen(path) - 1] = '\0';
}
int main()
{
    struct graph* G = (struct graph*)malloc(sizeof(struct graph));//构建一个方格
    init(G);//初始化方格
    char path[10]="";
    walk(G,0,0,path);//遍历s到e的路径
    printf("最短路径:%s", min);
    return 0;
}
```

## 7.最小开支
```c
#include <stdio.h>
#include <math.h>
#include <string.h>
int get_min(int m, int a, int b)
{
	int max1;//双人房的最多订的间数
	if (m % 2 == 0)
	{
		max1 = m / 2;
	}
	else
	{
		max1 = m / 2 + 1;
	}
	int min = 10000;//最小开支初始化为一个很大的数
	for (int i = 0; i <= max1; i++)
	{
		int x1 = i;
		int x2 = 0;
		if (2 * i > m)//无剩余人数，x2=0
		{
			x2 = 0;
		}
		else if ((m - 2 * i) % 3 == 0)//有剩余，刚好住满三人间
		{
			x2 = (m - 2 * i) / 3;
		}
		else
		{
			x2 = (m - 2 * i) / 3 + 1;//有剩余且无法刚好住满三人间
		}
		int W = a * x1 + b * x2;//该x1、x2下的开支
		if (W < min)//找到所有分配方式的最小开支赋值给min
		{
			min = W;
		}
	}
	return min;
}
int main()
{
	int m;//学生数
	int a;//双人房
	int b;//三人房
	int n;//测试案例数量
	scanf("%d", &n);
	int w[10];//存放每个案例的最小开支
	for (int i = 0; i < n; i++)
	{
		scanf("%d%d%d", &m, &a, &b);
		w[i] = get_min(m, a, b);
	}
	for (int i = 0; i < n; i++)
	{
		printf("%d%\n", w[i]);
	}
}
```
