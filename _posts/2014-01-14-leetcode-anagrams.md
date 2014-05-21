---
layout: post
title: "LeetCode: anagrams"
description: "a simple solution for anagrams in leetcode"
category:
tags: [Programming, Algorithms]
---
{% include JB/setup %}

[LeetCode这个题目](http://oj.leetcode.com/problems/anagrams/)想出来一个好办法，题目的意思是输入一组字符串，把他们按照Anagrams归组出来，
Anagrams的意思是字母相同，排列不同的两个字符串。

{% highlight sh %}
比如：
aabc
baac
cbaa
{% endhighlight %}

这些都是anagrams的。如果两个字符串是满足这种关系的，那么把字符串排序后的结果一定相同。因此想到用一个map去存来。


{% highlight c++ %}
class Solution {
public:
    vector<string> anagrams(vector<string> &strs) {
        typedef map<string, vector<string> > Dict;
        vector<string> res;
        Dict S;
        for(int i=0; i<strs.size(); i++) {
            string tmp = strs[i];
            sort(tmp.begin(), tmp.end());
            if(S.find(tmp) == S.end()) {
                S[tmp] = vector<string>(1, strs[i]);
            } else {
                S[tmp].push_back(strs[i]);
            }
        }

        for(Dict::iterator it = S.begin(); it != S.end(); ++it) {
            vector<string>& vec = it->second;
            if(vec.size() <= 1) continue;
            res.insert(res.begin(), vec.begin(), vec.end());
        }
        return res;
    }
};
{% endhighlight %}
