---
layout: pos
title: JavaScript splice() 函数
date: 2020-01-08 15:39:58
tags: JavaScript
---
今天遇到一个JavaScript里array的函数splice，构造非常奇特
W3Cschool中有如下介绍，不在赘述参数内容：
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190902134911131.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2t1cm9uZWtvbmFubw==,size_16,color_FFFFFF,t_70)

可以看到，其中的参数第一个是操作的数组下标index，而第二个是删除个数，之后的可选参数是增加内容，第一次看到让人疑惑的是，这并没有说明何时是删除，何时是增加。

也就说，操作位置可以确定，当我只想增加的时候参数应该长这样:
array.splice(index,0,[增加元素])

当我只想删除的时候参数长这样：
array.splice(index,删除个数)
此时之后没有需要增加的元素

如此人为控制你需要用于增加还是删除
***
当然可以知道，若需要清空数组就可以用如下操作
array.splice(0,array.lenght)
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190902135426375.png)


但是如果我们填入参数时，删除个数不为0，且存在增加元素，该函数如何运作

![在这里插入图片描述](https://img-blog.csdnimg.cn/2019090213553287.png)
此处我们在下标3上删除2个元素，并增加一个元素
结果是删除了下标为3的值，并且增加了一个元素，因为下标位3往后只存在一个元素，所以删除2个元素不成立，只删除了一个，并且不会报错越界。

![在这里插入图片描述](https://img-blog.csdnimg.cn/20190902135717549.png)
当我继续从下标0开始删除2个元素并添加一个元素时，删除了值1,2，并在原来的位置增加了一个元素。