---
title: Exploring Sorting Algorithms
---

# Table of Contents

1. [Bubble Sort](#bubble-sort)
2. [Cocktail Sort](#cocktail-sort)
3. [Selection Sort](#selection-sort)
4. [Insertion Sort](#insertion-sort) 
5. [Circle Sort](#circle-sort)
6. [Merge Sort](#merge-sort)
7. [Quicksort](#quicksort)
8. [Summary](#summary)

# Introduction
As I said earlier, I'll be going through a series of sorting algorithms for review and documenting what I see here.

To test, I have created a small playing card program that creates a deck of an arbitrary size, an ArrayList of PlayingCard objects. The initial test is 50,000 cards, increasing as necessary.

# Algorithms

## Bubble Sort

* Best: **O(n)** 
* Average: **O(n^2)** 
* Worst: **O(n^2)**

All this is doing is iterating through the array comparing each element to the next, swapping as necessary. It is complete if no elements are swapped.


~~~java
public static void bubbleSort(List<PlayingCard> deck){
    boolean changed;
    do {
        changed = false;
        for(int a = 0; a < deck.size()-1; a++){
            if(deck.get(a).compareTo(deck.get(a+1))>0){
                swapper(deck, a, a+1);
                changed = true;
            }
        }
    }while(changed);
}
~~~

### Results

This was as much of a pain to wait on as I thought it would be. Almost any other algorithm would be a better choice.

	Total execution time: 61722
	Total execution time: 58754
	Total execution time: 53999
	Total execution time: 54489
	Total execution time: 56068
	Ave.  execution time: 57006

## Cocktail Sort

* Best: **O(n)** 
* Average: **O(n^2)** 
* Worst: **O(n^2)**

I'm not going to bother with this one because it is essentially Bubble Sort but each iteration covers both directions of the array. The complexity is the same, with apparently minor improvements not worth it right now to confirm.


## Selection Sort

* Best: **O(n^2)**  
* Average: **O(n^2)** 
* Worst: **O(n^2)**

Given the asymptotic complexity of this algorithm I shouldn't expect this to work in large arrays. The primary use of this is for when writing data is expensive compared to reading. However, no data has less data movement.

Find the lowest number, switch it with the first element. Find the second-smallest, switch it with the second element, continue until the end is reached.

~~~java
public static void selectionSort(List<PlayingCard> deck){
    int smallestAt;
    for(int current = 0; current < deck.size()-1; current++){
        smallestAt = current;
        for(int check = current+1; check < deck.size(); check++){
            if(deck.get(check).compareTo(deck.get(smallestAt)) < 0){
                smallestAt = check;
            }
        }
        swapper(deck, current, smallestAt);
    }
}
~~~

### Results

50,000:

	Total execution time: 17133
	Total execution time: 15610
	Total execution time: 12665
	Total execution time: 12567
	Total execution time: 13180
	Ave.  execution time: 14231
	
	
## Insertion Sort

* Best: **O(n)**  
* Average: **O(n^2)** 
* Worst: **O(n^2)**

Another **O(n^2)** algorithm that works best with smaller sets but has many improvements over the previous ones here.

The algorithm progresses forward through the array under the assumption that it has already sorted everything before it; it takes an element and checks backward until it finds where to place it, pushing the larger values up as necessary.

According to [Wikipedia](https://en.wikipedia.org/wiki/Insertion_sort), the method is:

* Simple to implement (as seen here)
* Adaptive, i.e., efficient for data sets that are already substantially sorted: the time complexity is O(nk) when each element in the input is no more than k places away from its sorted position
* Stable; i.e., does not change the relative order of elements with equal keys
* In-place; i.e., only requires a constant amount O(1) of additional memory space
* Online; i.e., can sort a list as it receives it

~~~java
public static void insertionSort(List<PlayingCard> deck){
    for(int i = 1; i < deck.size(); i++){
        PlayingCard val = deck.get(i);
        int j = i -1;
        while(j >= 0 && (deck.get(j).compareTo(val) > 0)){
            deck.set(j + 1,deck.get(j));
            j -= 1;
        }
        deck.set(j + 1, val);
    }
}
~~~


### Results

At 50,000 objects:

	Total execution time: 4564
	Total execution time: 3796
	Total execution time: 3728
	Total execution time: 3730
	Total execution time: 4830
	Ave.  execution time: 4129
	
At 100,000 objects:

	Total execution time: 24225
	Total execution time: 24829
	Total execution time: 21696
	Total execution time: 24850
	Total execution time: 24190
	Ave.  execution time: 23958
	

## Circle Sort

* Best: **O(n(log n)^2)**   
* Average: **O(n(log n)^2)**
* Worst: **O(n(log n)^2)** 

Primarily useful for sorting inverted sets of data. According to the [author](https://sourceforge.net/p/forth-4th/wiki/Circle%20sort/), doing up to .5log(n) iterations and then finishing with an Insertion sort is even faster.

This algorithm compares the first element to the last, then the second to the second to last, and so on. It then splits the array and recurses until there is only one element. This is repeated until there are no swaps. If the middle element is stuck in place, bump it forward to get it back to work.

~~~java
public static void circleSort(List<PlayingCard> deck){
    while(circleSortA(deck, 0,deck.size()-1,0) !=0 ){};
}

private static int circleSortA(List<PlayingCard> deck, int lo, int hi, int swaps){
    if(lo==hi){return swaps;}

    int high = hi; int low = lo;
    int mid = (hi-lo)/2;

    while(lo < hi){
        if(deck.get(lo).compareTo(deck.get(hi))>0){
            swapper(deck, lo, hi);
            swaps++;
        }
        lo++;
        hi--;
    }

    if((deck.get(lo).compareTo(deck.get(hi))==0) && (deck.get(lo).compareTo(deck.get(hi+1))>0)){
        swapper(deck, lo, hi+1);
        swaps++;
    }

    swaps = circleSortA(deck, low, low+mid, swaps);
    swaps = circleSortA(deck, low+mid+1, high, swaps);

    return swaps;
}
~~~

### Results

With the same size set as above (50,000), I got the following:

	Total execution time: 223
	Total execution time: 144
	Total execution time: 120
	Total execution time: 115
	Total execution time: 141
	Ave.  execution time: 148
	
	
So I upped it to 100,000 objects.
	
	Total execution time: 445
	Total execution time: 293
	Total execution time: 227
	Total execution time: 276
	Total execution time: 307
	Ave.  execution time: 309
	

## Merge Sort

* Best: **O(n)**   
* Average: **O(n(log n))** 
* Worst: **O(n(log n))** 

Divide and conquer.

Recursively split the array in half until you have one or no elements, then merge the two in the correct order. This algorithm is not in-place as new lists are created and appended to one-another.

~~~java

public static List<PlayingCard> mergeSort(List<PlayingCard> deck){
    if(deck.size() <= 1){return deck;}

    int mid = deck.size()/2;
    List<PlayingCard> left = deck.subList(0, mid);
    List<PlayingCard> right = deck.subList(mid, deck.size());

    right = mergeSort(right);
    left = mergeSort(left);

    return(merge(left, right));
}

private static List<PlayingCard> merge(List<PlayingCard> left, List<PlayingCard> right){
    List<PlayingCard> result = new ArrayList<>();
    Iterator<PlayingCard> it1 = left.iterator();
    Iterator<PlayingCard> it2 = right.iterator();

    PlayingCard x = it1.next();
    PlayingCard y = it2.next();

    while(true){
        if(x.compareTo(y) <= 0){
            result.add(x);
            if(it1.hasNext()){
                x = it1.next();
            }else {
                result.add(y);
                while (it2.hasNext()){
                    result.add(it2.next());
                }
                break;
            }
        }else{
                result.add(y);
                if(it2.hasNext()){
                    y = it2.next();
                }else{
                    result.add(x);
                    while (it1.hasNext()){
                        result.add(it1.next());
                    }
                    break;
                }
            }
    }
    return(result);
}
~~~


### Results

50,000:

	Total execution time: 161
	Total execution time: 94
	Total execution time: 64
	Total execution time: 102
	Total execution time: 87
	Ave.  execution time: 101
	
100,000:

	Total execution time: 238
	Total execution time: 148
	Total execution time: 173
	Total execution time: 92
	Total execution time: 115
	Ave.  execution time: 153
	
1,000,000:

	Total execution time: 2948
	Total execution time: 1002
	Total execution time: 1143
	Total execution time: 1014
	Total execution time: 988
	Ave.  execution time: 1419
	

## Quicksort

* Best: **O(nlog(n))**   
* Average: **O(n(log n))** 
* Worst: **O(n^2)** 

The worst case often occurs if the pivot is the first or last. I got much faster results setting it to the middle-most element.

~~~java
public static List<PlayingCard> quickSort(List<PlayingCard> deck){
    if(!deck.isEmpty()){
        PlayingCard pivot = deck.get(deck.size()/2);

        List<PlayingCard> less = new ArrayList<>();
        List<PlayingCard> more = new ArrayList<>();
        List<PlayingCard> pivotList = new ArrayList<>();

        for (PlayingCard i : deck) {
            if (i.compareTo(pivot) < 0) {
                less.add(i);
            } else if (i.compareTo(pivot) > 0) {
                more.add(i);
            } else {
                pivotList.add(i);
            }
        }

        less = quickSort(less);
        more = quickSort(more);

        less.addAll(pivotList);
        less.addAll(more);
        return less;
    }
    return(deck);
}
~~~

### Results

I upped the runs to 10 becuase it was running so fast and I wanted a better measure.

50,000:

	Total execution time: 226
	Total execution time: 148
	Total execution time: 138
	Total execution time: 152
	Total execution time: 155
	Total execution time: 77
	Total execution time: 42
	Total execution time: 32
	Total execution time: 51
	Total execution time: 62
	Ave.  execution time: 108
	
100,000:

	Total execution time: 210
	Total execution time: 99
	Total execution time: 112
	Total execution time: 102
	Total execution time: 148
	Total execution time: 121
	Total execution time: 134
	Total execution time: 123
	Total execution time: 89
	Total execution time: 147
	Ave.  execution time: 128
	
1,000,000:

	Total execution time: 2671
	Total execution time: 1074
	Total execution time: 989
	Total execution time: 975
	Total execution time: 978
	Total execution time: 968
	Total execution time: 962
	Total execution time: 895
	Total execution time: 948
	Total execution time: 977
	Ave.  execution time: 1143
	

# Summary

I've re-organized the page based on the results table here.

|      | Bubble | Selection | Insertion | Circle | Merge | Quicksort |
|------|--------|-----------|-----------|--------|-------|-----------|
| 50k  | 57006  | 14231     | 4129      | 148    | 101   | 108       |
| 100k | -      | -         | 23958     | 309    | 153   | 128       |
| 1M   | -      | -         | -         | -      | 1419  | 1143      |


**Bubble Sort** should never be used. Ever.

**Selection Sort** should only be used when writing data is expensive. It also has the least amount of data movement.

**Insertion Sort** is simple to implement and has a low overhead; however, it is terrible with large sets. Use only for small *n* and finishing faster sorts.

**Circle Sort** is the best method for sorting a mostly-inverted array. Very similar to merge sort. Is unstable, recursive, parallelizable, and in-place. Potentially do .5log(n) iterations and finish with Insertion sort.

**Merge Sort** is one of the most basic sorting algorithms. It is efficient, recursive, and parallelizable. Divide-then-conquer. Does fewer comparisons than Quicksort: use when complicated comparison routines are used.

**Quicksort** is one of the best-operating sorting algorithms and is easy to implement. The elements must have a strickt weak order. The best pivots create partitions of equal length (differing by at most one); the worst occur when empty partitions are created (pivot is first or last element). Conquer-then-divide. Merging is trivial. 