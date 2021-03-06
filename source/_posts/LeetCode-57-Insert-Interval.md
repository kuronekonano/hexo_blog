---
layout: pos
title: LeetCode 57.插入区间
date: 2020-01-08 17:20:23
tags: LeetCode
---
给出一个无重叠的 ，按照区间起始端点排序的区间列表。

在列表中插入一个新的区间，你需要确保列表中的区间仍然有序且不重叠（如果有必要的话，可以合并区间）。


>输入: intervals = [[1,3],[6,9]], newInterval = [2,5]
输出: [[1,5],[6,9]]
示例 2:

>输入: intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
输出: [[1,2],[3,10],[12,16]]
解释: 这是因为新的区间 [4,8] 与 [3,5],[6,7],[8,10] 重叠。

来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/insert-interval
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。


**思路：** 大体和前一题（56）的方法一样，这次不用排序了，可以直接遍历或二分将新区间插入到相应有序位置，之后直接用区间合并的方法遍历即可，区间合并的方法即，构造一个ans数组，对于每个收入的区间，判断其边界是否有交叉，若有交叉则修改ans中当前遍历区间的右边界，否则直接append到ans中。
```py
class Solution:
    def insert(self, intervals: List[List[int]], newInterval: List[int]) -> List[List[int]]:
        if intervals==[]:
            return [newInterval]
        if newInterval[0]<=intervals[0][0]:
            intervals.insert(0,newInterval)
        elif newInterval[0]>intervals[-1][0]:
            intervals.append(newInterval)
        else:
            for i in range(len(intervals)):
                if intervals[i+1][0]>=newInterval[0]>=intervals[i][0]:
                    intervals.insert(i+1,newInterval)
                    break
        print(intervals)
        ans = []
        for i in intervals:
            if ans==[]:
                ans.append(i)
            else:
                start=ans[-1][0]
                end=ans[-1][1]
                if i[0]>end:
                    ans.append(i)
                else:
                    ans[-1][1]=max(end,i[1])
        return ans
```
