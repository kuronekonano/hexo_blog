---
layout: pos
title: Hrbust-1284 编辑距离【LCS最长公共子序列】 /  leetcode 72.编辑距离
date: 2020-01-08 17:40:58
tags: LeetCode
---
编辑距离
Time Limit: 1000 MS	Memory Limit: 65536 K
Total Submit: 937(198 users)	Total Accepted: 373(190 users)	Rating: 	Special Judge: No
Description

俄罗斯科学家Vladimir
Levenshtein在1965年提出了编辑距离概念。编辑距离，又称Levenshtein距离，是指两个字符串之间，由一个转成另一个所需的最少编辑操作次数。许可的三种编辑操作包括插入一个字符、删除一个字符、将一个字符替换成另一个字符。
至今，编辑距离一直在相似句子检索的领域中发挥着不可忽视的作用。

我们不妨来设计一个程序，计算两个字符串的编辑距离。

Input
输入数据的第一行是一个正整数，表示一共有几组数据。
每组数据有两行，每行一个字符串。

* 每个字符串长度不超过1000

 - 字符串中只含小写英文字母

Output
对于每组数据，请输出一个整数表示两个字符串的编辑距离。
每个答案占一行。

Sample Input
2
david

vivian

abc

aabbcc

Sample Output
4
3


**思路：** LCS最长公共子序列模板题，利用动态规划，按顺序对比字符，分三种情况，若a串当前字符与b串当前字符相等，那么直接从前一个字符继承修改次数，否则选择删去该字符或替换该字符，或增加该字符，删除和增加是相对于a串或b串而言的，因此分别从两串的上一状态+1来继承并延续修改次数，对于当前字符，选择最小的一种修改方式赋值，用于构造最终最优解。
```c
#include<stdio.h>///最长公共子序列
#include<string.h>
#include<algorithm>
using namespace std;
int dp[1008][1009],i,j,t;
int main()
{

    char x[1005];
    char y[1004];
    scanf("%d",&t);
    while(t--)
    {
        scanf("%s %s",x+1,y+1);//为方便对数组中每个位置的元素计数，输入字符串从首地址位置+1开始
        int lx=strlen(x+1);
        int ly=strlen(y+1);
        for(i=1;i<=lx;i++)
            dp[i][0]=i;//第几个字符就是和0位相比，增添几次，所以次数刚好是字符的位置编号
        for(i=1;i<=ly;i++)
            dp[0][i]=i;
        dp[0][0]=0;//初始值都是为0
        for(i=1;i<=lx;i++)
        {
            for(int j=1;j<=ly;j++)//动态规划,一个一个字符比较，若相同，则继承之前的次数，不同，则从三种变换中筛选最优解
            {
                if(x[i]==y[j])//一直累加到最后是最终变化结果
                    dp[i][j]=dp[i-1][j-1];//继承
                else
                    dp[i][j]=dp[i-1][j-1]+1;//变换
                dp[i][j]=min(dp[i-1][j]+1,min(dp[i][j],dp[i][j-1]+1));//三种情况，插入（增加）、删减、变换，从中取最优解（改变次数最少的）
//                dp[i][j]=min(dp[i][j-1]+1,dp[i][j]);
            }
        }
        printf("%d\n",dp[lx][ly]);
    }
    return 0;
}

```

```py
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        str1=len(word1)
        str2=len(word2)
        dp = [[0 for i in range(str1+1)] for j in range(str2+1)]
        for i in range(1,str1+1):
            dp[0][i]=i
        for i in range(1,str2+1):
            dp[i][0]=i
        dp[0][0]=0
        for i in range(1,str2+1):
            for j in range(1,str1+1):
                dp[i][j] = dp[i-1][j-1] if word2[i-1]==word1[j-1] else dp[i-1][j-1]+1
                dp[i][j] = min(dp[i-1][j]+1, min(dp[i][j],dp[i][j-1]+1))
        return dp[str2][str1]
                    
```
