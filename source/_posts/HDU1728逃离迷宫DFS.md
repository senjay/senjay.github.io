---
title: HDU1728逃离迷宫DFS
date: 2019-09-03 15:08:54
categories:
- 习题
tags:
- 习题
- DFS
---

## 逃离迷宫

Problem Description

　　给定一个m × n (m行, n列)的迷宫，迷宫中有两个位置，gloria想从迷宫的一个位置走到另外一个位置，当然迷宫中有些地方是空地，gloria可以穿越，有些地方是障碍，她必须绕行，从迷宫的一个位置，只能走到与它相邻的4个位置中,当然在行走过程中，gloria不能走到迷宫外面去。令人头痛的是，gloria是个没什么方向感的人，因此，她在行走过程中，不能转太多弯了，否则她会晕倒的。我们假定给定的两个位置都是空地，初始时，gloria所面向的方向未定，她可以选择4个方向的任何一个出发，而不算成一次转弯。gloria能从一个位置走到另外一个位置吗？

 


Input

　　第1行为一个整数t (1 ≤ t ≤ 100),表示测试数据的个数，接下来为t组测试数据，每组测试数据中，
　　第1行为两个整数m, n (1 ≤ m, n ≤ 100),分别表示迷宫的行数和列数，接下来m行，每行包括n个字符，其中字符'.'表示该位置为空地，字符'*'表示该位置为障碍，输入数据中只有这两种字符，每组测试数据的最后一行为5个整数k, x1, y1, x2, y2 (1 ≤ k ≤ 10, 1 ≤ x1, x2 ≤ n, 1 ≤ y1, y2 ≤ m),其中k表示gloria最多能转的弯数，(x1, y1), (x2, y2)表示两个位置，其中x1，x2对应列，y1, y2对应行。





Output

　　每组测试数据对应为一行，若gloria能从一个位置走到另外一个位置，输出“yes”，否则输出“no”。

 


Sample Input
```c++
2
5 5
...**
*.**.
.....
.....
*....
1 1 1 1 3
5 5
...**
*.**.
.....
.....
*....
2 1 1 1 3
```





Sample Output
```
no
yes
```

**思路:dfs,并且用一个辅助二维数组记录当前坐标的最小转向次数,每次转向path+1,同时每次向四周走的时候用turn记录当前方向,此题的坑在输入的x代表列，y代表行。**

```c++
#include<iostream>
using namespace std;
char map[100][100];
int tt[100][100];//记录最小转向次数 
int n, m;
int k, x1, yy1, x2, y2;
int ans;
void dfs(int x, int y, int path, int turn)//x,y 坐标,path 转向次数, turn 方向分别为2468，下左右上,参考街机 
{
	if (x == y2 - 1 && y == x2 - 1 && tt[y2 - 1][x2 - 1] <= k)//出口 
		return;
	if (x<0 || x>n - 1 || y<0 || y>m - 1 || map[x][y] == '*' || tt[x][y] > k)//不符合 
		return;
	if (map[x + 1][y] == '.')//向左 
	{
		if (turn == 2 || turn == 0)//检查当前方向是否为左或初始点 
		{
			if (path<=tt[x + 1][y])//转向次数是否比tt数组记录中的小或等于,这边一定要用小于等于, 因为可能有不同路径 
			{
				tt[x + 1][y] = path;
				dfs(x + 1, y, path, 2);
			}

		}
		else//方向不同要转向 path+1,并调整方向 
		{
			if (path + 1<=tt[x + 1][y])
			{
				tt[x + 1][y] = path + 1;
				dfs(x + 1, y, path + 1, 2);
			}
		}

	}
	if (map[x - 1][y] == '.')
	{
		if (turn == 8 || turn == 0)
		{
			if (path<=tt[x - 1][y])
			{
				tt[x - 1][y] = path;
				dfs(x - 1, y, path, 8);
			}
		}
		else
		{
			if (path + 1<=tt[x - 1][y])
			{
				tt[x - 1][y] = path + 1;
				dfs(x - 1, y, path + 1, 8);
			}
		}
	}
	if (map[x][y + 1] == '.')
	{
		if (turn == 6 || turn == 0)
		{
			if (path<=tt[x][y + 1])
			{
				tt[x][y + 1] = path;
				dfs(x, y + 1, path, 6);
			}
		}
		else
		{
			if (path + 1<=tt[x][y + 1])
			{
				tt[x][y + 1] = path + 1;
				dfs(x, y + 1, path + 1, 6);
			}
		}
	}
	if (map[x][y - 1] == '.')
	{
		if (turn == 4 || turn == 0)
		{
			if (path<=tt[x][y - 1])
			{
				tt[x][y - 1] = path;
				dfs(x, y - 1, path, 4);
			}
		}
		else
		{
			if (path + 1<=tt[x][y - 1])
			{
				tt[x][y - 1] = path + 1;
				dfs(x, y - 1, path + 1, 4);
			}
		}
	}
}
int main()
{
	int t;
	cin >> t;
	while (t--)
	{
		cin >> n >> m;
		for (int i = 0; i<n; i++)
			for (int j = 0; j<m; j++)
			{
				cin >> map[i][j];
				tt[i][j] = 10000;
			}
		cin >> k >> x1 >> yy1 >> x2 >> y2;
		tt[yy1 - 1][x1 - 1] = 0;
		ans = 0;
		
		dfs(yy1 - 1, x1 - 1, 0, 0);

		if (tt[y2 - 1][x2 - 1]<=k)
			cout << "yes" << endl;
		else
			cout << "no" << endl;
	}
	return 0;
}
```

