# Data structure 学习笔记
>    本文来源于网络，可能存在错漏之处，仅供参考。

## 递归
>    [递归：如何用三行代码找到 “最终推荐人”？(王争)](https://www.jianshu.com/p/32eacb1a8337?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

### 1. 递归需要满足的三个条件
+ 一个问题的解可以分解为几个子问题的解
+ 这个问题与分解之后的子问题，除了数据规模不同，求解思路完全一样
+ 存在递归终止条件  

### 2. 如何编写递归代码
+ 写出递推公式
+ 找到终止条件
- 假设这里有 n 个台阶，每次你可以跨 1 个台阶或者 2 个台阶，请问走这 n 个台阶有多少
种走法？如果有 7 个台阶，你可以2，2，2，1这样上去，也可以1，2，1，1，2这样子上
去，走法有很多，那么如何用编程求得总共有多少种走法呢？
```bash
//递推公式
f(n) = f(n-1) + f(n-2)
//下面是边界情况
//还剩一个台阶有 1 种情况
f(1) = 1;
//还剩两个台阶有 2 种情况，1、1或者 2
f(2) = 2;
```
```bash
int f(int n){
    if(n==1) return 1;
    if(n==2) return 2;
    return f(n-1) + f(n-2);
}
```
- 写递归代码的关键就是找到如何将大问题分解为小问题的规律，并且基于此写出递推公式， 然后再推敲终止条件，最后将递推公式和终止条件翻译成代码。

### 3. 思维误区
- 想清楚整个递和归的过程的做法是一个思维误区。
- 编写递归代码的关键是，只要遇到递归，我们就把它抽象成一个递推公式，不用想一层层的调用关系，不要试图用人脑去分解递归的每个步骤。

### 4. 递归代码要警惕堆栈溢出
- 在学习栈的时候，我们了解到函数调用会使用栈来保存临时变量，每调用一个函数，都会将临时变量封装为栈帧压入内存栈，等函数执行完成返回后，才出栈。那么当调用层次很深的时候，一直压入栈，就会有堆栈溢出的风险。
- 我们可以在代码中限制递归调用的最大深度（就是多少层）来解决这个问题。递归调用超过一定深度（比如1000）之后，我们就不继续往下再递归了，直接返回报错。
```bash
//电影院例子新版
//全局变量
int depth = 0;

int f(int n){
    ++depth;
    if(depth > 1000) throw new XXXException();
    
    if(n == 1) return 1;
    return f(n - 1) + 1;
}
```

### 5. 递归代码要警惕重复计算
- 为了避免重复计算，我们可以通过一个数据结构（比如散列表）来保存已经求解过的 f(k)。当递归调用到 f(k) 时，先看下是否已经求解过了，如果是，则直接从散列表中取值返回，不需要重复计算，这样就能避免重复计算的问题了。
```bash
public int f(int n) {
  if (n == 1) return 1;
  if (n == 2) return 2;
  
  // hasSolvedList 可以理解成一个 Map，key 是 n，value 是 f(n)
  if (hasSolvedList.containsKey(n)) {
    return hasSovledList.get(n);
  }
  
  int ret = f(n-1) + f(n-2);
  hasSovledList.put(n, ret);
  return ret;
}
```

### 6. 将递归代码改为非递归代码
- 递归的表达度高、简洁但是空间复杂度高。
```bash
int f(int n){
    int result = 1;
    for(int i = 2;i <= n;i++){
        result = result +1;
    }
    
    return result;
}
```
```bash
int f(int n) {
  if (n == 1) return 1;
  if (n == 2) return 2;
  
  int ret = 0;
  int pre = 2;
  int prepre = 1;
  for (int i = 3; i <= n; ++i) {
    ret = pre + prepre;
    prepre = pre;
    pre = ret;
  }
  return ret;
}
```

### 7.最终推荐人
```bash
long findRootReferrerId(long actorId) {
  Long referrerId = select referrer_id from [table] where actor_id = actorId;
  if (referrerId == null) return actorId;
  return findRootReferrerId(referrerId);
}
```

## 数组的全排列
>    [参考](https://blog.csdn.net/k346k346/article/details/51154786)  
### 递归实现
- n!表示n个元素全排列的个数。
- 需要消耗大量的栈空间，如果函数栈空间不够，那么就运行不下去了，而且函数调用开销也比较大。
- 