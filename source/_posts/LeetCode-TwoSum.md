---
title: '[LeetCode]TwoSum'
date: 2017-07-27 14:56:46
tags:
---

作者：disappearedgod
[文章出处](http://blog.csdn.net/disappearedgod/article/details/23634067)
时间：2014-4-13

[题目](https://leetcode.com/problems/two-sum/)
```
Two Sum
Given an array of integers, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution.

Input: numbers={2, 7, 11, 15}, target=9
Output: index1=1, index2=2
```
#思路：

1.克隆排序

2.双指针查找

3.对应返回位置
#解题
C++ 版本
```cpp
class Solution {
public:
    vector<int> twoSum(vector<int> &numbers, int target) {
        vector<int> num = numbers;
        std::sort(num.begin(),num.end());

        int length = num.size();
        int left = 0;
        int right = length -1;
        int sum = 0;

        vector<int> index;

        while(left<right){
            sum = num[left]+num[right];
            if(sum > target)
              --right;
            else if(sum < target)
              ++left;
            else{
                for(int i = 0; i < length ; ++i){
                    if(index.size()==2)
                      break;
                    if(numbers[i]==num[left])
                      index.push_back(i+1);
                    else if(numbers[i]==num[right])
                      index.push_back(i+1);
                }
                return index;
            }
        }
    }
};
```
java 版本-有问题
```java
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
      int[] num = numbers.clone();
      Arrays.sort(num);//unstability sort

      int length = numbers.length;
      int left = 0;
      int right = length -1;
      int sum = 0;
      int[] index = new int[2];

      while(left<right){
          sum= num[left] + num[right];
          if(sum<target)
            ++left;
          else if(sum > target)
            --right;
          else{
              int count=0;
              for(int i = 0; i < length; i++){
                  if(count==2)
                    break;
                  if(numbers[i]==num[left]){
                    index[0]=i+1;
                    ++count;
                    continue;
                  }
                  else if(numbers[i]==num[right]){
                     index[1]=i+1;
                     ++count;
                     continue;
                  }
              }
              break;
          }
      }
      return index;
    }
}
```

问题在于
  if(numbers[i]==num[left]){
如果两个因子相同，则只改变了left，而没有改变right，所以要用容器
```cpp
public class Solution {
    public int[] twoSum(int[] numbers, int target) {
      int[] num = numbers.clone();
      Arrays.sort(num);//unstability sort
      int length = numbers.length;
      int left = 0;
      int right = length -1;
      int sum = 0;
      int[] index = new int[2];
      ArrayList<Integer> list = new ArrayList<Integer>();// not int
      while(left<right){
          sum= num[left] + num[right];
          if(sum<target)
            ++left;
          else if(sum > target)
            --right;
          else{
              list.clear();
              for(int i = 0; i < length; i++){
                  if(list.size()==2)
                    break;
                  if(numbers[i]==num[left]){
                    list.add(i+1);
                  }
                  else if(numbers[i]==num[right]){
                     list.add(i+1);
                  }

              }
              break;
          }
      }
      int i = 0;
      for(Iterator it = list.iterator();it.hasNext();++i){
        index[i]=(int)it.next();
      }
      return index;
    }
}
```

借鉴：http://blog.csdn.net/lilong_dream/article/details/19298357


[返回]