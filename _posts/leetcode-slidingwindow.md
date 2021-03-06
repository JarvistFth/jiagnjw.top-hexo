---
title: leetcode-slidingwindow
date: 2020-08-19 21:18:11
categories: 
- leetcode
- 滑动窗口
tags: [leetcode ,滑动窗口]
keywords: [leetcode,滑动窗口]
---

参考labudalong大佬的滑动窗口框架；一些是适用于给定子串，一些则不是，但是需要维护window。
window的维护，有的选deque，有选priority_queue的。
<!---more--->

滑动窗口框架：
```C++
//普通版：
int left=0,right=0;
        while(right<A.size()){
            //移入窗口的元素
            //第一步：入窗：
            int r = A[right];
            //判断条件，对窗口内的数据进行处理更新
            if(r == 0){
                // todo
            }
            //第二步：出窗：
            //判断窗口需要左移的条件
            while(window needs shrink){
                //移出窗口的元素
                int l = A[left];
                //对窗口内数据进行更新
                if(l == 0) {
                    // todo
                }
                //窗口左移
                left++;
            }
            //第三步：干该干的
            //一般是看情况在哪里更新答案的长度，看结果应该在扩大窗口还是缩小窗口时候进行更新
            // ans = max(ans,right-left+1);
            //窗口右移
            right++;
        }

        return ans;



class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char,int> need,window;

        int left=0,right=0;
        //
        int valid = 0;

        for(auto c:t){
            need[c]++;
        }

        while(right<s.size()){
            //元素移入窗口
            char c = s[right];

            //对窗口内元素进行处理
            if(need.count(c)){
                window[c]++;
                if(window[c] == need[c]){
                    valid++;
                }
            }

            //判断元素移出窗口
            while(valid == need.size()){

                //d是要移出的元素
                char d = s[left];
                //窗口左移
                left++;
                //对窗口内元素进行处理
                if(need.count(d)){
                    if(window[d] == need[d]){
                        valid--;
                    }
                    window[d]--;
                }
            }
            right++;
        }
        // return len==INT_MAX?"":s.substr(start,len);
    }
};        


```

## 剑指 Offer 48. 最长不含重复字符的子字符串
https://leetcode-cn.com/problems/zui-chang-bu-han-zhong-fu-zi-fu-de-zi-zi-fu-chuan-lcof/

用一个map维护一个window，窗口右移。当window里面是有遍历的字符串的时候，窗口左移。左移后比较当前长度是否是最长。

```C++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        unordered_map<char,int>window;

        int left=0,right=0;
        int ans = 0;

        while(right<s.size()){
            char c = s[right];
            window[c]++;
            while(window[c] > 1){
                char d = s[left];
                left++;
                window[d]--;
            }
            ans = max(ans,right-left+1);
            right++;
        }
        return ans;
    }
};
```

## 1004. 最大连续1的个数 III
https://leetcode-cn.com/problems/max-consecutive-ones-iii/
窗口右移；当遇到0的个数大于K时，窗口左移。在窗口左移后更新最大长度。

```C++
class Solution {
public:
    int longestOnes(vector<int>& A, int K) {
        int ans = 0;

        int left=0,right=0;
        int zeroCount = 0;
        while(right<A.size()){
            int r = A[right];
            
            if(r == 0){
                zeroCount++;
            }
            while(zeroCount > K){
                if(A[left] == 0) {
                    zeroCount--;
                }
                left++;
            }
            
            ans = max(ans,right-left+1);
            right++;
        }

        return ans;
    }
};
```

## 480. 滑动窗口中位数
https://leetcode-cn.com/problems/sliding-window-median/

想法就是用一个vector维护一个window，每次window超过K个元素就出窗；入窗的时候通过二分查找维护窗口有序插入，这样可以方便找到window的中位数(window[(i-1)/2] + window(i/2))/2 。
代码比较简单，也比较容易理解，但是复杂度比较高O(n^2)级别了应该【窗口遍历O(n)，插入排序O(n)】。

```C++
class Solution {
public:
    vector<double> medianSlidingWindow(vector<int>& nums, int k) {

        int left=0,right=0;
        vector<double>window;
        vector<double>ans;

        while(right<nums.size()){

            auto index = lower_bound(window.begin(),window.end(),nums[right]);
            window.insert(index,nums[right]);

            while(window.size()>=k){
                if(window.size() == k){
                    double mid = (window[(k-1)/2] + window[k/2])/2.0;
                    ans.push_back(mid);
                }

                auto index = lower_bound(window.begin(),window.end(),nums[left]);
                window.erase(index);

                left++;
            }
            right++;  
        }

        return ans;


    }
};
```

## 76. 最小覆盖子串
https://leetcode-cn.com/problems/minimum-window-substring/

字符串子串的滑动窗口模板。维护一个window和一个need，入窗时判断一下入窗的元素是不是子串的字符，如果是window++;如果window和need维护的字符次数相同，就说明window这个字符可用；当window维护的可用字符==need.size()，说明子串和原字符串已经完全覆盖了。这时候为了保证长度最小，就要窗口左移，元素出窗。出窗的时候也要更新窗口内的数据。同时更新最小长度。

```C++
class Solution {
public:
    string minWindow(string s, string t) {
        unordered_map<char,int> need,window;

        int left=0,right=0;
        int start=0,len=INT_MAX;
        int valid = 0;

        for(auto c:t){
            need[c]++;
        }

        while(right<s.size()){
            char c = s[right];
            if(need.count(c)){
                window[c]++;
                if(window[c] == need[c]){
                    valid++;
                }
                

            }

            while(valid == need.size()){
                if(right-left+1<len){
                    start = left;
                    len = right-left+1;
                }

                char d = s[left];
                left++;
                if(need.count(d)){
                    if(window[d] == need[d]){
                        valid--;
                    }
                    window[d]--;
                }
            }
            right++;

        }

        return len==INT_MAX?"":s.substr(start,len);
    }
};
```

## 239. 滑动窗口最大值
https://leetcode-cn.com/problems/sliding-window-maximum/

维护一个单调双端队列作为窗口，每次将数组元素下标入窗。当队头元素下标超出窗口大小k时，队头元素出窗。此时window.front()<=此时元素下标-k。

同样，如果入窗的元素大于窗内末尾元素，就将窗口末尾元素出窗。这样就可以维持队头元素是最大值。

当窗口超过k的时候，将此时队头元素（也就是最大值的下标）送入ans。

```C++
class Solution {
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {


        vector<int> ans;
        deque<int> window;
        int right=0;
        

        while(right<nums.size()){
            while(!window.empty() && window.front() <= right-k){
                window.pop_front();
            }
            while(!window.empty() && nums[right]>nums[window.back()]){
                window.pop_back();
            }
            window.push_back(right);
            if(right>=k-1){
                ans.push_back(nums[window.front()]);
            }
            right++;

        }
        return ans;
    }
};
```

## 剑指 Offer 59 - II. 队列的最大值
https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/

和上题基本一致，维护一个deque，最大值放在队列头；当存入的value大于队尾元素时，将队尾元素出队；当从队头出队时，判断一下是不是deque的队列头，如果是deque也出队。

```C++
class MaxQueue {
public:
    MaxQueue() {

    }
    
    int max_value() {
        return deque.empty()?-1:deque.front();
    }
    
    void push_back(int value) {
        queue.push(value);
        while(!deque.empty() && deque.back()<value){
            deque.pop_back();
        }
        deque.push_back(value);
    }
    
    int pop_front() {
        if(queue.empty()){
            return -1;
        }
        int val = queue.front();
        queue.pop();
        if(deque.front() == val){
            deque.pop_front();
        }
        return val;
    }

private:
    deque<int> deque;
    queue<int> queue;
};

/**
 * Your MaxQueue object will be instantiated and called as such:
 * MaxQueue* obj = new MaxQueue();
 * int param_1 = obj->max_value();
 * obj->push_back(value);
 * int param_3 = obj->pop_front();
 */
```

## 424. 替换后的最长重复字符
https://leetcode-cn.com/problems/longest-repeating-character-replacement/

模板走起。先入窗，当需要替换的字符大于k的时候，也就是窗口长度-最长重复字符长度>k时，窗口收缩。所以定义一个变量count计算当前窗口最长重复字符的长度。

最后每次窗口右移就更新一下最长重复字符长度。

```C++
class Solution {
public:
    int characterReplacement(string s, int k) {
        unordered_map<char,int> window;

        int left=0,right=0;
        int ans = 0;
        int count = 0;

        while(right<s.size()){
            char c = s[right];
            window[c]++;
            count = max(count,window[c]);

            while(right-left+1 > count+ k){
                char d = s[left];
                window[d]--;
                left++;
            }

            ans = max(ans,right-left+1);
            right++;
        }

        return ans;

    }
};
```

## 1456. 定长子串中元音的最大数目
https://leetcode-cn.com/problems/maximum-number-of-vowels-in-a-substring-of-given-length/

入窗；窗口长度>=k时出窗。出窗时更新最长长度。当出窗和入窗是元音字母的时候，更新当前元音字母数量。

```C++
class Solution {
public:
    int maxVowels(string s, int k) {

        int left=0,right=0;
        int ans = 0;
        int count = 0;

        while(right<s.size()){
            char c = s[right];
            
            if(c == 'a' || c == 'e' || c == 'i' || c == 'o' || c == 'u'){
                count++;
            }

            while(right-left+1 >= k){
                ans = max(ans,count);
                char d = s[left];
                if(d == 'a' || d == 'e' || d == 'i' || d == 'o' || d == 'u'){
                    count--;
                }
                left++;
                
            }
            right++;  
        }
        return ans;


    }
};
```

## 567. 字符串的排列

字符串模板；入窗；如果入窗的字符是子串中的字符，入窗的map对应字符++，表示窗口内有该字符；当窗口内的某一字符数和子串中对应字符的数量相等时，表明子串中有一个字符已经符合结果；

当窗口长度大于子串长度时，开始出窗；当子串所有字符都符合结果，也就是valid==need.size()时，返回true。判断出窗字符是不是在子串中，如果是，更新window；再看出窗字符中window保存的数量是不是子串中保存的数量，如果是，出窗后valid应该--，表明子串中仍有未满足的字符。

最后返回false。

```C++
class Solution {
public:
    bool checkInclusion(string s1, string s2) {
        unordered_map<char,int> need,window;

        for(auto c:s1){
            need[c]++;
        }

        int left=0,right=0;
        int valid = 0;

        while(right<s2.size()){
            char c = s2[right];
            if(need.count(c)){
                window[c]++;
                if(need[c] == window[c]){
                    valid++;
                }
            }
            

            while(right-left+1 >= s1.size()){
                if(valid == need.size()){
                    return true;
                }
                char d = s2[left];
                if(need.count(d)){
                    if(window[d] == need[d]){
                        valid--;
                    }
                    window[d]--;
                }
                left++;
            }
            right++;


        }
        return false;
    }
};
```

## 面试题 17.18. 最短超串
最短子串类问题，框架走起。注意这里说的是返回最左端的端点，开始多想了怎么保证是最左端；后来发现在下面这里就确定了。
```C++
                //right-left<len 就保证了是最左端；如果改为<=len，就是最右端。
                if(right-left < len){
                    start = left;
                    len = right-left;
                }
```
完整代码和上面有一题最短子串也差不多。

```C++
class Solution {
public:
    vector<int> shortestSeq(vector<int>& big, vector<int>& small) {
        unordered_map<int,int> need,window;

        for(auto i:small){
            need[i]++;
        }

        int left=0,right=0;
        int valid = 0;
        int start=0,len=INT_MAX;
        while(right<big.size()){
            int c = big[right];
            right++;

            if(need.count(c)){
                window[c]++;
                if(need[c] == window[c]){
                    valid++;
                }
            }

            while(valid == need.size()){
                if(right-left < len){
                    start = left;
                    len = right-left;
                }
                int d = big[left];
                left++;
                if(need.count(d)){
                    if(window[d] == need[d]){
                        valid--;
                    }
                    window[d]--;
                } 
            }
        }
        if(len == INT_MAX){
            return {};
        }
        return {start,start+len-1};


    }
};
```

## 1248. 统计「优美子数组」
https://leetcode-cn.com/problems/count-number-of-nice-subarrays/

入窗；当奇数数目为k时，出窗；出窗时子数组数量+1；因为题目不是只包含奇数的子数组，所以可以有偶数。但是和上面一些题目不同的是，当奇数数目小于k时，也符合要求，所以我们这时候要把之前所有符合要求的子数组数目都加上。

```C++
class Solution {
public:
    int numberOfSubarrays(vector<int>& nums, int k) {
        int ans = 0;

        int left=0,right=0;
        int count = 0;
        int temp = 0;

        while(right<nums.size()){
            if(count < k){
                int c = nums[right];
                right++;
                if(c % 2 != 0){
                    count++;
                }
            }
            
            if(count == k){
                temp = 0;
                while(count == k){
                    int d = nums[left];
                    ans++;temp++;
                    if(d % 2 != 0){
                        count--;
                    }
                    left++;
                }
            }else if(count < k){
                ans += temp;
            }


        }
        return ans;
    }
};
```

## 992. K 个不同整数的子数组
https://leetcode-cn.com/problems/subarrays-with-k-different-integers/

恰好为K个不同整数的子数组个数 = 最多为K个不同整数的子数组个数 - 最多为K-1个不同整数的子数组个数。

所以问题转化为最多K个不同整数的子数组个数。

所以统计window里面的元素的个数，大于K时出窗，出窗后更新数组长度。

```C++
class Solution {
public:
    int subarraysWithKDistinct(vector<int>& A, int K) {
        if(A.size() == 0 || K == 0){
            return 0;
        }


        return subarraysWithKDistinctMost(A,K) - subarraysWithKDistinctMost(A,K-1);
    }

    int subarraysWithKDistinctMost(vector<int>& A, int K) {
        int left=0,right=0;
        unordered_map<int,int> window;
        int ret = 0;

        while(right<A.size()){
            int c = A[right];
            right++;
            window[c]++;

            while(window.size() > K){
                int d = A[left];
                if(window.count(d)){
                    window[d]--;
                    if(window[d] == 0){
                        window.erase(d);
                    }
                }
                left++;
            }
            ret += right - left + 1;
        }
        return ret;

    }

};
```

## 1695. 删除子数组的最大得分
https://leetcode-cn.com/problems/maximum-erasure-value/

题目的意思其实是找出最大连续子数组的和。。。。。

```C++
class Solution {
public:
    int maximumUniqueSubarray(vector<int>& nums) {
        int l=0,r=0;
        unordered_map<int,int> window;
        int ans = 0;
        int sum = 0;
        while(r<nums.size()){
            sum += nums[r];
            int c = nums[r++];
            window[c]++;

            while(window[c] > 1){
                int d = nums[l];
                sum -= nums[l++];
                window[d]--;
            }

            ans = max(ans,sum);
        }
        return ans;
    }
};
```