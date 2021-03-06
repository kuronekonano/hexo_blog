---
layout: pos
title: 目前为止见到的精妙面试算法题【部分剑指offer原题】
date: 2020-01-08 17:49:50
tags:
---
都是思维题，不容易想到，但是结果非常简单易懂。

**题目为：给你1-1000个连续自然数，然后从中随机去掉两个，再打乱顺序，要求只遍历一次，求出被去掉的两个数。**
使用异或。

说说异或的两个特性：顺序无关 / 对一个数异或两次等于没有异或。顺序无关就是说异或的元素可以随意交换顺序，而不会影响结果。异或两次可以理解为+x和-x。

 

首先，这两个数组(打乱前和打乱后)各自异或，也就是1 ^ 2 ^ … ^ 1000，得到两个异或值。再对这两个异或值进行一次异或，这样就得到了x^y的指(重复部分互相抵消了)。

 
获取计算出的异或值的1所在的位置，并继续异或
因为x和y是两个不同的整数，所以这两个数的异或结果，转化为二进制的话，一定在某位是1，假设在第3位。也就是说如果把原始数组按第3位是否为0进行划分，就可以分成两个数组，每个数组各包含一个被抽取的数。如果打乱后的数组也按这个规则划分为两个数组，这样就得到了4个数组，其中两组是第3位为0，另外两组是第3位为1。把第3位为0的两个数组所有元素进行异或就能得到被抽取的一个数，同理也就能获得另外一个被抽取的数，于是问题解决。



**题目：一个序列中任意两数的异或最大值**     
01字典树，复杂度nlogn


**题目：在一个无序数组中O(n)找出一个数字，该数在数组中出现次数超过数组大小的一半。**
首先可以知道既然超过数组数量的一半，说明该数连续排序后中位数一定是ans。
然后最傻x的做法就是排序找中位数，或者另开空间计算数的出现次数。
【解法一】记得快速排序的排序部分吗？不是递归部分，是排序部分，将标准值放到了左边数都比其小，右边数都比其大的位置，那么这个标准值返回的下标若等于n/2即中位数，就是结果，若下标<n/2那么ans一定在该数右边的序列，若下标>n/2，ans一定在该数左边的序列，这样二分的查找一个中位数即可。

【解法二】既然该数出现的次数已经大于了数组的一半，那么一定大于剩余数出现次数的总和，于是考虑O(n)遍历数组，用一个值记录出现的数，一个值记录出现次数，若下一个数等于记录的数，则出现次数+1，若不等，则-1，当出现次数被减到0时，替换记录的数，并把出现次数记为1，那么最后，因为不同的数出现次数一定抵不过相同数出现次数，所以最终遍历完数组，一定是存储着次数超过数组大小一半的ans，并且出现次数还大于等于1
**(摩尔投票算法)**
```c
class Solution {
public:
    int MoreThanHalfNum_Solution(vector<int> numbers) {
        int cnt=1,last=numbers[0],ans=numbers[0];
        if(numbers.size()==1)return last;
        for(int i=1;i<numbers.size();++i)
        {
            if(last==numbers[i])cnt++;
            else cnt--;
            if(cnt==0)last=numbers[i],cnt=1,ans=last;
        }
        cnt=0;
        for(int i=0;i<numbers.size();++i)if(numbers[i]==ans)cnt++;
        return cnt*2>numbers.size()?ans:0;
    }
};
```

**算法题：给你一个无序数组，对于区间[l，r]，求所有区间中sum(l…r)乘以min(l…r)的积最大的值。**
对于数组中的每一个数，所有以它为最小值的区间里sum最大的一定是它左边和右边分别到比他小的第一个数那里，这个显而易见，所以枚举一遍所有的数就可以了。先预处理出前缀和，然后用单调栈正着和反着各走一遍找出每个数左边和右边第一个比他小的数，最后枚举每一个数算sum*min即可

原题如下：【来自头条面试题】
**给定一个数组序列, 需要求选出一个区间, 使得该区间是所有区间中经过如下计算的值最大的一个：
区间中的最小数 * 区间所有数的和最后程序输出经过计算后的最大值即可，不需要输出具体的区间。如给定序列  [6 2 1]则根据上述公式, 可得到所有可以选定各个区间的计算值:**

 

[6] = 6 * 6 = 36;

[2] = 2 * 2 = 4;

[1] = 1 * 1 = 1;

[6,2] = 2 * 8 = 16;

[2,1] = 1 * 3 = 3;

[6, 2, 1] = 1 * 9 = 9;

 

从上述计算可见选定区间 [6] ，计算值为 36， 则程序输出为 36。

区间内的所有数字都在[0, 100]的范围内;


输入描述:
第一行输入数组序列长度n，第二行输入数组序列。
对于 50%的数据,  1 <= n <= 10000;
对于 100%的数据, 1 <= n <= 500000;

输出描述:
输出数组经过计算后的最大值。

输入例子1:
3
6 2 1

输出例子1:
36

代码如下：

```c
#include<bits/stdc++.h>
using namespace std;
#define LL long long
const int maxn=500005;
struct node
{
    int pos;
    LL val;
}a[maxn];
stack<node>s;
int n;
LL sum[maxn];
int lpos[maxn];
int rpos[maxn];
int main()
{
    scanf("%d",&n);
    for(int i=1;i<=n;i++)scanf("%lld",&a[i].val),a[i].pos=i,sum[i]=sum[i-1]+a[i].val;
    s.push(a[1]);
    for(int i=2;i<=n;i++)
    {
        while(!s.empty()&&a[i].val<s.top().val)
        {
            rpos[s.top().pos]=i-1;
            s.pop();
        }
        s.push(a[i]);
    }
    while(!s.empty())rpos[s.top().pos]=n,s.pop();
    s.push(a[n]);
    for(int i=n-1;i>=1;i--)
    {
        while(!s.empty()&&a[i].val<s.top().val)
        {
            lpos[s.top().pos]=i;
            s.pop();
        }
        s.push(a[i]);
    }
    while(!s.empty())lpos[s.top().pos]=0,s.pop();
    LL ans=0;
    for(int i=1;i<=n;i++)
    {
        LL tmp=a[i].val*(sum[rpos[i]]-sum[lpos[i]]);
        if(tmp>ans)ans=tmp;
    }
    printf("%lld\n",ans);
}

```


**题目：一个数组里除了一个数字之外，其他的数字都出现了两次。请写程序找出这个只出现一次的数字。**

这个题目的突破口在哪里？题目为什么要强调有一个数字出现一次，其他的出现两次？我们想到了异或运算的性质：任何一个数字异或它自己都等于0。也就是说，如果我们从头到尾依次异或数组中的每一个数字，那么最终的结果刚好是那个只出现依次的数字，因为那些出现两次的数字全部在异或中抵消掉了。


**算法题：给一组数字，这些数字里面每一个都重复出现了三次，只有一个数字只出现了一个，要求在时间O（n）空间O（1）内解出来。**

  int类型有32位，假如二进制位不进位的话，对数组中除了single数以外的所有数做累加，32位每一位上的值都是3。假如我对每一位是做模3加法的话，把数组所有数加起来，32位每一位的值应该有1有0，因为三个相同数加起来都会模3变为0，所以剩下的1都是属于single数的，所以这个累加和就等于single数。
这个方法时间是O(32*N)，但要分别计算每一位的累加，分别作32次累加。空间是O(1)。

```c
class Solution {
public:
    int singleNumber(int A[], int n) {
        int result = 0;
        int sum;
        int i, j;
        for(i=0;i<32;i++)
        {
            sum = 0;
            for(j=0;j<n;j++)sum += ((A[j]>>i)&1); //每轮只对固定某一位做累加
            if(sum%3 == 1)result = result|(1<<i);
        }
        return result;
    }
};

```

**链表反转**
没用网上普遍的三指针平移做法，自己想了一个奇葩做法，三指针的到时候看一下，没准面试官只认这个呢orz

```c
/*
struct ListNode {
	int val;
	struct ListNode *next;
	ListNode(int x) :
			val(x), next(NULL) {
	}
};*/
class Solution {
public:
    ListNode* ReverseList(ListNode* pHead) {
        if(pHead==NULL)return pHead;
        ListNode* p=pHead;
        ListNode* q=pHead->next;
        ListNode* tmp=pHead;
        while(p->next!=NULL)
        {
            p->next=q->next;
            q->next=tmp;
            tmp=q;
            if(p->next==NULL)break;
            q=p->next;
        }
        return tmp;
    }
};

```

**镜像二叉树**
以为是什么高大上的可怕模拟，然而忘了指针，不用对值操作，只要递归交换俩指针就行了orz

```c
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    void Mirror(TreeNode *pRoot) {
        if(pRoot==NULL)return;
        TreeNode *tmp=pRoot->left;
        pRoot->left=pRoot->right;
        pRoot->right=tmp;
        Mirror(pRoot->left);
        Mirror(pRoot->right);
    }
};
```
**平衡二叉树转化为有序双向链表**
输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。

**思路：** 从根节点出发，递归调用函数遍历该二叉树，设置一个链表的尾指针，在遍历二叉树回溯过程中接上尾指针，并后移尾指针。总体上看代码结构类似中序遍历平衡二叉树，众所周知，中序遍历平衡二叉树即可以得到一个有序序列。
```c
/*
struct TreeNode {
	int val;
	struct TreeNode *left;
	struct TreeNode *right;
	TreeNode(int x) :
			val(x), left(NULL), right(NULL) {
	}
};*/
class Solution {
public:
    TreeNode* plast=NULL;
    TreeNode* Convert(TreeNode* pRootOfTree)
    {
        ConvertNode(&plast,pRootOfTree);
        TreeNode* phead=plast;
        while(phead!=NULL&&phead->left!=NULL)phead=phead->left;
        return phead;
    }
    void ConvertNode(TreeNode** plast,TreeNode* proot)
    {
        if(proot==NULL)return;
        TreeNode* cur=proot;
        if(cur->left!=NULL)ConvertNode(plast,cur->left);//若有左儿子则继续递归遍历
        cur->left=*plast;//回溯过程中将当前结点接入递归途中的结果链表尾指针
        if(*plast!=NULL)(*plast)->right=cur;//并修改当前尾指针的右孩子指向位置
        *plast=cur;//后移尾指针
        if(cur->right!=NULL)ConvertNode(plast,cur->right);///以上操作相当于完成了当前结点除右孩子之外所有节点的连接，作为中序遍历也是最后遍历右孩子，连接步骤和左子树一样
    }
};
```


**题目：P为给定的二维平面整数点集。定义 P 中某点x，如果x满足 P 中任意点都不在 x 的右上方区域内（横纵坐标都大于x），则称其为“最大的”。求出所有“最大的”点的集合。（所有点的横坐标和纵坐标都不重复, 坐标轴范围在[0, 1e9) 内）**

如下图：实心点为满足条件的点的集合。请实现代码找到集合 P 中的所有 ”最大“ 点的集合并输出。


![在这里插入图片描述](https://img-blog.csdnimg.cn/20190314115654344.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2t1cm9uZWtvbmFubw==,size_16,color_FFFFFF,t_70)

输入描述:
第一行输入点集的个数 N， 接下来 N 行，每行两个数字代表点的 X 轴和 Y 轴。
对于 50%的数据,  1 <= N <= 10000;
对于 100%的数据, 1 <= N <= 500000;

输出描述:
输出“最大的” 点集合， 按照 X 轴从小到大的方式输出，每行两个数字分别代表点的 X 轴和 Y轴。

输入例子1:
5
1 2
5 3
4 6
7 5
9 0

输出例子1:
4 6
7 5
9 0

简单水题，因为被定义为最外层点的条件是右上角没有点，因此不符合条件的点满足的是且的关系【即既要大于X又要大于Y】，可以知道一旦有任意一个条件不符合，那么就是最外层点，因此我们只需要判断X或Y是否符合条件就好了，将二维问题转换为一维，首先按X或Y值将所有点排序，从后往前遍历，因为是这样的遍历顺序，因此在一个维度上肯定是不符合条件的，如，已知某个点的X值大于自己，那么唯一有可能让自己成为外层点的条件就是Y值必须大于那个点。从后往前遍历的过程中首先X就都比遍历到的点大了，于是维护一个最大的Y值，如果遍历到的点Y值大于最大的Y值，就没有点在其右上角，直接判定为最外层点，并更新最大Y值，继续判断。

最后倒序输出所有记录的点即可


代码如下：

```c
#include<bits/stdc++.h>
using namespace std;
#define LL long long
#define maxn 500005
struct point
{
    LL x,y;
    bool operator <(const point &a)const
    {
        if(x==a.x)return y<a.y;
        return x<a.x;
    }
} a[maxn];
int main()
{
    vector<point>ans;
    ans.clear();
    int n;
    scanf("%d",&n);
    for(int i=0; i<n; i++)
    {
        scanf("%lld%lld",&a[i].x,&a[i].y);
    }
    sort(a,a+n);
    int yy=a[n-1].y;
    if(n)ans.push_back(a[n-1]);
    for(int i=n-2; i>=0; i--)
    {
        if(a[i].y>=yy)ans.push_back(a[i]);
        if(yy<a[i].y)yy=a[i].y;
    }
    if(ans.size())
    {
        for(int i=ans.size()-1; i>=0; i++)
            printf("%lld %lld\n",ans[i]);
    }
}

```
**题目描述**
**定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。**
维护一个单调栈，因为是先进先出，因此后进的值若是小于栈顶那么很有可能成为栈内最小值，因此入栈，否则，该元素直到出栈都不会影响整个栈的最小值
```c
class Solution {
public:
    stack<int>q,p;
    void push(int value) {
        q.push(value);
        if(p.empty()||p.top()>value) p.push(value);
        return;
    }
    void pop() {
        if(p.top()==q.top())p.pop();
        q.pop();
        return;
    }
    int top() {
        return q.top();
    }
    int min() {
        return p.top();
    }
};
```
**题目: 给一棵树,输出这棵树每层的第一个值.[也被称为树的左视图]**
思路:最裸的包括我面试时用的方法都是层序遍历,对于每一层用一个队列或vector存下,对于每一层统一广搜,当记录每一层的第一个值时放入结果中.

```c
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
class Solution {
public:
        vector<vector<int> > Print(TreeNode* pRoot) {
            vector<int>ans;
            vector<int>tmp;
            queue<TreeNode*>q;
            int nownum=1;
            int nextnum=0;
            q.push(pRoot);
            while(!q.empty())
            {
                TreeNode* top=q.front();
                q.pop();
                if(top==nullptr)continue;
                if(top->left!=nullptr)nextnum++;
                if(top->right!=nullptr)nextnum++;
                tmp.push_back(top->val);
                if(nownum==1)
                {
                    ans.push_back(tmp[0]);
                    tmp.clear();
                    nownum=nextnum;
                    nextnum=0;
                }
                else nownum--;
                q.push(top->left);
                q.push(top->right);
            }
            return ans;
        }
     
};

```

但是这个方法并不足够优秀,因为其开辟了一个空间来存储一整层的信息,用于判断深度,来找到每层的第一个值.这其实是可以避免的,我们可以利用前序遍历的特点,前序遍历的序列中即可判断每层的第一个值,并计入结果中.

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190701180952198.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2t1cm9uZWtvbmFubw==,size_16,color_FFFFFF,t_70)
可以知道前序遍历时遇到的每层第一个节点一定是最左边的节点,因此记录一个当前遍历dep值,记录一个遍历时的最大深度cnt,当dep等于cnt时,表示第一次走到一个最大深度,那么此时遍历到的值一定是该层最左边的节点,输出即可,然后cnt++表示我们要寻找下一个深度的最左边值,等到下次当前深度dep与之相等时继续输出并增加最大深度
```c
/*
struct TreeNode {
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
            val(x), left(NULL), right(NULL) {
    }
};
*/
#include <bits/stdc++.h>
using namespace std;
struct TreeNode
{
    int val;
    struct TreeNode *left;
    struct TreeNode *right;
    TreeNode(int x) :
        val(x), left(NULL), right(NULL)
    {
    }
};
void create(TreeNode* & root)
{
    int x;
    scanf("%d",&x);
    if(x==0)
    {
        root=NULL;
    }
    else
    {
        root=new TreeNode(0);
        root->val=x;
        create(root->left);
        create(root->right);
    }
}
int cnt;
void Print(TreeNode* pRoot,int dep)
{
    dep++;
    if(pRoot==NULL)
    {
        return;
    }
    if(dep==cnt)
    {
        cnt++;
        printf("%d ",pRoot->val);
    }
    if(pRoot->left!=NULL)Print(pRoot->left,dep);
    if(pRoot->right!=NULL)Print(pRoot->right,dep);
}

int main()
{
    TreeNode* t;
    create(t);
    cnt=1;
    Print(t,0);
}
```
从此,之后若是再出现诸如,**树每层的最后一个值 [树的右视图]** 时,也可以用先遍历最右节点这样相同的方法实现

```c
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int cnt=1;
    vector<int>ans;
    void dfs(TreeNode* root,int dep)
    {
        dep++;
        if(root==NULL)return;
        if(dep==cnt)
        {
            ans.push_back(root->val);
            cnt++;
        }
        if(root->right!=NULL)dfs(root->right,dep);
        if(root->left!=NULL)dfs(root->left,dep);
    }
    vector<int> rightSideView(TreeNode* root) {
        dfs(root,0);
        return ans;
    }
};
```
