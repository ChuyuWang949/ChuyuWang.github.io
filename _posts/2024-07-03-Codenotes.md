---
layout: post
title: "数据结构刷题笔记"
date:   2024-7-3
tags: [数据结构刷题]
comments: true
author: cybro
---

## STL库

https://wyqz.top/p/870124582.html#toc-heading-60%E5%8F%AF%E4%BB%A5%E7%9C%8B%E7%9C%8B%E8%BF%99%E4%B8%AA%E5%AD%A6stl

## 标准化输入输出

**Cin的相关小知识：**

https://blog.csdn.net/u011630575/article/details/79721024

**加入输入是一行 没告诉你有多少个数字可以用**

<!-- more -->

```cpp
while(cin>>a[i++]);
```

```cpp
printf("  %c  \n",c);
printf(" %c%c%c \n",c,c,c);
printf("$c%c%c%c%c\n",c,c,c,c,c);
```

本文将为你介绍 **格式化输入输出函数**：scanf 和 printf。

### 为什么

scanf/printf 相比 cin/cout 快得多，应对多数题目绰绰有余。

![image-20240322165536927](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240322165536927.png)

```cpp
#include <cstdio>

using namespace std;
char a, b, c, d;
int main(){
	scanf("%c%c%c.%c", &a, &b, &c, &d);
	printf("%c.%c%c%c", d, c, b, a);
	return 0;
} // 来自洛谷数字反转https://www.luogu.com.cn/problem/solution/P5705

printf("%.3f\n", a/b); // 保留三位小数
cout << fixed << setprecision(6) << 500.0 / 3; // 保留6位小数，去掉fixed就是六位有效数字
printf("%d", int(a * 0.2 + b * 0.3 + c * 0.5));//类型需要对上
```



## 1.二分查找

当我们将区间[l, r]划分成[l, mid]和[mid + 1, r]时，其更新操作是r = mid或者l = mid + 1;，计算mid时不需要加1。

**找到满足check区间的左边界** **实际上cpp中已经集成了lower_bound这个函数**

```cpp
int bsearch1(int l, int r){
    while(l < r){
        int mid = l + r >> 1;
        if(check(mid)) r = mid;
      	else l = mid + 1;
    }
    return l;
}
```

当我们将区间[l, r]划分成[l, mid - 1]和[mid, r]时，其更新操作是r = mid - 1或者l = mid;，此时为了防止死循环，计算mid时需要加1。

**找到满足check区间的右边界** **upper_bound这个函数**,实际上是右边界的下一个数，实际操作时需要注意r-1

```cpp
int bsearch2(int l, int r){
	while(l < r){
		int mid = l + r + 1 >> 1;
		if(check(mid)) l = mid；
		else r = mid - 1;
	}
	return l;
}
```

## ![image-20240321205814017](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240321205814017.png)

![image-20240327205720149](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240327205720149.png)

## 2.集合求和

来自洛谷P2415，题目描述为：

![image-20240326223134476](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240326223134476.png)

**题解：**

![image-20240326223103922](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240326223103922.png)

故代码为如下：

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    long long tmp,num=0,sum=0;
    while(cin>>tmp){sum+=tmp;num++;}//读入集合元素个数num和元素和sum
    cout<<(long long)(sum*pow(2,num-1));//必须显式地转换为long long输出
    return 0;
}
```

**严谨的数学证明如下：**

![image-20240326223312527](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240326223312527.png)

## 3.高精度

### 3.1高精度加法

核心思路，每一位计算，每一位上的计算结果要么是 一个digit 要么是 两个digit，所有采取 /10 %10等操作将其取出，再作用于下一位

代码如下：值得注意的是，将需要计算的数字倒序遍历会方便很多，因为我们是从低位开始计算的

```cpp
#include <bits/stdc++.h>    
using namespace std;

int main(){
    string a, b;
    cin >> a >> b;
    int c = 0;//这个c值得一提，他记录了是否有进位，并且这个进位是多少
    int lena = a.length();int lenb = b.length();
    vector<int> ans;
    if(lena < lenb){
        string temp;
        temp = a;
        a = b;
        b = temp;
    }
    
    int len = a.length();
    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());

    for(int i = 0; i < len; i ++ ){
        c += a[i] - '0';
        if(i < b.length()) c += b[i] - '0';
        ans.push_back(c % 10);
        c /= 10;
    }
    if(c) ans.push_back(c);//最后别忘了c还有没有

    for(int i = ans.size() - 1; i >= 0; i--){
        cout << ans[i];
    }
    return 0;
}
```

### 3.2高精度减法

思路是一样的依然是采用竖式减法，按位相减，不够的就向高位借一个，将大的数字放在前面，倒序是为了更好的计算从低到高；

注意考虑符号问题，为了方便采取大减小，小减大实际上只差了一个负号

代码如下：

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(){
    string a, b;
    cin >> a >> b;
    int flag = 0;
    if(a.length() < b.length() || (a.length() == b.length() && a < b)){
        swap(a, b);
        flag = 1;
    }
    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());
    int c = 0;
    vector<int> ans;
    for(int i = 0; i < a.size(); i ++ ){
        c = a[i] - '0' - c;
        if(i < b.size()) c -= b[i] - '0';
        ans.push_back((c + 10) % 10);//将负数映射会0-9之间
        if(c < 0) c = 1;//如果为负数，则下一位数需要多减一个1
        else c = 0;//要不然则不用减，归0
    }
    if(flag == 1) cout << '-';
    while(ans.size() > 1 && ans.back() == 0) ans.pop_back();//高精度减法的关键之处在于去除先导0，避免在输出的时候输出多个0
    for(int i = ans.size() - 1; i >= 0; i--) cout << ans[i];
}
```

### 3.3高精度乘法

其实有了上面的思路之后，高精度乘法就变得比较简单了，关键在于提炼出公式C[i + j - 1] = a[i] * b[j]（爱因斯坦表达公式）

```cpp
#include <bits/stdc++.h>
using namespace std;
// mutiply
int main(){
    string a, b;
    cin >> a >> b;
    reverse(a.begin(), a.end());
    reverse(b.begin(), b.end());
    int ans[4005] = {0};

    int len = a.length() + b.length();

    for(int i = 0; i < a.length(); i ++ )
        for(int j = 0; j < b.length(); j ++ ){
            ans[i + j] += (a[i] - '0') * (b[j] - '0');
            ans[i + j + 1] += ans[i + j ] / 10;
            ans[i + j] %= 10;
        }
    while(len > 0 && ans[len] == 0) len -- ;
    for(int i = len; i >= 0; i -- ) cout << ans[i];
}

/// 单循环写法来自acwing，此模板为高精度乘低精度
vector<int> ans;
int t = 0;
for(int i = 0; i < A.size() || t; i ++ ){//这里之所以多了一个t的判断是为了省去后续处理余下来的t，合并到一起了更简洁
//要不然就会像加法中一样单独再开一个判断来计算
    if(t < A.size()) t += A[i] * b;
    ans.push_back(t % 10);
    t /= 10;
}
while(ans.size() > 1 && ans.back() == 0) ans.pop_back();
```

## 4.暴力枚举

暴力枚举一直是我的弱项，关键的能力在于如何从问题描述中抽象出来程序逻辑，这实际上是编程的核心能力，至于其他的算法都无非是在优化计算时间空间罢了

### 4.1深度优先搜索

实际上就是递归判断，关键在于，变动的参数是什么，如何遍历，如何确定起始和终止的条件

**P2089洛谷，分烧烤调料，十个数字1~3，给定十个数字之和，给出所有的可能方案，按字典序排列**

非常标准的深搜题，关键在于起止

```cpp
#include <bits/stdc++.h>
using namespace std;
vector<vector<int>> ans;
vector<int> tmp(10);
//
void dfs(int l, int e){
    if(l == 10 && e == 0){//到10就停，e==0说明满足条件，可以作为一种可能
        ans.push_back(tmp);
        return;
    }
    if(l == 10 && e != 0){//到10停，但不满足条件，直接返回
        return;
    }
    for(int i = 0; i <= 2; i ++ ){
        if(e - i >= 0){
            tmp[l] = i + 1;
            dfs(l + 1, e - i);
        }
        else return;//e已经不满足条件，没必要继续搜索直接返回
    }
}
int main(){
    int n;
    cin >> n;
    if(n < 10 || n > 30){
        cout << 0;
        return 0;
    }
    dfs(0, n - 10);
    cout << ans.size() << endl;
    for(int i = 0; i < ans.size(); i ++ ){
        for(int j = 0; j < 10; j ++ ){
            cout << ans[i][j] << " ";
        }
        cout << endl;
    }
}
```

### 4.2矩形中正方形和长方形的个数

给定一个nxm的网格，问其中能有多少个正方形多少个长方形，乍一看，会感觉很复杂很混乱，但其实问题的关键在于如何确定好讨论目标，且这个讨论目标是可迭代的条件统一的。

最终选定的讨论思路是以（i，j）为左顶点的正方形和长方形，可以很简单的发现:

```cpp
int main(){
    int n, m;
    cin >> n >> m;
    LL a = 0, b = 0;
    for(int i = 0; i < n; i ++ )
        for(int j = 0; j < m; j ++ ){
            a += min(n - i, m - j);//正方形的个数就等于你画一个对角线，到边界的个数
            b += (n - i) * (m - j) - min(n - i, m - j);//很容易知道矩形的个数，就是边长的所有可能相互乘，那矩形的个数减去长方形的个数即可
        }
    cout << a << " " << b;
}
```

小学数学知识：正方形的个数：n\*m + (n-1)*(m-1) +(n-2)\*(m-2)+.....

矩形个数：（1+2+3+4+······+n）*(1+2+3+4+·····+m)

## 5.动态规划（dynamic programing）

动态规划的关键在于如何定义 **状态函数** 和 **状态转移函数** 起始状态是什么 如何转移到下一个状态是我们所关心的

## Trick

### 位运算的妙用

#### 判断奇偶数

![image-20240321202805146](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240321202805146.png)

- 在 C++ 中, `&` 是按位与运算符。当对一个整数与 `0x1` (二进制形式为 `00000001`) 进行按位与运算时, 结果将只保留该整数的最低有效位。
- 如果 `array[j]` 是奇数, 则其最低有效位为 1, 与 `0x1` 进行按位与运算后结果为 1 (即 true)。
- 如果 `array[j]` 是偶数, 则其最低有效位为 0, 与 `0x1` 进行按位与运算后结果为 0 (即 false)。

这种利用按位运算来判断一个数是奇数还是偶数的方法是一种常见的编程技巧, 它比直接使用模运算 `%` 更加高效, 特别是在需要频繁判断奇偶的场景下

#### 0 1转换

```cpp
1 ^ 1 = 0; 0 ^ 1 = 1;//按位异或，可以实现0 1之间转换
```



### 反转数组 字符串

![image-20240321205722974](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240321205722974.png)

1. 为了获得与链表节点相反的顺序,代码利用了 `vector` 类提供的反向迭代器 `rbegin()` 和 `rend()`。
   - `rbegin()` 返回一个指向 `res` 最后一个元素的反向迭代器。
   - `rend()` 返回一个指向 `res` 第一个元素之前的反向迭代器。

2. `vector<int>(res.rbegin(), res.rend())` 会构造一个新的向量,其元素顺序与 `res` 相反。

   除了数组，字符串也有此种妙用

   ![image-20240326210916707](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240326210916707.png)

   ```cpp
   cin>>a;               //输入
   reverse(a.begin(),a.end());  //反转
   cout<<a;              //输出
   ```

   

### 获取排列 组合

```cpp
class Solution {
public:
    vector<vector<int>> permutation(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> ret;
        do{
            ret.push_back(nums);
        }while(next_permutation(nums.begin(),nums.end()));
        return ret;
    }
};
```

next_permutation(nums.begin(),nums.end())根据从小到的的顺序以此排列下一个数

**深搜写法：**思路为每一位检索，关键点在于会有重复数字，因此在遍历时，限定，**相同的数字只能出现在后面，因为涉及重复数字，进行排序可以降低复杂度。**

```cpp
class Solution {
public:
    vector<int> path;
    vector<vector<int>> ans;
    int state = 0;
    int start = 0;
    vector<vector<int>> permutation(vector<int>& nums) {
        path.resize(nums.size());
        sort(nums.begin(), nums.end());
        dfs(nums, 0, 0, 0);
        return ans;
    }
    
    void dfs(vector<int>& nums, int u, int start, int state){
        if(u == nums.size()){
            ans.push_back(path);
            return;
        }
        if(!u || nums[u - 1] != nums[u]) start = 0;
        for(int i = start; i < nums.size(); i++)
            if(!(state >> i & 1)){
                path[i] = nums[u];
                dfs(nums, u + 1, i + 1, state + (1 << i));
            }
    }
};
```

**获取组合数，深搜**

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> res;
vector<int> temp;

void dfs(int n, int k, int start){//start是因为避免重复，由于组合数顺序不重要，因此只需要每个数都只考虑自己后面的数字就行
    if(k == 0){
        res.push_back(temp);
        return;
    }
    for(int i = start; i <= n; i ++ ){
        temp.push_back(i);
        dfs(n, k - 1, i + 1);
        temp.pop_back();
    }
}

int main(){
    int n, k;
    cin >> n >> k;
    dfs(n, k, 1);
    for(int i = 0; i < res.size(); i ++ ){
        for(int j = 0; j < k; j ++ )
            cout << setw(3) << res[i][j];
        cout << endl;
    }     
}
```

实际上所有的选取几个元素的都可以这么写，只需要在这个模板之上加入额外的判断条件即可。

**获取排列数，无重复：**

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> res;
vector<int> temp;
bool visited[10];//加入这个是因为，不像组合数顺序无关，排列的话顺序是有关的，每一位数字的选择都要从头开始遍历，又不能重复，因此进行判断，选择过的就跳过

void dfs(int n){
    if(temp.size() == n){
        res.push_back(temp);
        return;
    }
    for(int i = 1; i <= n; i ++ ){
        if(!visited[i]){
            temp.push_back(i);
            visited[i] = true;
            dfs(n);
            visited[i] = false;//还原
            temp.pop_back();//还原
        }
    }
}

int main(){
    int n;
    cin >> n;
    dfs(n);
    for(int i = 0; i < res.size(); i ++ ){
        for(int j = 0; j < n; j ++ )
            cout << setw(5) << res[i][j];
        cout << endl;
    }
}
```



### 获得每位数及判断是否重复

```cpp
a/100+a/10%10+a%10
```

数学原理，2个集合内所有数相加相乘结果一样，2个集合的内容一样，可以用于判断集合内是否有重复数字，这个办法其实有局限，对于比较弱的数据可以通过，但需要保证他是个集合。

```cpp
if((a/100+a/10%10+a%10+b/100+b/10%10+b%10+c/100+c/10%10+c%10==1+2+3+4+5+6+7+8+9)&&((a/100)*(a/10%10)*(a%10)*(b/100)*(b/10%10)*(b%10)*(c/100)*(c/10%10)*(c%10)==(1)*(2)*(3)*(4)*(5)*(6)*(7)*(8)*(9)))
                    printf("%d %d %d\n",a,b,c);
```

来自洛谷https://luogu.com.cn/problem/solution/P1008三连击

去重一个数组内的元素，利用cpp自带的unordered_set

```cpp
void removeDuplicates(int arr[], int& size) {
    std::unordered_set<int> unique(arr, arr + size); // 创建一个无序集合，直接将数组元素插入
    std::vector<int> temp(unique.begin(), unique.end()); // 将无序集合转换为 vector

    // 将 vector 复制回原数组
    for (int i = 0; i < temp.size(); i++) {
        arr[i] = temp[i];
    }

    size = temp.size(); // 更新数组大小
}
```

一个二维数组，对里面的每一个一维数组进行去重

```cpp
std::vector<std::vector<int>> removeDuplicates(std::vector<std::vector<int>>& matrix) {
    // 创建一个set用于存储排序后的一维数组
    std::set<std::vector<int>> uniqueRows;

    // 遍历二维数组
    for (const auto& row : matrix) {
        // 对一维数组进行排序
        std::vector<int> sortedRow(row);
        std::sort(sortedRow.begin(), sortedRow.end());

        // 将排序后的一维数组插入到set中
        uniqueRows.insert(sortedRow);
    }

    // 将set转换为vector
    std::vector<std::vector<int>> uniqueMatrix(uniqueRows.begin(), uniqueRows.end());
    return uniqueMatrix;
}
```



### 大小字母转换

**首先我们要知道，C++字符的所有转换形式都是依照ASCII码来的。小写字母=大写字母+32，则大写字母=小写字母-32**

```cpp
int main(){
	char a;       //创建字符型变量
	cin>>a;       //小写输入
	cout<<char(a-32);//注意要用char进行强制转换，否则输出的就是整数类型的了
   return 0;
}
```

```cpp
cout<<(char)(a-('a'-'A')); //当你不知道对应的ASCII码时可以采用这种方式
toupper(a) //或者还有toupper（）函数
```

```cpp
#include<iostream>
#include<algorithm>
using namespace std;
int a[3];
char A,B,C;
int main()
{
    cin>>a[0]>>a[1]>>a[2];
    cin>>A>>B>>C;
    sort(a,a+3);//懒懒_(:з」∠)_排序法
    cout<<a[A-'A']<<" "<<a[B-'A']<<" "<<a[C-'A'];//字母是大写，减去‘A’后得到0（A）,1（B）,2（C）。
    return 0;
}// 来自洛谷P4414， 字符串索引数组的方法除了map可以简单的利用ascii码
```

当你有一个区间类似一个环，你希望所有数都在1-10，例如-2变成8可以使用如下代码

```cpp
(t + 10) % 10
```

### 三角形面积

<img src="C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20240322170758141.png" alt="image-20240322170758141" style="zoom:150%;" />

### 精度统一

 ```c++
         const double PI = 3.141593;
         cout << PI * 10 << endl;
         cout << PI * 25 << endl;
         cout << 4.0/3 * PI * 125 << endl;// 注意这个4.0， 将其转换为double
 ```

### 如何在整数型稳定的向上取整?

```cpp
(A+B-1)/B 
```

int类型的数操作后[向上取整](https://so.csdn.net/so/search?q=向上取整&spm=1001.2101.3001.7020)，特别是两个int相除然后取整，需要使用ceil（c++）函数，但是这也有个问题，就是你需要把int转为浮点型才行，因为两个int相除，结果还是int，始终是向下取整。能不能不转就可以实现呢？肯定是可以的，下面的公式就是向上取整。

### 求取最大公约数

辗转相除法

```cpp
int gcd(int a,int b){ // a > b
    if (b==0) return a; 
    return gcd(b, a%b); 
}
```

或者可以直接调用

```cpp
__gcd(a, b); //自带的函数
```

### 判断素数

```cpp
bool isPrime(int n){
    if(n <= 3){
        return n > 1;
    }
    for(int i = 2; i <= sqrt(n); i++){
        if(n % i == 0){
            return false;
        }
    }
    return true;
}
```

### 字符串处理示范题

**来自洛谷P1957**

在此隆重推出

```cpp
#include <cctype>
```

这是一个拥有许多字符串处理函数声明的头文件，这些函数可以用来对单独字符串进行分类和转换；

例如：

isalpha():检查这个字符是否为字母，真返回1，假返回0；

isalnum()：检查这个字符是否为字母或数字数字，同上；

isdigit():检查这个字符是否为十进制数字，同上；

islower():检查这个字符是否为小写字母，同上；

**sprintf()**

或许看起来很像printf？实际上，他只比printf多一个参数，一个字符串！

没错，他就是一个将数据输入字符串的函数！

那长度直接输出ans的长度就可以啦。

```cpp
#include <cctype>
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
int n,x,y;
char ss[20],ans[100],k;
int main() {
	scanf("%d",&n);
	for(int i=1; i<=n; i++) {
		scanf("%s",&ss);
		if(isalpha(ss[0])) {
			k=ss[0];
			scanf("%d%d",&x,&y);
		} else {
			x=atoi(ss);
			scanf("%d",&y);
		}
		if(k=='a')
			sprintf(ans,"%d+%d=%d",x,y,x+y);
		else if(k=='b')
			sprintf(ans,"%d-%d=%d",x,y,x-y);
		else
			sprintf(ans,"%d*%d=%d",x,y,x*y);
		printf("%s\n%d\n",ans,strlen(ans));
	}
	return 0;//完美结束！
}
```

当你需要对一行字符输入进行操作的时候，getline(*s*)的东东。s☞字符串的名称

他有什么作用呢？

> 将一行字符从文件或屏幕中读入，可以读空格，遇到换行跳出。

strlen(*s*)这里的n指字符串的名称。

我们在代码中进行如下操作

```cpp
string s;
getline(s);
int n=strlen(s);
```

判断字符类型的方法

```cpp
if(s[i]>='A'&&s[i]<='Z')
if(s[i]>='a'&&s[i]<='z')
if(s[i]>='0'&&s[i]<='9')
```

### 多重条件判断时的代码简化

```cpp
#include<bits/stdc++.h>
using namespace std;

int main() {
    int x;
    cin >> x;

    bool isEven = (x % 2 == 0);
    bool inRange = (x > 4 && x <= 12);
    cout << (isEven && inRange) << " ";
    cout << (isEven || inRange) << " ";
    cout << (isEven != inRange) << " ";
    cout << (!isEven && !inRange) << endl;
    return 0;
}
```

## 做题记录

### 洛谷P2670扫雷——边界条件

现在给出 n行 m 列的雷区中的地雷分布，要求计算出每个非地雷格周围的地雷格数。

注：一个格子的周围格子包括其上、下、左、右、左上、右上、左下、右下八个方向上与之直接相邻的格子。

**输入格式**

第一行是用一个空格隔开的两个整数 n 和 m，分别表示雷区的行数和列数。

接下来n行，每行m个字符，描述了雷区中的地雷分布情况。字符 ** 表示相应格子是地雷格，字符 ?? 表示相应格子是非地雷格。相邻字符之间无分隔符。

**输出格式**

输出文件包含 n 行，每行m个字符，描述整个雷区。用 ** 表示地雷格，用周围的地雷个数表示非地雷格。相邻字符之间无分隔符。



**问题：在计算的时候边界条件难以考虑，需要加很多的判断，左右边界会导致程序很复杂，各种情况难以讨论清楚。**

**解决思路：就像python中的padding一样，当你的图像卷积无法完全整除，你又不想丧失信息的时候就可以考虑采用padding，在cpp中具体为，你可以把数组的大小设置的更大一些，这样就可以避免讨论一些复杂的边界条件。**

除此之外，计算的顺序也是需要考虑的，是先遍历雷*，遇到直接给周围的格子加上，还是给\*的格子赋值为1，再在输出的时候进行统计，此处是有两种思路，即是先分摊后直接输出答案，还是先赋值后聚合再输出答案

### 洛谷P1328石头剪刀布——打表与循环

**绘制得分表的妙用：我的代码如下采用大量的if判断来进行，暴力的获得所有的输入，再一一对比，没能很好的利用到周期的性质，以及石头剪刀布之间的循环克制关系。**

```cpp
#include <bits/stdc++.h>
using namespace std;

int main(){
    vector<int> A, B;
    int N, Na, Nb;
    cin >> N >> Na >> Nb;
    for(int i = 0; i < Na; i ++ ) {
        int temp;
        cin >> temp;
        A.push_back(temp);
    }
    for(int i = 0; i < Nb; i ++ ) {
        int temp;
        cin >> temp;
        B.push_back(temp);
    }
    int scoreA = 0, scoreB = 0;
    vector<int> a, b;
    int ta = N / Na, tb = N / Nb;
    while(ta){
        a.insert(a.end(), A.begin(), A.end());
        ta -- ;
    }
    while(tb){
        b.insert(b.end(), B.begin(), B.end());
        tb -- ;
    }
    a.insert(a.end(), A.begin(), A.begin() + N % Na);
    b.insert(b.end(), B.begin(), B.begin() + N % Nb);
    for(int i = 0; i < N; i ++ ){
        if(a[i] == 0 ){
            if(b[i] == 2 || b[i] == 3) scoreA ++ ;
            if(b[i] == 1 || b[i] == 4) scoreB ++ ;
        }
        if(a[i] == 1 ){
            if(b[i] == 0 || b[i] == 3) scoreA ++ ;
            if(b[i] == 2 || b[i] == 4) scoreB ++ ;
        }
        if(a[i] == 2 ){
            if(b[i] == 1 || b[i] == 4) scoreA ++ ;
            if(b[i] == 0 || b[i] == 3) scoreB ++ ;
        }
        if(a[i] == 3 ){
            if(b[i] == 2 || b[i] == 4) scoreA ++ ;
            if(b[i] == 1 || b[i] == 0) scoreB ++ ;
        }
        if(a[i] == 4 ){
            if(b[i] == 0 || b[i] == 1) scoreA ++ ;
            if(b[i] == 2 || b[i] == 3) scoreB ++ ;
        }
    }
    cout << scoreA << " " << scoreB;
}
```

示例代码：采用的分表，并且利用周期的性质进行访问，更简洁高效

```cpp
#include <bits/stdc++.h>
using namespace std;
const int MAXN = 200 + 10;
int n, na, nb, a[MAXN], b[MAXN], cnta, cntb;
int vs[5][5] = {{0,0,1,1,0},{1,0,0,1,0},{0,1,0,0,1},{0,0,1,0,1},{1,1,0,0,0}}; //得分表的处理 
int main()
{
    cin >> n >> na >> nb;
    for(int i = 0; i < na; i++) cin >> a[i];
    for(int i = 0; i < nb; i++) cin >> b[i];
    for(int i = 0; i < n; i++)
    {
        cnta += vs[a[i % na]][b[i % nb]]; //周期循环 
        cntb += vs[b[i % nb]][a[i % na]];
    }
    cout << cnta << " " << cntb << endl;
    return 0;
}
```

### 洛谷P1518抓牛——判断死循环

此题的模拟不是重点，关键在于如何确定是否会陷入死循环，由于行为模式是完全固定的，因此可以通过产生的特征值的方式来确定，是否会出现重复情况，若出现重复情况则证明会陷入死循环。

```cpp
bool flag[320005];
state = xf + yf * 10 + xc * 100 + yc * 1000 + df * 10000 + dc * 40000;
if(flag[state]){
    cout << 0;
    return 0;
}
flag[state] = 1; 
```

