LC Link : https://leetcode.com/problems/roman-to-integer/

Solution :
Own solution
``` c++
class Solution {
public:
    int romanToInt(string s) 
    {
        unordered_map<char,int> umap = 
        {
            {'I', 1},
            {'V', 5},
            {'X', 10},
            {'L', 50},
            {'C', 100},
            {'D', 500},
            {'M', 1000}
        };
        char prev = s[s.size()-1];
        int res = umap[prev];
        for(int i = s.size()-2;i>=0;i--)
        {
            char curr = s[i];
            prev = s[i+1];
            if((prev == 'V' || prev =='X') && (curr == 'I') ||
                (prev == 'L' || prev =='C') && (curr == 'X') ||
                (prev == 'D' || prev =='M') && (curr == 'C'))
                
            {
                res-= umap[prev];
                res+= umap[prev] - umap[curr];
            }
            else
            {
                res+= umap[curr];
            }
        }
        return res;
        
    }
};
```

Optimised solution :

Approach 3: Right-to-Left Pass

Intuition

This approach is a more elegant variant of Approach 1. Just to be clear though, Approach 1 and Approach 2 are probably sufficient for an interview. 

This approach is still well worth understanding though.

In the "subtraction" cases, such as XC, we've been updating our running sum as follows:

> sum += value(C) - value(X)
However, notice that this is mathematically equivalent to the following:

> sum += value(C)
> sum -= value(X)
Utilizing this means that we can process one symbol each time we go around the main loop. We still need to determine whether or not our current symbol should be added or subtracted by looking at the neighbour though.

In Approach 1, we had to be careful when inspecting the next symbol to not go over the end of the string. 

This check wasn't difficult to do, but it increased the code complexity a bit, and it turns out we can avoid it with this approach!

Observe the following:

Without looking at the next symbol, we don't know whether or not the left-most symbol should be added or subtracted.
The right-most symbol is always added. It is either by itself, or the additive part of a pair.
So, what we can do is initialise sum to be the value of the right-most (last) symbol. Then, we work backwards through the string, starting from the second-to-last-symbol. We check the symbol after (i + 1) to determine whether the current symbol should be "added" or "subtracted".
```
last = s.length - 1
total = value(last)

for i from last - 1 down to 0:
    if value(s[i]) < value(s[i+1]):
        total -= value(s[i])
    else:
        total += value(s[i])
return sum
```
Because we're starting at the second-to-last-index, we know that index i + 1 always exists. 

We no longer need to handle its potential non-existence as a special case, and additionally we're able to (cleanly) use a for loop, as we're always moving along by 1 index at at time, unlike before where it could have been 1 or 2.

```
class Solution {
public:
    int romanToInt(string s) 
    {
        // optimised solution
        unordered_map<char,int> umap = 
        {
            {'I', 1},
            {'V', 5},
            {'X', 10},
            {'L', 50},
            {'C', 100},
            {'D', 500},
            {'M', 1000}
        };
        
        int curr;
        int prev = umap[s[s.size()-1]];
        int res = prev;
        for(int i = s.size()-2;i>=0;i--)
        {
            curr = umap[s[i]];
            res += (curr < prev ) ? -curr : + curr;
            prev = curr;
        }
        return res;
        
    }
};
```
