---
title: 快排C++&Python
date:  2019-03-31 16:04:44
categories:
- C++
- Python
tags:
- 习题
- 算法
---

## C++

1．i =left; j =right; 找一个基准数（随意，这里不妨找a[left]=a[i]），将其挖出。
2．j--由后向前找比它小的数，找到后挖出此数填前一个坑a[i]中。
3．i++由前向后找比它大的数，找到后也挖出此数填到前一个坑a[j]中。
4．重复2，3步，直到i==j，将基准数填入a[i]中。

```c++
#include<iostream>
#include<stdlib.h>
using namespace std;
void quick(int a[],int left,int right)
{
	if(left>=right)
		return;
	int i=left,j=right,key=a[left];
	while(i<j)
	{
		while((i<j)&&a[j]>=key)
			j--;
		if(i<j)
			a[i++]=a[j];
		while((i<j)&&a[i]<key)
			i++;
		if(i<j)
			a[j--]=a[i];
	}
	a[i]=key;
	quick(a,left,j-1);
	quick(a,j+1,right);

}
int main()
{
	int a[]={-1,-9,4,66,123,452,665,1,34};
	static int len=sizeof(a)/sizeof(int);//不用static len会变
	
	for(int i=0;i<len;i++)
	cout<<a[i]<<" ";
	cout<<endl;

	quick(a,0,len-1);

	for(int i=0;i<len;i++)
	cout<<a[i]<<" ";
	cout<<endl;
	system("pause");
	return 0;
} 
```

## Python

1.记位于列表中间的数为mid
2.大于mid的放入greater列表
3.小于mid的放入less列表
4.递归greater和less列表
```python
def quick(arr):
    if(len(arr)<2):
        return arr
    mid=arr[len(arr)//2]
    less,greater=[],[]
    arr.remove(mid)
    for num in arr:
        if(num>mid):
            greater.append(num)
        else:
            less.append(num)
    return quick(less)+[mid]+quick(greater)
    
arr=[1,2,5,5,5,5,31,4]
arr=quick(arr)
print(' '.join([str(i)for i in arr]))
```