---
layout: post
title: Sorting algorithms
tags:
- Algorithm
---

This article will summarize about sorting algorithms, how they were named, and why use one over another.  
Full information is in [wikipedia](https://en.wikipedia.org/wiki/Sorting_algorithm#Comparison_of_algorithms) already.  
All opinions below are based on the simplest implementation of these sorting algorithms. There are many improved
versions e.g. in-place Merge Sort, Quick Sort with O(NlogN) worst-case..., which are not mentioned here.

# A. Simple sorting algorithms

## 1. Selection sort
### Concept
Looking into this sorted array: 0,1,2,3,..,n; we can see that the value at index `i` is the i<sup>th</sup> smallest
element. In general word, Selection Sort will go sequentially from 0,1,2 to N.  
- At index 0, find the smallest element in array, by looping from 0 to N; and fill it into index 0.  
- At index 1, find the 2<sup>nd</sup> smallest element, also by looping through whole array. But this time, ignore 
the element at index 0, because it's the smallest element (we need 2<sup>nd</sup> smallest). And fill the result into
index 1.  
- At index 2, find the 3<sup>th</sup> smallest element, ignore index 0 and 1. And so on.  

So, at index `i`, we find smallest element in the sub array from i -> N; it is the i<sup>th</sup> smallest element.

```
Initial:  |3 4 6 1 2 5
i == 0 :  |1 4 6 3 2 5   // 1 is smallest in unsorted sub array. Swap 1 & 3
i == 1 :   1|2 6 3 4 5   // Search from i=1, select 2
i == 2 :   1 2|3 6 4 5   // Search from i=2, Select 3
i == 3 :   1 2 3|4 6 5   // Search from i=3, Select 4
i == 4 :   1 2 3 4|5 6   // Search from i=4, Select 5
i == 5 :   1 2 3 4 5|6   // Search from i=5, Select 6
Done   :   1 2 3 4 5 6   // Select 6
---------------------
 sorted | unsorted
```
### About the name
This algorithm builds sorted list from left to right. At element `i`, it will **select** smallest element in remain part
and swap into position of element `i`.

### Strength
  - Simple
  - In-place sorting (no need addition memory)
  - If the list is small, Selection Sort is faster than other efficient types (Merge, Quick, Heap...)
  - Low number-of-write (linear): efficient when memory-write operation is expensive

### Weakness
  - At each iteration, it needs to scan whole unsorted sub array to find the smallest.
  - O(N<sup>2</sup>)


## 2. Bubble sort
```
0 1 2 3 4   i
---------------------
3 1 5 4 2 | 6 7    // Bubble the biggest
1 3 5 4 2 | 6 7    // j==0, bubble 3
1 3 5 4 2 | 6 7    // j==1, 5 is current biggest
1 3 4 5 2 | 6 7    // j==2, bubble 5 
1 3 4 2 5 | 6 7    // j==3, bubble 5 
1 3 4 2   | 5 6 7  // 5 is in sorted sub array
---------------------
 unsorted | sorted - biggest
```
### About the name
Because elements tend to move up into the correct order like bubbles rising to the surface.

It's bad algorithm, almost have nothing to be recommend.  
Maybe in the case [limited temporary memory](https://stackoverflow.com/a/3274203/6445037), bubble sort can help.
### Compare to Selection Sort
  - Both need to scan whole unsorted sub array at each iteration.
  - But Selection Sort just use 1 single swap when find correct position.
  - Bubble Sort at each iteration has to perform many swaps to bubble up the largest element.


## 3. Insertion sort
### Concept
Insertion Sort goes from index 0 -> N. At index `i`, given that the sub-array `0 -> i-1` was in sorted order, we must
insert element at index `i` into correct position in that sub-array.  
Insertion Sort done that by sequentially **sinking** element `i` down into sub-array:
  - If `array[i] < array[i-1]`, sink it by swapping `array[i]` and `array[i-1]`;
  - If `array[i] >= array[i-1]`, stop. Done sinking.
  - Now sub-array `0 -> i` was sorted. Go to next element `i+1`.

```
          i
---------------------
1 4 6   | 3 2 5   // Pick i
1 4 6 3 | 2 5     // Insert i into left sub array 
1 4 3 6 | 2 5     // 3 < 6 so bubble i
1 3 5 6 | 2 5     // Continue until reach correct position
---------------------
 sorted | unsorted
```
### About the name
This algorithm also builds sorted list from left to right. It will **insert** element `i` to correct position in the left
partition of list.
### Strength
  - Simple
  - In-place sorting (no need addition memory)
  - Stable
  - Compare with Selection Sort:
    - At each element, Selection Sort has to scan ALL remaining elements to find smallest. Insertion Sort in the other
    hand, doesn't scan all, it will stop when correct insert position were found.
    - Insertion Sort supports **online sorting** without knowing size of list, while Selection Sort must know size prior.
  - Online (can sort as receiving list, without knowing list size) 
  - Compared to Bubble Sort:
    - Bubble Sort at each iteration, it needs to scan whole unsorted sub array
    - Insertion Sort at each iteration, doesn't need to scan whole sub array, it stops when find correct position.

### Weakness
  - O(N<sup>2</sup>).
  - Cost more write-operation.

## 4. Cycle sort
### Strength
Optimal total number of writes (better than Selection Sort). Efficient when memory-write operation is expensive, like
flash memory, when write operation will reduce memory lifespan.

# B. Efficient sorting algorithms

## 1. Merge sort
### Concept
Suppose we have an array with 8 elements with index: 1 3 2 0 5 6 7 4. Perform these steps:
1. Break into these smaller sub arrays:
  - [1 3] and [2 0] and [5 6] and [7 4]
2. Sort each sub arrays:
  - [1 3] and [0 2] and [5 6] and [4 7]
3. Merge 2 sub arrays at a time (merge, not concat, google for detail)
  - [0 1 2 3] and [4 5 6 7]
4. Repeat step 3 until we have 1 sorted array:
  - [0 1 2 3 4 5 6 7]

### Strength
  - Stable
  - O(NlogN)
  - High locality

### Weakness
  - Cost more memory for temporary array for merging.
  - Cost more swap/copy operations.

## 2. Quick sort
### Concept
1. Pick an element, called a pivot. There are many methods of picking pivot.
2. Partitioning: reorder the array so that pivot will be in its final position. And:
  - All elements to the left of pivot are smaller than pivot.
  - All elements to the right of pivot are bigger than pivot.
3. Repeat these steps for left-partition and right-partition.

### About the name
At the time of invention, Quick Sort was quicker than other algorithms, so Hoare named it like that.
Apparently, he wasn't totally confidence about it. If that, he could name it Quickest Sort.

### Strength
  - In-place
  - O(NlogN)
  - High locality
  - Use [less amount of swap operations](https://stackoverflow.com/a/28522840/6445037). So it's typically best among
  other O(NlogN) sorting algorithms.

### Weakness
  - Unstable
  - Worst case O(N<sup>2</sup>)

## 3. Heap sort
### Concept
1. From initial array, build a max heap on it. Cost O(N)
2. From max heap, build sorted array by this:
  - Swap max element (root of heap, at index 0) with last element in array at index N. Then last element is in its
  final position.
  - Rebuild heap from [0 -> N-1]. Cost O(logN)
  - Repeat until end. Cost O(N)
Total cost: O(N) + O(NlogN) = O(NlogN)

### Strength
  - In place
  - O(NlogN)

### Weakness
  - Unstable
  - Poor locality
  - If array was already sorted prior, Heap Sort still has to swap ALL elements.