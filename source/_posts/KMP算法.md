---
title: KMP算法
date: 2022-05-17 17:11:37
categories: [算法]
tags:
- KMP
---

# 旋转字符串

`leetcode`上的题目：[旋转字符串](https://leetcode.cn/problems/rotate-string/)

在字符串后加上该字符串，得到的字符串中包含原字符串的所有旋转字符串

使用`indexOf`：

```js
var rotateString = function(s, goal) {
  if(s.length !== goal.length) return false
  s += s
  return s.indexOf(goal) !== -1
};
```

自己用KMP算法实现`indexOf`：

# 检查子树

`leetcode`上的题目：[检查子树](https://leetcode.cn/problems/check-subtree-lcci/)

1. 暴力算法：

   时间复杂度：O(n*m)

   ```js
   var checkSubTree = function(t1, t2) {
     if(!t2) return true
     if(!t1) return false
     if(equal(t1,t2)){
       return true
     }else{
       return checkSubTree(t1.left,t2) || checkSubTree(t1.right,t2)
     }
   };
   
   function equal(t1,t2){
     if(!t1 && t2) return false
     if(t1 && !t2) return false
     if(!t1 && !t2) return true
     if(t1.val === t2.val){
       return equal(t1.left,t2.left) && equal(t1.right,t2.right)
     }else {
       return false
     }
   }
   ```

2. KMP算法

   时间复杂度

