---
title: tt-3 -- Deserunt minim anim deserunt nostrud ex ipsum.ddddd
slug: new-ll-33
tags: reactjs, css, python, nodejs
domain: klimbee.hashnode.dev
hideFromHashnodeCommunity: true
---

TLDR: Three alternative methods for [LeetCode 6348](https://leetcode.com/problems/take-gifts-from-the-richest-pile/). Best complexity is O(N).

The input is a collection of \\(N\\) numbers and iteration k. At each iteration, we need pick the largest number \\(x\\), put \\(\sqrt{x}\\) back to the collection,  and take away \\(x-\sqrt{x}\\) . The output is sum of remaining numbers after \\(k\\) iterations.

## Method 1. Direct

Simulate the aformentioned process directly. The complexity is \\(O(Nk)\\). Actual LC time is 300ms.

```python
class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        while k > 0:
            k -= 1
            m = float('-inf')
            mi = None
            for i, g in enumerate(gifts):
                if g > m:
                    m = g
                    mi = i
            gifts[mi] = int(m ** 0.5)
        return sum(gifts)
```

## Method 2. Heap

We still use the simulation approach for \\(K\\) iteration, but could optimise the internal loop with a heap. The heap building process takes \\(O(N)\\) time. At each iteration:

*   Retrieve the min value is \\(O(1)\\)
*   Reorganize the array into heap takes \\(O(\log N)\\)

Python has the built in `heapq` implementation. Note that the implementation is a min-heap, so we need to negate the elements to make a max heap.

The overall complexity is \\(O(N + k \log N)\\). Actual LC time is 48ms.

```python
class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        # min heap implementation
        import heapq
        gifts = [-g for g in gifts]
        heapq.heapify(gifts)
        while k > 0:
            k -= 1
            heapq.heapreplace(gifts, -int((-gifts[0]) ** 0.5))
        return -sum(gifts)
```

## Method 3a. Element bound

Every time, we put some value back and take the remaining one. So at a certain iteration, we pick some number of \\(X\_i\\) , or \\(\sqrt{X\_i}\\),  or \\(\sqrt{\sqrt{X\_i}}\\), or \\(\sqrt{\sqrt{\sqrt{X\_i}}}\\), ... The value we take out is:

$$
\begin{align}Y*{i,1}&=X_i-\sqrt{X\_i} \ Y*{i,2}&=\sqrt{\sqrt{X\_i}}-\sqrt{\sqrt{\sqrt{X\_i}}} \end{align}
$$

The process basically takes out top \\(k\\) values of all \\(Y\_{i,j}\\).

One key observation is that the element \\(X\\) , from input `gifts[i]` is bounded by \\(X \le 10^9\\) . So the index \\(j\\) is well bounded by \\(\log\_2X \approx 30\\), which is a small constant.

The preprocess of \\(X\\) into \\(Y\\) costs \\(O(30N)\\).

We can use heap like in Method 2 to get the top \\(k\\) values. However, it is not an improvement.

The key difference here is that, in Method 2, new values are constructed in an online fashion, so heap is the goto soluton for top-k queries. In **Method 3, thanks to the preprocess, it is now an offline problem**, i.e. all the values are known in one go.

For ease of implementation, we can just sort and get first k values. Or we can do more sophisticated treatment in next method.

The sort implementation is as follows.

The overall complexity is \\(O(N+N \log N)\\). Actual LC time is 55ms.

```python
class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        # method 3
        nums = [] # nums we can take out, i.e. x - sqrt(x)
        for g in gifts:
            while g >= 2:
                gg = int(sqrt(g))
                nums.append(g - gg)
                g = gg
        nums.sort(reverse=True)
        return sum(gifts) - sum(nums[: k])
```

## Method 3b. Partial Quik Sort

A classical method for solving top-K of offline array is partial Quick Sort. At each iteration, we group elements to be smaller than or larger than pivot value. Unlike in a full quick sort algorithm, we only need to branch into one direction, depending on the size of left and right, and their relationship to \\(k\\). This process is worth another post to detail. When the pivot is randomly picked, we have a good chance to divide array into equal halves. So recursion cost of each layer is \\(X\\), \\(\frac{X}{2}\\), \\(\frac{X}{2^2}\\),  \\(\frac{X}{2^3}\\), ... which is \\(2X\\).

I have developed a routine `kth_min_inplace()` and used it for years in similar problems. The routine operate the input array in-place, and put the smallest k numbers into first k elements. This routine is able to handle the situation when there are duplicate values.

The complexity is: \\(O(30N + 2N)\\), i.e. \\(O(N)\\) after removing constant. Actual LC time is 87ms.

```python
def kth_min_inplace(arr, l, r, k):
    '''
    Arrange the smallest k elements of arr[l: (r+1)]
    into the range arr[l: (l+k)]
    
    '''
    # print(arr, l, r, k)
    
    import random
    # pivoti = l 
    pivoti = random.randint(l, r)
    pivot = arr[pivoti]
    
    i, j = l, r
    arr[pivoti], arr[l] = arr[l], pivot
    # iteration invariant:
    #   - Keep those who are smaller than pivot on the left side
    #   - i points the latest position of pivot
    ec = 0 # equal count on the left side of pivot
    while i < j:
        if pivot < arr[j]:
            j -= 1
        else:
            arr[i] = arr[j]
            i += 1
            arr[j] = arr[i]
            if pivot == arr[j]:
                ec += 1
    arr[i] = pivot
    q, p = 0, i - 1
    while q < p:
        if arr[p] == pivot:
            p -= 1
        elif arr[q] == pivot:
            arr[p], arr[q] = arr[q], arr[p]
        else:
            q += 1
    
    # print(arr, i, pivot)
    
    di = i - l
    if di == k - 1:
        return
    elif di < k - 1:
        kth_min_inplace(arr, i + 1, r, k - (di + 1))
    else:
        # di > k - 1
        if di - (k - 1) > ec:
            kth_min_inplace(arr, l, i - ec, k)
        else:
            # answer is already in the equal count region
            pass


class Solution:
    def pickGifts(self, gifts: List[int], k: int) -> int:
        # method 3b.
        nums = [0] * k # nums we can take out, i.e. x - sqrt(x)
        for g in gifts:
            while g >= 2:
                gg = int(sqrt(g))
                # negate to leverage kth_min algorithm
                nums.append(-(g - gg)) 
                g = gg
        kth_min_inplace(nums, 0, len(nums) - 1, k)
        # negate sum of selected as elements are negated before
        return sum(gifts) - (-sum(nums[: k]))         
```

## Reality check

Method 3b is supposedly the theoretically best one but runs slower than Method 2. On the other hand, Method 3a is worse than 2 or 3b, but benefits from the efficient built-in implementation of `sort`.

We can easily come up with the case to show the linear complexity of Method 3b prevails. Just set smaller N and larger K.

Here is a construction:

```python
X = 10**9
N = 10
K = 10**3

x = 2
while x * x < X:
  x = x * x

gifts = [x] * N
```

One would find that above implementation of 3b is still slower. This is due to the line `nums = [0] * k`. I prepare k 0's in order to fit into the template `kth_min_inplace` algorithm. This results in a complexity of \\(O(N+K)\\) in stead of \\(O(N)\\). We can do a quick check before invoking the algorithm. If the `nums[]` array contains less than k, we just take them all. After this tweak, we can see good improvement of Method 3b.

*   Heap: 405 µs ± 14.7 µs
*   Element bound + partial quick sort: 32.8 µs ± 766

More details can be found in this [colab](https://colab.research.google.com/drive/1i8FlJXyQ5FnuH4NPqRA8XQXO7QL3q4bD#scrollTo=XxcO-dBYC-94) .

## About

Sometimes I like "easy" problem more than medium or hard ones on LC. There are great room for improvement when adding or removing constraints. Also we might have good alternatives for thoughts. Medium or hard ones often come with one single accepted approach, depending on whether one know the algorithm or not.

I may not consistently blog about LC. Organising thoughts in this format costs me 2 hours than writing them in free flow (30min). However, if anyone is interseted in the above content in raw format, please let me know. I may consider to package my 800+ python files as subscriber perks. Most of those python files include a doc string detailing my thoughts on the fly. The language may not be edible. The comments may not be consistent with the implementation. Nevertheless, it is good reference of the thinking strategy, and alternative solutions.

$$\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}$$

$$
\sum_{i=0}^n i^2 = \frac{(n^2+n)(2n+1)}{6}
$$
