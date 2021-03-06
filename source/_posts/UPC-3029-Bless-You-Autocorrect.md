---
layout: pos
title: UPC-3029 Bless You Autocorrect!(字典树+BFS)
date: 2020-01-08 18:01:30
tags: BFS
---
Typing on phones can be tedious. It is easy to make typing mistakes, which is why most phones come with an autocorrect feature. Autocorrect not only ﬁxes common typos, but also suggests how to ﬁnish the word while you type it. Jenny has recently been pondering how she can use this feature to her advantage, so that she can send a particular message with the minimum amount of typing.
The autocorrect feature on Jenny’s phone works like this: the phone has an internal dictionary of words sorted by their frequency in the English language. Whenever a word is being typed,autocorrect suggests the most common word (if any) starting with all the letters typed so far. By pressing tab, the word being typed is completed with the autocorrect suggestion. Autocorrect can only be used after the ﬁrst character of a word has been typed –it is not possible to press tab before having typed anything. If no dictionary word starts with the letters typed so far, pressing tab has no effect.
Jenny has recently noticed that it is sometimes possible to use autocorrect to her advantage even when it is not suggesting the correct word, by deleting the end of the autocorrected word. For instance, to type the word “autocorrelation”, Jenny starts typing “aut”, which then autocorrects to “autocorrect” (because it is such a common word these days!) when pressing tab. By deleting the last two characters (“ct”) and then typing the six letters “lation”, the whole word can be typed using only 3 (“aut”) + 1 (tab) + 2 (backspace twice) + 6 (“lation”) = 12 keystrokes, 3 fewer than typing “autocorrelation” without using autocorrect. 
Given the dictionary on the phone and the words Jenny wants to type, output the minimum number of keystrokes required to type each word. The only keys Jenny can use are the letter keys, tab and backspace.

输入
The ﬁrst line of input contains two positive integers n (1 ≤ n ≤ 105 ), the number of words in the dictionary, and m (1 ≤ m ≤ 10 5 ), the number of words to type. Then follow n lines with one word per line, sorted in decreasing order of how common the word is (the ﬁrst word is the most common). No word appears twice in the dictionary. Then follow m lines, containing the words to type.
The dictionary and the words to type only use lower case letters ‘ a ’-‘ z ’. The total size  of the input ﬁle is at most 1 MB.

输出
For each word to type, output a line containing the minimum number of keystrokes required to type the corresponding word.

样例输入
5 5
austria
autocorrect
program
programming
computer
autocorrelation
programming
competition
zyx
austria

样例输出
12
4
11
3
2

**题意:** 给出n个单词，表示输入法的词典中存在这些单词，现在有三种操作：
1.输入一个字符
2.删除一个字符
3.根据已有字符自动补全某个单词剩余的字符
如样例中
austria
autocorrect
program
programming
computer
如果输入au，则可以自动补全出第一个带au前缀的单词austria
若输入pro，自动补全出program，而不是programming
现在有q次询问，问输入某个单词用的最少的操作次数是多少

**题解：** 首先可以知道，对于一个单词，如果已知词典中有一个比目的单词长的单词，我们可以通过补全，再删除一些字符得到的操作会比直接打完全部字符用的操作更少。
再比如一个单词很长，但是我们可以通过自动补全快速得到一部分前缀，这样也省了操作次数。

对于最小操作的问题，我们不必考虑得到最优化操作的过程，因为能够减少操作次数的只有字典上的自动补全了，否则任何一个跟补全无关的目的单词都得全部一个一个打全。

因此对于已知字符进行处理。建立一棵字典树，对于这个字典树，我们知道因为有补全操作，因此树上每一个非单词结尾的节点到达该路径的终结节点存在一条边，表示一步到达。如果建了边，这就不是一棵树了，因此我们要根据这棵字典树重新建图。

使用vector建图，将某个字典中的单词的所有字符都向终结节点连一条边，注意这样的连边只是新节点才需要的，注意题意中说自动补全出来的只是第一次输入的存在该前缀的按此，因此每个节点第一次创建时，他的补全位置就是唯一的。且不会有变动。

建图后从根节点进行广搜，为啥是广搜，因为广搜得到的顺序是到达一个节点的最短路程。通过广搜到达的节点的先后顺序可以得到输入词典中的该字符的最小操作。

剩下的事情就很简单了，输入一个单词，我们先对其在字典上搜索，得到匹配的一个最长前缀的节点，然后之前也得到了输入到该节点的最小操作，那么直接用最小操作+剩余字符长度即可得到该单词通过补全操作得到的最小操作次数。

当然也有一些情况是根本不需要补全操作就能得到更小的操作次数的，因此最后那上面求出的结果与单词原长取一个最小值即结果。

具体通过字典树得到的图可以参考题解PPT中的这幅图
![在这里插入图片描述](https://img-blog.csdn.net/20181014224233983?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2t1cm9uZWtvbmFubw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
即建树再建图再跑广搜。

```c
#include<bits/stdc++.h>
#define LL long long
#define M(a,b) memset(a,b,sizeof a)
#define pb(x) push_back(x)
using namespace std;
const int maxn=1e6+7;
int tri[maxn][26],tot=0;
vector<int >mp[maxn];
void insert(char a[],int rt)
{
    int len=strlen(a);
    vector<int>q;
    for(int i=0;i<len;i++)
    {
        int x=a[i]-'a';
        if(!tri[rt][x])
        {
            tri[rt][x]=++tot;
            M(tri[tot],0);
            if(i<len-2)q.pb(tot);
            mp[rt].pb(tot);
            mp[tot].pb(rt);
        }
        rt=tri[rt][x];
    }
    for(int i=0;i<q.size();i++)mp[q[i]].pb(rt);
}
int dis[maxn];
void bfs()
{
    queue<int>q;
    q.push(1);
    dis[1]=0;
    while(!q.empty())
    {
        int tp=q.front();
        q.pop();
        for(int i=0;i<mp[tp].size();i++)
        {
            if(!dis[mp[tp][i]])
            {
                dis[mp[tp][i]]=dis[tp]+1;
                q.push(mp[tp][i]);
            }
        }
    }
}
int finds(char a[],int rt)
{
    int len=strlen(a),cnt=0;
    for(int i=0;i<len;i++)
    {
        int x=a[i]-'a';
        if(!tri[rt][x])break;
        rt=tri[rt][x];
        cnt++;
    }
    return min(len,len-cnt+dis[rt]);
}
int n,q;
char str[maxn];
int main()
{
    int rt=++tot;
    M(tri[rt],0);
    M(dis,0);
    scanf("%d%d",&n,&q);
    for(int i=1;i<=n;i++)
    {
        scanf("%s",str);
        insert(str,rt);
    }
    bfs();
    while(q--)
    {
        scanf("%s",str);
        printf("%d\n",finds(str,rt));
    }
}
```
