# 本周计划

1.并发与多线程

2.Qt窗口的调用

3.battery demo程序

4.每日一道力扣

# 周一

## 1 多线程

### 1.1 相关基本概念

​	1.线程是进程的实体程序中至少有一个进程，一个进程至少有一个线程

​	2.并行，多个任务同时执行；并发，多任务在同一时间段内交替执行，看起来同时执行，实际上是交替的

​	3.死锁条件：

- 一个资源只能一个进程使用，另一个进程只能等待
- 只能自己主动释放资源
- 已有一个资源a，又去请求另一个资源b，但b已被占用，但线程对占用的资源a保持不放
- 循环等待，{p0,p1,p2...pn}资源循环等待链，p0等待的资源被p1占用，p1等待的资源被p2占用······，pn等待的资源被p0占用

   四个条件需要同时成立，才会死锁

​	4.活锁，与死锁表现相同，线程没有进展，但线程一直在尝试取获取需要的锁，一直在做无用功

## 2 力扣

### 599 寻找相同字符串的最小下标

思路1：遍历字符串数组，通过两重循环找到相同字符串的下标，将两个下标放入到对组数组中，同时计算出最小的下标和；然后遍历对组数组，如果first+second等于最小下标和，就将下标对应的字符串放入结果数组中。

debug流程：将最小的下标和设置为INT_MAX，并且将判断最小和与结果存入一起进行。可能导致第一个结果为5，但它小于INT_MAX，就将结果存入数组，后面结果为3，仍将结果存入。解决方法，将判断最小与结果存入分开执行。

```cpp
#include <iostream>
#include <string>
#include <vector>
#include <limits.h>

using namespace std;

class Solution;

void test01();
void print_string(const vector<string>& v);

class Solution
{
public:
    vector<string> findRestaurant(vector<string>& list1, vector<string>& list2)
    {
        int len1 = (signed)list1.size();
        int len2 = (signed)list2.size();
        int min_index = INT_MAX;
        vector<pair<int, int>> same_restaurant;
        vector<string> res;

        for (int i = 0; i < len1; i++)
        {
            for (int j = 0; j < len2; j++)
            {
                if (list1[i].compare(list2[j]) == 0)
                {
                    //两个字符串相同，就将下标存入数组
                    same_restaurant.push_back(pair<int, int>(i, j));
                    //寻找最小下标和
                    min_index = i + j < min_index ? i + j : min_index;
                }
            }
        }
        //对组的读取输出
        cout << same_restaurant[0].first << " " << same_restaurant[0].second << endl;
        cout << same_restaurant[1].first << " " << same_restaurant[1].second << endl;
        for (int i = 0; i < (signed)same_restaurant.size(); i++)
        {
            if (same_restaurant[i].first + same_restaurant[i].second == min_index)
            {
                //下标和等于最小和，就将其放入结果
                res.push_back(list1[same_restaurant[i].first]);
            }
        }
        return res;
    }
};

void test01()
{
    Solution s;
    vector<string> list1 = {"Shogun","Piatti","Tapioca Express","Burger King","KFC"};
    vector<string> list2 = {"Piatti","The Grill at Torrey Pines","Hungry Hunter Steakhouse","Shogun"};
    vector<string> res = s.findRestaurant(list1, list2);
    print_string(res);
}

void print_string(const vector<string>& v)
{
    for (vector<string>::const_iterator it = v.begin(); it != v.end(); it++)
    {
        cout << *it << " ";
    }
    cout << endl;
}

int main()
{
    test01();
    cout << "Hello world!" << endl;
    return 0;
}
```

思路2：遍历数组，找到最小下标，放入结果数组中，如果后面出现更小的和，就将结果数组清空，将本次的结果放入；如果后面的和与最小的和相同，就将结果插入。循环一次，就可以得出结果。在遍历数组时，可以使用map容器，使用count()函数与find()函数查找相同字符串，简化代码。

# 周二

## 1 力扣

### 953 外星人字典

题目：根据给出的字典顺序，判断字符串数组是否按照该字典顺序排序。

1.比较相邻两个字符串的每一个字符，判断是否按照字典排序，如果某一位达到要求，就比较下一对相邻的字符串；如果未按顺序，就直接return

2.如果两个字符串的前面部分相同，就要比较长度

```cpp
#include <iostream>
#include <string>
#include <vector>

using namespace std;

class Solution;

class Solution
{
public:
    bool isAlienSorted(vector<string>& words, string order)
    {
        int flag = 0;
        //外层循环检查两个相邻单词
        for (int i = 0; i < (signed)words.size() - 1; i++)
        {
            flag = 0;
            //内层循环检查相邻单词的对应位置,循环次数设置为较短的字符串长度
            for (int j = 0; j < min((signed)words[i].size(), (signed)words[i+1].size()); j++)
            {
                //该单词按照字典排序，跳出内层循环，检查下一个单词
                if (order.find(words[i][j]) < order.find(words[i+1][j]))
                {
                    //标志置为1，继续比较下一个单词
                    flag = 1;
                    break;
                }
                //未按字典排序，直接返回
                else if (order.find(words[i][j]) > order.find(words[i+1][j]))
                {
                    return false;
                }
                //对应字符相等，继续比较下一位字符
            }

            if (flag == 1)
            {
                continue;
            }
            //前面部分字符均相同，比较长度
            if (words[i].size() > words[i+1].size())
            {
                return false;
            }
        }
        return true;
    }
};

int main()
{
    vector<string> words = {"apple","app"};
    string order = "abcdefghijklmnopqrstuvwxyz";
    Solution s;
    cout << s.isAlienSorted(words, order) << endl;
    cout << "Hello world!" << endl;
    return 0;
}
```

## 2 c++复习

​	1.指针与引用

​	2.结构体与联合

# 周三

## 1 力扣

### 668 乘法表中第k小的数

思路1：直接法，先给二维数组的第一行和第一列按顺序赋值；剩下的值，用第一列和第一行的乘积赋值。然后再将二维数组转化为一维数组，排序后找到第k个数。思路简单，但是操作量大，会超出时间限制。

```cpp
class Solution1
{
public:
    int findKthNumber(int m, int n, int k)
    {
        //初始化二维数组，将其全部元素赋值为0
        vector<vector<int>> multiplication_table(m, vector<int>(n, 0));
        vector<int> res_arr;
        //打印第一行与第一列
        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n; j++)
            {
              //给第0行赋值为1，2，3，4...
              if (i == 0)
              {
                  multiplication_table[i][j] = j + 1;
              }
              //给第一列赋值，(0,0)处的元素已经在第一行中赋值，从(1,0)处开始给列赋值
              else if (i > 0 && j == 0)
              {
                  multiplication_table[i][j] = i + 1;
              }
            }
        }
        //给其它元素赋值，第一行元素和第一列元素的乘积
        for (int i = 1; i < m; i++)
        {
            for (int j = 1; j < n; j++)
            {
                multiplication_table[i][j] = multiplication_table[i][0] * multiplication_table[0][j];
            }
        }
        //放入一维数组
        for (int i = 0; i < m; i++)
        {
            for (int j = 0; j < n; j++)
            {
                res_arr.push_back(multiplication_table[i][j]);
            }
        }
        //排序
        sort(res_arr.begin(), res_arr.end(),[](int a, int b){
                     return a < b;
                     });
        return res_arr[k-1];
    }
};
```

思路2：需要找到乘法表中第k小的数x，意思也就是，对于数x，有k-1个数小于它。我们需要做的就是，遍历数组，把每一个数取为x，与条件相比较，判断是否有k-1个数小于它。为了减小查找的工作量，使用二分法。

```cpp
class Solution2
{
    public:
    int findKthNumber(int m, int n, int k)
    {
        //乘法表里的最大值与最小值
        int left = 1;
        int right = m * n;
        // [left, right]
        while (left < right)
        {
            //关键！！！中间值的取法
            int mid = left + (right - left) / 2;
            //二分法，改变左右的值
            //mid就是需要遍历查找的数x
            //先在1到mn的区间用二分法查找数x（mid），然后将该x放到二维数组（乘法表）中去查找小于等于x的个数
            //如果个数大于所要求的值k，那么数就选大了，目标数在left和mid之间，就把mid赋值给right
            if (count_num(m, n, mid) >= k)
            {
                right = mid;
            }
            //如果个数小于所要求的值k，那么数就选小了，目标数在mid和right之间，就把mid+1赋值给left
            else
            {
                left = mid + 1;
            }
        }
        //循环结束，左右指针相等，直接返回
        return left;
    }

    // 统计乘法表中有多少个数小于等于待查找的数x，这里的x相当于mid
    int count_num(int m, int n, int k)
    {
        int res = 0;
        // 统计每行小于等于 mid 的数目并求和,为了便于计算，从1到m计数，也可以从0到m-1计数
        //算式相应变为k / i + 1
        for (int i = 1; i <= m; ++i)
        {
            res += min(k / i, n);
        }
        return res;
    }
};
```



# 周四

## 1 力扣

### 766 对角线上矩阵元素相同

思路：逐个对斜行进行遍历，如果遇到有两个斜行的元素不想等，就直接返回。斜行可以分为两个部分进行遍历，右上角和左下角。右上角中，斜行开始元素的行号不变，都是第0行，列号在变。左下角中，斜行开始元素的行号不变，均为第0列，行号在变。

```cpp
#include <iostream>
#include <vector>

using namespace std;

class Solution;

void test01();

class Solution
{
public:
    bool isToeplitzMatrix(vector<vector<int>>& matrix)
    {
        int row = (signed)matrix.size();
        int col = (signed)matrix[0].size();

        int index_r = 0;//行
        int index_c = 0;//列
        //第一行开始遍历
        for (int i = 0; i < col; i++)
        {
            //斜行开始元素为(0,0)(0,1)(0,2)(0,3)...
            //都是从第0行开始，列在变
            index_c = i;//当前的列数
            index_r = 0;
            //当前元素和下一位元素相比较，不需要遍历到最后一个元素
            while (index_r < row - 1 && index_c < col - 1)
            {
                if (matrix[index_r][index_c] != matrix[(long long)index_r + 1][(long long)index_c + 1])
                {
                    return false;
                }
                index_r++;
                index_c++;
            }
        }
        //再次初始化
        index_r = 0;
        index_c = 0;
        //第一列开始遍历,由于(0,0)处已经遍历过，从第二行(1,0)开始
        for (int i = 1; i < row; i++)
        {
            //都是从第0列开始，行在变
            index_r = i;
            index_c = 0;
            while (index_r < row - 1 && index_c < col - 1)
            {
                if (matrix[index_r][index_c] != matrix[(long long)index_r + 1][(long long)index_c + 1])
                {
                    return false;
                }
                index_r++;
                index_c++;
            }
        }
        return true;
    }
};

void test01()
{
    vector<vector<int>> matrix = { {11,74,0,93}, {40,11,74,7} };
    Solution s;
    cout << s.isToeplitzMatrix(matrix) << endl;
}

int main()
{
    test01();
    cout << "Hello world!" << endl;
    return 0;
}
```

# 周五 

## 1 力扣

### 806 写字符串需要的行数

思路：widths数组中，第0个元素代表a的宽度，遍历字符串，对每个字符减'a'，就得到该字符的宽度；对宽度进行累加，超过100就给需要行数加一

```cpp
#include <iostream>
#include <vector>
#include <string>

using namespace std;

class Solution;

void test01();
void print_vector(const vector<int>& v);

class Solution
{
public:
    vector<int> numberOfLines(vector<int>& widths, string s)
    {
        int len_s = (signed)s.size();
        int count_row = 1;//至少需要一行
        int count_num = 0;

        for (int i = 0; i < len_s; i++)
        {
            //判断行空间加上该字符宽度后是否达到100
            if (count_num + widths[s[i] - 'a'] <= 100)
            {
                count_num += widths[s[i] - 'a'];
            }
            //超过100，行数加一，新起一行并初始化记录为0，再将超过长度的字符放到新的一行
            else
            {
                count_row++;
                count_num = 0;
                count_num += widths[s[i] - 'a'];
            }
        }

        return {count_row, count_num};
    }
};

void test01()
{
    vector<int> widths = {4,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10,10};
    string s = "bbbcccdddaaa";
    Solution solution;
    vector<int> res = solution.numberOfLines(widths, s);
    print_vector(res);
}

void print_vector(const vector<int>& v)
{
    for (vector<int>::const_iterator it = v.begin(); it != v.end(); it++)
    {
        cout << *it << " ";
    }
    cout << endl;
}

int main()
{
    test01();
    cout << "Hello world!" << endl;
    return 0;
}
```

# 周六

## 1 力扣

### 961 长度为2n的数组中找到重复次数为n次的元素

思路：总共长度为2n，有n+1个不同元素，其中一个重复n次，说明剩下的n和元素各出现一次，只需找到重复数字大于等于2的数。长度为2，说明都满足要求，随便返回一个。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution;

void test01();

class Solution
{
public:
    int repeatedNTimes(vector<int>& nums)
    {
        int len = (signed)nums.size();
        //小于2，只有两个数，返回其中一个
        if (len == 2)
        {
            return nums[0];
        }
        //遍历数组，出现次数大于2的数，直接返回
        for (int i = 0; i < len; i++)
        {
            if (count(nums.begin(), nums.end(), nums[i]) >= 2 )
            {
                return nums[i];
            }
        }
        return -1;
    }
};

void test01()
{
    vector<int> nums = {2,1,2,5,3,2};
    Solution s;
    cout << s.repeatedNTimes(nums) << endl;
}

int main()
{
    test01();
    cout << "Hello world!" << endl;
    return 0;
}
```

### 1046 最后一块石头的重量

思路：进行循环，退出条件是数组长度大于1，每次循环都对数组排序，选择最大的两个，将其从数组中移除，再计算出差值，不为零就放入数组

```cpp
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

class Solution;

void test01();

class Solution
{
public:
    int lastStoneWeight(vector<int>& stones)
    {
        int len = (signed)stones.size();
        int difference = 0;
        //最后只可能剩下0个或者1个元素
        while (len > 1)
        {
            //排序
            sort(stones.begin(), stones.end(), [](int a, int b){
                 return a >= b;
                 });
            //计算差值
            difference = stones[0] - stones[1];
            //移除最大的两个数
            stones.erase(stones.begin(), stones.begin() + 2);
            //差值不为0，有一块石头有剩余，放入数组
            if (difference != 0)
            {
                stones.push_back(difference);
            }
            //每次操作后，重新计算数组长度
            len = (signed)stones.size();
        }
        //只有一个值
        if (len == 1)
        {
            return stones[0];
        }
        //数组为空
        else
        {
            return 0;
        }
    }
};

void test01()
{
    vector<int> stones = {2,7,4,1,8,1};
    Solution s;
    cout << s.lastStoneWeight(stones) << endl;
}

int main()
{
    test01();
    cout << "Hello world!" << endl;
    return 0;
}
```

# 总结

## 1 计划完成情况

​	1.完成7道力扣题目。已完成

​	2.线程还未开始正式学习，了解大概框架

​	3.Qt未看

​	4.结构体与共用体，常见类型的大小

## 2 下周计划

​	1.c++面试复习，多态，虚函数

​	2.简历

​	3.Qt demo

​	4.力扣每日一道





























