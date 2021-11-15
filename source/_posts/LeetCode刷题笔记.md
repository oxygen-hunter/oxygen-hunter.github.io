---
title: LeetCode刷题笔记
date: 2020-01-27 09:35:45
categories:
tags:
---

# 简单题

## 7. 整数反转

1. 每次截取末位，*10，叠加，直到原数x = 0

2. 处理溢出，整数范围在-2147483648~2147483647，每次要溢出之前都是大于2147483647/10，或者小于-2147483648/10，所以只要判定一下即可

```c++
class Solution {
public:
    int reverse(int x) {
        //每次截取末位，*10，叠加
        int last = 0;
        while (x != 0)
        {
            if (last > 2147483647 / 10 ||
                last < -2147483648 / 10)
                {
                    return 0;
                }
            last = last * 10;
            last += x % 10;
            x = x / 10;
        }
        
        return last;
    }
};
```

## 9. 回文数

1. 首先，负数不是回文数
2. 对非负数，先得到逆序数，再判断是否和原数相等

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        if (x < 0)
        {
            return false;
        }
        int last = 0;
        int oldx = x;
        while (x != 0)
        {
            if (last > 2147483647 / 10 ||
                last < -2147483648 / 10)
                {
                    last = -1;
                    break;
                }
            last = last * 10;
            last += x % 10;
            x = x / 10;
        }
        return last == oldx;
    }
};
```

***

官方题解说只要翻转一半的数字就可以了，同时可以避免溢出，如何判断到一半了呢？条件是x < lastx

12345 - 54321，12345|0，1234|5，123|54，12|543。翻转到123 和543是否相等？12和54

123456-654321，123456|0,12345|6,12345|65,123|654。翻转到123和654是否相等，



修改了代码如下

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        //负数，末位为0但本身不是0的数
        if (x < 0 || (x % 10 == 0 && x != 0))
        {
            return false;
        }
        int lastx = 0;
        while (x > lastx)
        {
            lastx = lastx * 10 + x % 10;
            x = x / 10;
        }
        //对于偶数长度的x，lastx == x
        //对于奇数长度的x，lastx / 10 == x
        return lastx == x || lastx / 10 == x;
    }
};
```

## 13. 罗马数字转整数

一位位处理即可，遇到IXC，就再看下一位一起处理。

```c++
class Solution {
public:
    int romanToInt(string s) {
        //遍历，假设当前字符为c1，下个字符为c2
        //c1=I, 若c2=V,X, 4和9
        //c1=X, 若c2=L,C, 40和90
        //c1=C, 若c2=D,M, 400和900
        //其他, +对应
        int N = 0;
        for (int i = 0; i < s.length(); )
        {
            switch (s[i])
            {
            case 'I':
                help(i, s, N, 'V', 'X', 4, 9, 1);
                break;
            case 'X':
                help(i, s, N, 'L', 'C', 40, 90, 10);
                break;
            case 'C':
                help(i, s, N, 'D', 'M', 400, 900, 100);
                break;
            case 'V':
                N += 5;
                i++;
                break;
            case 'L':
                N += 50;
                i++;
                break;
            case 'D':
                N += 500;
                i++;
                break;
            case 'M':
                N += 1000;
                i++;
                break;
            }
        }
        return N;
    }

    void help(int& i, string& s, int& N, char c1, char c2, int n1, int n2, int n3)
    {
        if (i+1 < s.length())
        {
            if (s[i+1] == c1)
            {
                N += n1;
                i += 2;
            }
            else if (s[i+1] == c2)
            {
                N += n2;
                i += 2;
            }
            else
            {
                N += n3;
                i++;
            }
        }
        else
        {
            N += n3;
            i++;
        }
    }
};
```

***

上面的方法写的不够简洁，遇到改需求（比如IXC之类的改成别的字符会比较麻烦）会大改代码，看了一篇写的挺简洁的，即把我的help函数改成查表法，值得注意的是第14行的`if (i+1 < s.length() && hash[s[i]-'C'] < hash[s[i+1]-'C'])`的第二个条件，假如出现小面额在大面额之前的情况，我一开始以为这个写法有漏洞，比如IM这种情况，后来想想这种情况不属于合法输入，所以可以。

```c++
class Solution {
public:
    int romanToInt(string s) {
        int s1[] = {1, 5, 10, 50, 100, 500, 1000};
        int s2[] = {'I','V','X','L','C','D','M'};
        int hash['X'-'C'+1] = {0}; 
        int N = 0;
        for (int i = 0; i < sizeof(s1)/sizeof(s1[0]); i++)
        {
            hash[s2[i]-'C'] = s1[i];
        }
        for (int i = 0; i < s.length(); )
        {
            if (i+1 < s.length() && hash[s[i]-'C'] < hash[s[i+1]-'C'])
            {   //第二个判断条件是基于不会有非法输入，比如IM之类的出现
                N += hash[s[i+1]-'C'] - hash[s[i]-'C'];
                i += 2;
            }
            else
            {
                N += hash[s[i]-'C'];
                i++;
            }
        }
        return N;
    }
};
```

## 14. 最长公共前缀

从下标0开始一列列对比

```c++
class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if (strs.size() == 0 || strs[0].length() == 0)
        {
            return "";
        }
        int idx = -1;
        bool flag = true;
        while (flag)
        {
            idx++;
            char c = strs[0][idx];
            for (int i = 0; i < strs.size(); i++)
            {
                if (idx >= strs[i].length() || strs[i][idx] != c)
                {
                    flag = false;
                    break;
                }
            }
        }
        return strs[0].substr(0, idx);
    }
};
```

## 20. 有效的括号

用一个栈，遇到左括号入栈，遇到右括号看栈是否为空and栈顶是否为对应左括号。最后看栈是否为空

```c++
class Solution {
public:
    bool isValid(string s) {
        std::stack<char> st;
        for (auto c : s)
        {
            switch (c)
            {
            case '(': st.push(c); break;
            case '[': st.push(c); break;
            case '{': st.push(c); break;
            case ')':
                if (st.empty() || st.top() != '(') 
                    return false;
                else
                    st.pop();
                break;
            case ']':
                if (st.empty() || st.top() != '[')
                    return false;
                else
                    st.pop();
                break;
            case '}':
                if (st.empty() || st.top() != '{')
                    return false;
                else
                    st.pop();
                break;
            default:
                break;          
            }
        }
        return st.empty();
    }
};
```

***

可以加一些判断过滤：

1. s.length()为奇数
2. 栈深度大于s.length()/2时，后面有再多的右括号也消化不了
3. 栈深度大于剩余长度时，后面有再多的右括号也消化不了（强于2）

但其实没怎么改进速度

```c++
class Solution {
public:
    bool isValid(string s) {
        if (s == "")
        {
            return true;
        }
        if (s.length() % 2 == 1)
        {
            return false;
        }
        std::stack<char> st;
        for (int i = 0; i < s.length(); i++)
        {
            char c = s[i];
            switch (c)
            {
            case '(': st.push(c); break;
            case '[': st.push(c); break;
            case '{': st.push(c); break;
            case ')':
                if (st.empty() || st.top() != '(') 
                    return false;
                else
                    st.pop();
                break;
            case ']':
                if (st.empty() || st.top() != '[')
                    return false;
                else
                    st.pop();
                break;
            case '}':
                if (st.empty() || st.top() != '{')
                    return false;
                else
                    st.pop();
                break;
            default:
                break;          
            }
            if (st.size() > s.length() - i - 1)
            {
                return false;
            }
        }
        return st.empty();
    }
};
```

## 21. 合并两个有序链表

逐个比较两个链表的当前元素，小的插入新链表，然后当前元素后移一位

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* p1 = l1, *p2 = l2;
        ListNode* merge = new ListNode(-1);
        ListNode* tail = merge;
        while (p1 != NULL && p2 != NULL)
        {
            if (p1->val < p2->val)
            {
                ListNode* tmp = new ListNode(p1->val);
                tail->next = tmp;
                tail = tmp;
                p1 = p1->next;
            }
            else
            {
                ListNode* tmp = new ListNode(p2->val);
                tail->next = tmp;
                tail = tmp;
                p2 = p2->next;
            }
        }
        while (p1 != NULL)
        {
            ListNode* tmp = new ListNode(p1->val);
            tail->next = tmp;
            tail = tmp;
            p1 = p1->next;
        }
        while (p2 != NULL)
        {
            ListNode* tmp = new ListNode(p2->val);
            tail->next = tmp;
            tail = tmp;
            p2 = p2->next;
        }
        ListNode* ret = merge->next;
        delete merge;
        return ret;
    }
};
```

上面的写法没有利用l1和l2的空间，下面重复利用l1和l2的空间。用一个指针prev记录上一个选中的节点，然后选中当前节点cur后，将prev->next=cur。

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode* p1 = l1, *p2 = l2;
        ListNode* merge = new ListNode(-1);
        ListNode* prev = merge;
        while (p1 != NULL && p2 != NULL)
        {
            if (p1->val < p2->val)
            {
                prev->next = p1;
                prev = p1;
                p1 = p1->next;
            }
            else
            {
                prev->next = p2;
                prev = p2;
                p2 = p2->next;
            }
        }
        while (p1 != NULL)
        {
            prev->next = p1;
            prev = p1;
            p1 = p1->next;
        }
        while (p2 != NULL)
        {
            prev->next = p2;
            prev = p2;
            p2 = p2->next;
        }
        ListNode* ret = merge->next;
        delete merge;
        return ret;
    }
};
```

## 27. 移除元素

迭代器的使用

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        for (auto p = nums.begin(); p != nums.end(); )
        {
            if (*p == val)
            {
                p = nums.erase(p);
            }
            else
            {
                ++p;
            }
        }
        return nums.size();
    }
};
```

***

第二种办法，注意到题目里说元素顺序可以改变，所以可以不真正删除元素，而是把元素调到数组的最后面去（用时反而比上面的解法慢，不解）

```c++
class Solution {
public:
    int removeElement(vector<int>& nums, int val) {
        int p1 = 0, p2 = nums.size() - 1;
        int revNum = 0;
        while (p1 <= p2)
        {
            if (nums[p1] == val)
            {
                revNum++;
                while (nums[p2] == val && p1 < p2)
                {
                    p2--;
                    revNum++;
                }
                if (p1 < p2)
                {
                    std::swap(nums[p1], nums[p2]);
                    p2--;
                }
                else
                {
                    break;
                }
            }
            p1++;
        }
        return nums.size() - revNum;
    }
};
```

## 28. 实现strStr()

改进的kmp

```
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle == "") return 0;
        string& txt = haystack;
        string& pat = needle;
        int n = txt.length();
        int m = pat.length();
        auto dp = new int[m][256];
        memset(dp, 0, m*256*sizeof(int));

        dp[0][pat[0]] = 1;
        int X = 0;
        for (int i = 1; i < m; i++)
        {
            for (int c = 0; c < 256; c++)
            {
                if (c == pat[i])
                    dp[i][c] = i + 1;
                else
                    dp[i][c] = dp[X][c];
            }
            X = dp[X][pat[i]];
        }

        int j = 0;
        for (int i = 0; i < n; i++)
        {
            j = dp[j][txt[i]];
            if (j == m)
            {
                return i - m + 1;
            }
        }

        return -1;
    }
};
```

sunday

```c++
class Solution {
public:
    int strStr(string haystack, string needle) {
        if (needle == "") return 0;
        int n = haystack.length();
        int m = needle.length();
        if (n < m) return -1;

        int sunday[256];
        for (int i = 0; i < 256; i++)
        {
            sunday[i] = -1;
        }
        for (int i = 0; i < m; i++)
        {
            sunday[needle[i]] = m - i;
        }

        for (int i = 0; i < n; )
        {
            int j = 0;
            for ( ; i+j < n && j < m; j++)
            {
                if (haystack[i+j] != needle[j])
                    break;
            }
            if (j == m)
            {
                return i;
            }
            else if (i+m < n)
            {
                if (sunday[haystack[i+m]] != -1)
                {
                    i = i + sunday[haystack[i+m]];
                }
                else
                {
                    i = i + m;
                }
            }
            else
            {
                return -1;
            }
        }
        return -1;
    }
};
```

## 35. 搜索插入位置

二分查找递归版

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if (nums.size() == 0) return 0;
        if (target < nums[0]) return 0;
        if (target > nums[nums.size()-1]) return nums.size();

        return re(nums, 0, nums.size() - 1, target);
    }

    int re(vector<int>& nums, int sat, int end, int target)
    {
        if (sat == end) return target <= nums[sat] ? sat : sat+1;
        int mid = (sat + end) / 2;
        if (target == nums[mid])
            return mid;
        else if (target < nums[mid]) 
            return re(nums, sat, mid, target);
        else 
            return re(nums, mid + 1, end, target);
    }
};
```

二分查找迭代版

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        if (nums.size() == 0) return 0;
        if (target < nums[0]) return 0;
        if (target > nums[nums.size()-1]) return nums.size();

        int sat = 0, end = nums.size()-1;
        int res = 0;
        while (true)
        {
            if (sat == end) return target <= nums[sat] ? sat : sat+1;
            int mid = (sat+end)/2;
            if (target == nums[mid])
                return mid;
            else if (target < nums[mid]) 
                end = mid;
            else 
                sat = mid+1;
        }
        return 0;
    }
};
```

## 38. 外观数列

BF，枚举之前的字符串，处理每个字符串的方法，遇到相同的字符，计数器+1，遇到不同的字符，将计数器和当前字符添加到nstr的尾部

```c++
class Solution {
public:
    string countAndSay(int n) {
        string str = "1";
        for (int i = 2; i <= n; i++)
        {
            string nstr = "";
            char cur = str[0];
            int num = 0;
            int j = 0;
            while (j < str.length())
            {
                while (j < str.length() && str[j] == cur)
                {
                    j++;
                    num++;
                }
                nstr.append(to_string(num));
                nstr.push_back(cur);
                if (j < str.length())
                {
                    cur = str[j];
                    num = 0;
                }
            }
            str = nstr;
        }
        return str;
    }
};
```

# 困难题

## 4. 寻找两个有序数组的中位数

要求时间复杂度O(log(m+n))，想法每次砍掉一半的候选人，但出现了问题。

假如m和n均为奇数，或者均为偶数，那么中位数必然是(ai+bj)/2，如果m和n一奇一偶，那么中位数必然是ai或者bj。

每次砍掉一半，意味着m和n的奇偶性会变化，然后出事。

比如奇数，还是奇数，xxxx但是偶数，有可能变成奇数或者偶数。所以一奇一偶没问题。

两奇或者两偶的需要找第(m+n)/2-1大和(m+n)/2大的数

* 一种想法是：把两个数组中最大的一个数去掉，变成了一奇一偶，算出原第(m+n)/2-1大的数；把最小的一个数也去掉，变成了一奇一偶，算出原第(m+n)/2-1大的数，然后相加除以二
* 不过这样相当于算了两遍，改进一下，算出原第(m+n)/2-1大的数后，下一个数的候选人是这个数所在数组的后一个数，或者另一个数组的二分查找的刚好大于等于这个数的数



## 41. 缺失的第一个正数

给你一个未排序的整数数组，请你找出其中没有出现的最小的正整数。



```c++
class Solution {
public:
    int firstMissingPositive(vector<int>& nums) {
        //原地哈希
        //1. 把不在[1, N]的负数和0设为N+1，因为是无效数据，不能影响后面的标记
        //2. 把在[1, N]的数m，令nums[m-1] = -abs(nums[m-1])，打上标记
        //3. 遍历数组，如果有正数，说明没被标记，返回下标+1；如果遍历完了，说明[1, N]都出现了，返回N+1
        int N = nums.size();

        for (int i = 0; i < N; i++)
        {
            if (nums[i] <= 0)
                nums[i] = N+1;
        }

        for (int i = 0; i < N; i++)
        {
            int absi = abs(nums[i]);
            if (absi >= 1 && absi <= N)
            {
                nums[absi - 1] = -abs(nums[absi - 1]);
            }
        }

        for (int i = 0; i < N; i++)
        {
            if (nums[i] > 0)
            {
                return i + 1;
            }
        }
        return N+1;
    }
};
```



## 1019. 链表中的下一个更大节点

对于链表 [2, 5, 1, 1, 4]

next[] = [5, 0, 4, 4, 0]

```
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> nextLargerNodes(ListNode* head) {
        //单调递减栈
        //1. 把链表转为数组，方便后面按下标索引
        //2. 遍历数组，如果nums[i] <= nums[top]就一直pop，pop一个就令next[top] = i，然后push(i)
        //3. 把栈里剩余的元素的next置为0

        vector<int> nums;
        while (head != NULL)
        {
            nums.push_back(head->val);
            head = head->next;
        }

        stack<int> stk;
        vector<int> answer(nums.size());
        for (int i = 0; i < nums.size(); i++)
        {
            while (!stk.empty() && nums[i] > nums[stk.top()])
            {
                answer[stk.top()] = nums[i];
                stk.pop();
            }
            stk.push(i);
        }

        while (!stk.empty())
        {
            answer[stk.top()] = 0;
            stk.pop();
        }

        return answer;
    }
};
```



## 896. 单调数列

判断一个数列是否是单调递增or单调递减，解法1，往后看；解法2，两个单调栈

```c++
class Solution {
public:
    bool isMonotonic(vector<int>& A) {
        //简单往后看
        //1. 设置dir = 0
        //2. 遍历A，如果dir = 0，prev < cur，dir = 1（递增）；prev > cur, dir = -1（递减）；相等不管
        //如果dir = -1，prev < cur，return false；其他不管
        //如果dir = 1，prev > cur，return false；其他不管
        //3. 最后return true

        if (A.size() <= 1) return true;
        
        int dir = 0;
        int prev = A[0];
        for (int i = 1; i < A.size(); i++)
        {
            int cur = A[i];
            if (dir == 0)
            {
                if (prev < cur) dir = 1;
                else if (prev > cur) dir = -1;
            }
            else if (dir == -1)
            {
                if (prev < cur) return false;
            }
            else if (dir == 1)
            {
                if (prev > cur) return false;
            }
            prev = cur;
        }

        return true;
    }
};
```



```c++
class Solution {
public:
    bool isMonotonic(vector<int>& A) {
        //两个单调栈
        //1. 一个递增栈incStk，如果cur >= top，就往incStk中push(cur)，否则一路pop直到cur >= top，然后push(cur)
        //2. 一个递减栈decStk，一路pop直到 cur <= top，然后push(cur)
        //3. 如果是单调数列，那么两个栈一定1个是满的，另一个无所谓

        stack<int> increaseStk, decreaseStk;
        int N = A.size();

        for (int i = 0; i < N; i++)
        {
            int cur = A[i];
            while (!increaseStk.empty() && cur < increaseStk.top())
            {
                increaseStk.pop();
            }
            increaseStk.push(cur);

            while (!decreaseStk.empty() && cur > decreaseStk.top())
            {
                decreaseStk.pop();
            }
            decreaseStk.push(cur);
        }

        int size1 = increaseStk.size();
        int size2 = decreaseStk.size();
        if (size1 == N || size2 == N)
        {
            return true;
        }
        else
        {
            return false;
        }
    }
};
```



## 84. 柱状图中最大的矩形

![image-20200630090405899](C:\Users\hp\AppData\Roaming\Typora\typora-user-images\image-20200630090405899.png)



解法1：暴力

```
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        //暴力
        //1. 对于第i个柱子，他所在的最大矩形，开始位置在往左看第一个比他矮的柱子下标+1
        //结束位置在往右看第一个比他矮的柱子下标-1
        //高度是他自己的高度
        //2. 遍历一遍，时间O(n2)，空间原地O(n)
        //3. 为了方便处理头尾情况，在heights数组的前后各加一个0

        heights.insert(heights.begin(), 0);
        heights.push_back(0);

        int N = heights.size();
        int Max = 0;
        for (int i = 1; i < N - 1; i++)
        {
            int H = heights[i];
            int W_begin = 0 + 1;
            int W_end = N - 1; 
            for (int j = i - 1; j >= 0; j--)
            {
                if (heights[j] < H)
                {
                    W_begin = j + 1;
                    break;
                }
            }
            for (int j = i + 1; j < N; j++)
            {
                if (heights[j] < H)
                {
                    W_end = j - 1;
                    break;
                }
            }
            int W = W_end - W_begin + 1;
            int Rectangle = W * H;
            Max = max(Max, Rectangle);
        }
        return Max;
    }
};
```



解法2：单调栈

观察到，我们需要的是一个柱子的，左边第一个比他小的，和右边第一个比他小的；而单调递增栈恰好有这个性质：当一个元素被pop的时候，此刻的栈顶是左边第一个比他小的，让他pop的元素是右边第一个比他小的



```c++
class Solution {
public:
    int largestRectangleArea(vector<int>& heights) {
        //单调递增栈
        //1. 当一个元素被pop时，pop后的栈顶元素，是左边从右往左数第一个比他小的，
        //让他pop的元素，是右边从左往右数第一个比他小的
        //2. 由此可以在每个元素被pop时计算出对应的矩形面积
        //3. 注意，递增栈中存的是下标，不是值
        //4. 预先在heights数组的首尾插0，首部的0为了保证首元素计算正确，尾部的0是为了把栈里最后剩余元素都pop出去

        heights.insert(heights.begin(), 0);
        heights.push_back(0);

        stack<int> incStk;
        int N = heights.size();
        int Max = 0;
        for (int i = 0; i < N; i++)
        {
            while (!incStk.empty() && heights[i] < heights[incStk.top()])
            {
                int top = incStk.top();
                int H = heights[top];
                int W_end = i - 1;
                incStk.pop();
                if (!incStk.empty())
                {
                    int W_begin = incStk.top() + 1;
                    int W = W_end - W_begin + 1;
                    int Rectangle = W * H;
                    Max = max(Max, Rectangle);
                }
            }
            incStk.push(i);
        }
        return Max;
    }
};
```



## [剑指 Offer 09. 用两个栈实现队列](https://leetcode-cn.com/problems/yong-liang-ge-zhan-shi-xian-dui-lie-lcof/)

```c++
class CQueue {
public:
    //双栈，一个headStk栈负责存队列的前半段，另一个tailStk负责存队列的后半段
    //1. 有appendTai的时候只要push到tailStk中
    //2. 有deleteHead时，先看headStk是否为空，
    //如果为空，从tailStk中依次pop出所有元素，
    //  并push到headStk中，如果仍然为空，返回-1，否则返回headStk的栈顶并pop
    //如果不为空，返回headStk的栈顶并pop
    CQueue() {
        
    }
    
    void appendTail(int value) {
        tailStk.push(value);
    }
    
    int deleteHead() {
        if (!headStk.empty())
        {
            int top = headStk.top();
            headStk.pop();
            return top;
        }
        else
        {
            while (!tailStk.empty())
            {
                int val = tailStk.top();
                tailStk.pop();
                headStk.push(val);
            }
            if (!headStk.empty())
            {
                int top = headStk.top();
                headStk.pop();
                return top;
            }
            else
            {
                return -1;
            }
        }
    }
private:
    stack<int> headStk, tailStk;
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
```



## 300. 最长上升子序列

解法1：

dp[i] 代表以nums[i]作为首元素的最长上升子序列的长度

转移方程：dp[i] = max(1, max(dp[j]) + 1)  (j > i, num[j] > nums[i])

```python
class Solution:
    def lengthOfLIS(self, nums: List[int]) -> int:
        if not nums:
            return 0

        dp = [1] * len(nums)
        for i in range(len(nums)-2, -1, -1):
            max_len = 0
            for j in range(i+1, len(nums)):
                if nums[j] > nums[i]:
                    max_len = max(max_len, dp[j])
            dp[i] = max_len + 1
        return max(dp)
```



## x. 矩阵连乘

思考：矩阵Ai ... Aj 的乘法次数记做A[i, j]，最后一次左右两坨矩阵的乘法位置记做k，最少次数是 min(A[i, k] + A[k+1, j] + p[i] * p[k] * p[j])



## 150. 逆波兰表达式求值

只有+-*/四个运算符

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        //用栈计算
        //1. 遇到数字push
        //2. 遇到操作符取出栈顶两个元素进行运算，然后push
        //3. 最后栈里剩下的就是结果

        stack<int> stk;
        for (int i = 0; i < tokens.size(); i++)
        {
            string& s = tokens[i];
            if (s[0] >= '0' && s[0] <= '9' || s.length() > 1)
            {
                int val = atoi(s.c_str());
                stk.push(val);
            }
            else
            {
                int right = stk.top();
                stk.pop();
                int left = stk.top();
                stk.pop();
                int result = 0;
                switch (s[0])
                {
                    case '+': result = left+right; break;
                    case '-': result = left-right; break;
                    case '*': result = left*right; break;
                    case '/': result = left/right; break;
                }
                stk.push(result);
            }
        }

        return stk.top();

    }
};
```



## 224. 基本计算器

包含()+-，非负整数和空格

解法1：中缀转后缀，计算后缀

```c++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        //用栈计算
        //1. 遇到数字push
        //2. 遇到操作符取出栈顶两个元素进行运算，然后push
        //3. 最后栈里剩下的就是结果

        stack<int> stk;
        for (int i = 0; i < tokens.size(); i++)
        {
            string& s = tokens[i];
            if (s[0] >= '0' && s[0] <= '9' || s.length() > 1)
            {
                int val = atoi(s.c_str());
                stk.push(val);
            }
            else
            {
                int right = stk.top();
                stk.pop();
                int left = stk.top();
                stk.pop();
                int result = 0;
                switch (s[0])
                {
                    case '+': result = left+right; break;
                    case '-': result = left-right; break;
                    case '*': result = left*right; break;
                    case '/': result = left/right; break;
                }
                stk.push(result);
            }
        }

        return stk.top();

    }

    int calculate(string s) {
        //中缀转后缀，再用栈算后缀
        //1. 遇到数字就输出，遇到空格跳过
        //2. 遇到+-就和栈顶比，如果入栈时优先级比栈顶的栈内优先级高，那就push
        //  否则一路pop并输出top，直到不满足条件
        //3. 遇到(直接push
        //4. 遇到)一路pop并输出top，直到遇到第一个(
        //5. 扫完字符串后把栈里剩下的东西输出出来
        
        enum {plus = 0, minus, lb, rb, begin, end};
        int osp[6] = { 2, 2, 8, 1, 0, 0};
        int isp[6] = { 3, 3, 1, 8, 0, 0};

        stack<pair<char, int>> opStk;
        vector<string> suffix;

        opStk.push(pair<char, int>('#', 0));
        s.push_back('e');
        for (int i = 0; i < s.length(); i++)
        {
            char c = s[i];
            if (c == ' ') continue;
            int j = i;
            while (s[j] >= '0' && s[j] <= '9')
            {
                j++;
            }
            if (j != i)
            {
                suffix.push_back(s.substr(i, j-i));
                i = j - 1;
            }
            else
            {
                int op_enum = 0;
                switch (c)
                {
                    case '+': op_enum = 0; break;
                    case '-': op_enum = 1; break;
                    case '(': op_enum = 2; break;
                    case ')': op_enum = 3; break;
                    case 'e': op_enum = 5; break;
                }
                while (osp[op_enum] <= opStk.top().second)
                {
                    string op_str = "a";
                    op_str[0] = opStk.top().first;
                    opStk.pop();
                    
                    if (op_str == "(" || op_str == "#")
                    {
                        break;
                    }
                    suffix.push_back(op_str);
                }
                if (c != ')' && c != 'e')
                    opStk.push(pair<char, int>(c, isp[op_enum]));
            }
        }

        return evalRPN(suffix);
    }
};
```



解法2：先词法分析，找exp的dominant operator，递归求解exp1 op exp2

```c++
class Solution {
public:
    enum type {num, plus, minus, lb, rb};
    struct token
    {
        type t;
        int val;
        int level;
        int match;
        token(type _t = num, int _val = 0): t(_t), val(_val) {}
    };
    int calculate(string s) {
        //词法分析+递归解exp1 op exp2
        //1. 把s变成tokens数组
        //2. 对于exp(p, q)，
        //如果p > q，错误
        //如果p = q，返回tokens[p].val
        //如果p < q，如果pq是一对匹配的括号，返回exp(p+1, q-1)
        //由于加减法都是左结合，所以我们从q往p找第一个主加号，设其下标为k，
        //  返回 exp(p, k-1) op(k) exp(k+1, q)
        //主加号就是当前看来没被括号包着的加号，可以为每一个加号设置一个绝对包裹层数，传参时每脱掉一层括号，就把基础层数+1，然后去找第一个绝对层数-相对层数=0的加号
        //比如((a+b)+c)，绝对层数分别为2和1，脱掉第一层括号时，基础层数为1，发现第二个加号是主加号
        //绝对层数的计算可以由栈匹配括号时，遇到加号就把level设置成stack.size
        //匹配的括号可以由栈匹配括号时，为每个左括号记录他的右括号

        //词法分析
        vector<token> tokens;
        for (int i = 0; i < s.length(); i++)
        {
            char c = s[i];
            if (c == ' ') continue;
            else if (c == '(') tokens.push_back(token(lb));
            else if (c == ')') tokens.push_back(token(rb));
            else if (c == '+') tokens.push_back(token(plus));
            else if (c == '-') tokens.push_back(token(minus));
            else
            {
                int j = i;
                while (s[j] >= '0' && s[j] <= '9')
                {
                    j++;
                }
                int val = atoi(s.substr(i, j-i).c_str());
                tokens.push_back(token(num, val));
                i = j-1;
            }
        }

        //计算层数
        stack<int> bStk;
        for (int i = 0; i < tokens.size(); i++)
        {
            type t = tokens[i].t;
            if (t == lb) bStk.push(i);
            else if (t == rb) { tokens[bStk.top()].match = i; bStk.pop(); }
            else if (t == plus || t == minus) tokens[i].level = bStk.size();
        }

        //解exp
        return exp(tokens, 0, tokens.size()-1, 0);
    }

    int exp(vector<token>& tokens, int p, int q, int level)
    {
        if (p > q) return 0;
        else if (p == q) return tokens[p].val;
        else if (tokens[p].t == lb && tokens[q].t == rb && tokens[p].match == q) return exp(tokens, p+1, q-1, level+1);
        else
        {
            int dominant_op = -1;
            for (int i = q; i >= p; i--)
            {
                if ((tokens[i].t == plus || tokens[i].t == minus) && tokens[i].level == level)
                {
                    dominant_op = i;
                    break;
                }
            }
            
            if (tokens[dominant_op].t == plus)
            {
                return exp(tokens, p, dominant_op-1, level) + exp(tokens, dominant_op+1, q, level);
            }
            else
            {
                return exp(tokens, p, dominant_op-1, level) - exp(tokens, dominant_op+1, q, level);
            }
        }
        return 1;
    }
};
```

