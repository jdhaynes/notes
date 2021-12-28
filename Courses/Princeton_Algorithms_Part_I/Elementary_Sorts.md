# Elementary Sorts
Algorithms, Part I (Princeton, Coursera), Week 2

## Selection Sort
A simple but slow elementary sorting algorithm.

### Algorithm
Scan left to right of the array, iterating through each item. On each iteration, find the item with the smallest value on the right hand side of the current item, and exchange them. Iterate until the end ($n$ iterations).

This algorithm maintains two invariants:
1. Items to the left of the current item (inclusive) are in ascending order, and fixed.
2. No item to the right of current item is smaller than any entry to the left of the current item.

### Java Code
```java
public class SelectionSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            int min = i;
            for (int j = i + 1; j < N; j++)
                if (less(a[j], a[min]))
                    min = j;
            exch(a, i, min);
        }
    }
    private static boolean less(Comparable v, Comparable w) {
        return v.compareTo(w) < 0;
    }
    private static void exch(Comparable[] a, int i, int j) {
        Comparable swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }
}
```

### Analysis
Selection sort is slow as it always runs at quadratic time $O(N^2)$, even if the input is already sorted. However, data movement is minimal as the number of exchanges is linear $O(N)$ with respect to input size.

| Operation | Best Case | Worst Case |
|-|-|-|
| Comparison | $N^2/2$ | $N^2/2$ |
| Exchange | $N$ | $N$ |

The number of comparison operations is derived from a finite arithmetic series $(N-1)+(N-2)+\cdots+1$.


## Insertion Sort
A slight improvement on selection sort, except now allows improved runtime depending on the input.

### Algorithm
Scan left to right of the array, iterating through each item. On each iteration, if the current item is less than the item on the left, exchange them. Keep exchanging the item with the one on its left until it is larger, in which case it is the correct sorted position for the current iteration. Continue iteration for each item until the end of the array.

This algorithm maintains two invariants:
1. Items to the left of the pointer (inclusive) are in ascending order.
2. Items to the right of the pointer have not been seen yet.

### Java Code
```java
public class InsertionSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++)
            for (int j = i; j > 0; j--)
                if (less(a[j], a[j - 1]))
                    exch(a, j, j - 1);
                else break;
    }
    private static boolean less(Comparable v, Comparable w) {
        return v.compareTo(w) < 0;
    }
    private static void exch(Comparable[] a, int i, int j) {
        Comparable swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }
}
```

### Analysis
Insertion sort in the worst case guarantees quadratic time $O(N^2)$ but is an improvement over selection sort in that is can be more efficient depending on the input. 

Insertion sort works well for non-random arrays, even at large sizes. The closer an item is to the position is it meant to be in to be sorted, the less exchanges it takes to get there.

| Operation | Best Case | Worst Case |
|-|-|-|
| Comparison | $N-1$ | $\frac{N^2+N-2}{2}=\sim\frac{1}{2}N^2$ |
| Exchange | $0$ | $\frac{N^2+N-2}{2}=\sim\frac{1}{2}N^2$ |

The best case is the input is already sorted, and the worst case is that the array is in descending order with no duplicates. 

#### Randomly-Ordered Arrays
For randomly-ordered arrays, it is expected that each item will have to move about half the way back on average. Therefore, the runtime of this will be:

| Operation | Number of Operations | 
|-|-|
| Comparison | $\sim\frac{1}{4}N^2$ | 
| Exchange | $\sim\frac{1}{4}N^2$ | 


#### Partially-Sorted Arrays
The number of exchanges depends on how unordered the input is. A partially assorted array is one where it is almost sorted not not quite (no mathemetical definition for a single array.


The number of exchanges is equal to the number of inversions in the input - i.e. how many pairs of unordered keys there are. This is because an exchange only occurs if two items being compared are out of order, so exchanging them reduces the number of inversions by one.

## Shellsort
* Uses a series of modified insertion sorts, called h-sorts, where instead of comparing and exchanging items adjacent to eachother, it does it for items $h$ positions apart. Insertion sort is the same as h-sorting with $h=1$.
* Shellsort uses a sequence of h-sorts with decreasing values of $h$, making the input more sorted at each step. By $h=1$, the array is almost sorted so it is quick.
* Starting off at big $h$ values, the fact the array is unsorted is offset that not many exchanges are needed due to the big steps.

### Gap Sequences for $h$
There are many sequences of $h$ that could be used, and finding the most efficient one is still an open problem and difficult.

An easy sequence to compute is $3x-1$ (1, 4, 13, 40, 121, 364).

### Java Code
```java
public class ShellSort {
    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        # Compute the gap series.
        while (h < N / 3) h = 3 * h + 1; // 1, 4, 13, 40, 121, 364, ...
        while (h >= 1) { // h-sort the array.
            for (int i = h; i < N; i++) {
                for (int j = i; j >= h && less(a[j], a[j - h]); j -= h)
                    exch(a, j, j - h);
            }

            h = h / 3;
        }
    }
    private static boolean less(Comparable v, Comparable w) {
        return v.compareTo(w) < 0;
    }
    private static void exch(Comparable[] a, int i, int j) {
        Comparable swap = a[i];
        a[i] = a[j];
        a[j] = swap;
    }
}
```

### Analysis
The analysis and time complexity of shell sort is difficult and still an open problem. Runtime is heavily dependant on the gap sequence used.

However, for the $3x-1$ gap sequence, the worst case number of compares used is $O(N^{3/2})$.

### Shellsort in Practice
* Fast unless the array is huge.
* Useful for small subarrays.
* Useful for embedded systems or programs with memory constaints as due to the little amount of code needed for a fairly efficient sorting algorithm.
* Simple algorithm, but plently of questions relating to its analysis.

## Shuffling
Shuffling is the process of randomly re-ordering items in an array to produce a uniformally random permutation.

### Naive Algorithm
A naive but incorrect algorithm is to go through each item in the array and swap it with another random item in the array by generating a random number between $0$ and $n$ to use as the index.

However, this does not produce a uniformally random permutation, as some permutations are more likely occur them others as multiple swaps of the same item are possible. This is because on the outer loop there are $n$ items to swap, each of which can swap with $n$ possible items, leading to $n^2$ possible permutations. Combinatorics states that for a set of $n$ items, there are $n!$ possible permutations. As $n^2$ is larger than $n!$ and $n!$ is not divisble by $n^2$, not all permutations will occur equally.

### Knuth Shuffle
A modern in-place version of the Fisher-Yates algorithm. It produces a uniformally random shuffle with $n!$ possible permutations.

It mimics the process of putting all the items in a bag and picking out one item at a time. After each item is removed from the bag, the number of items that can be picked is reduced by one. 

#### Algorithm
Loop through each item in the array. On each iteration of the array, choose a random integer between $i$ and the end of the array $n-1$, and swap $i$ with the item at that index. 

Each item in the array is considered for shuffling and is exchanged with another item in the array. However, this item is now removed from consideration in future swaps, meaning that it's position is now fixed and the number of items available for swapping is reduced by one.

#### Java Code
```java
public static void knuthShuffle(Object[] a) {
    int n = a.length;
    for (int i = 0; i < n; i++) {
        // choose index uniformly in [i, n-1]
        int r = i + (int)(Math.random() * (n - i));
        Object swap = a[r];
        a[r] = a[i];
        a[i] = swap;
    }
}
```

#### Analysis
The algorithm performs $N$ exchanges with a runtime of $O(N)$.

As the number of items that can be shuffle is reduced by one at each iterations, the number of possible permutations is $n(n-1)(n-2)(n-3)\cdots1=n!$.

## Convex Hull - Application of Sorting
* In computational geometry, the convex hull is the smallest perimeter fence enclosing all points in a set of $N$ points.
* Analogy is to hammer nails on to a board and stretch an elastic band around them.
* The output of the convex hull is the set of points that make up the hull, all of which are also in the set.
* Some points may be on the boundary of the the convex hull, but not part of it.
* Useful for motion planning, to find the shortest route around an obstacle.

### Graham Scan Algorithm
The Graham scan algorithm exploits some properties of the convex hull:
* The convex hull can be traversed making only counterclockwise turns.
* Taking the point $p$ with the lowest $y$-coordinate, the verticles of the convex hull appear in increasing polar angle from $p$.

#### Algorithm
1. Identify point $p$ with the smallest $y$-coordinate by defining a total order. If multiple exist, choose the one with the smallest $x$-coordinate.
2. Calculate the polar angle between $p$ and all other points.
3. Sort points in order of polar angle from $p$. Any general purpose sorting algorithm can be used, such as mergesort ($O(N\log N$)).
4. Consider each point in order whether it makes a counterclockwise turn. If it doesn't, discard it as it doesn't lie inside the convex hull.

A stack can be used to add points as they are considered and pop them if they are discarded.

##### Determining Counterclockwise Movement
Without computing an exact angle, the cross-product can be used to determine if three points $a,b,c$ produce a counterclockwise turn. It produces a $2x$ signed area of the triangle formed by the three points.

$Area(a,b,c)=(b_x-a_x)(c_y-a_y)-(b_y-a_y)(c_x-a_x)$

* If $Area(a,b,c)>0$, then counterclockwise.
* If $Area(a,b,c)<0$, then clockwise.
* If $Area(a,b,c)=0$, then collinear.

```java
public static int ccw(Point2D a, Point2D b, Point2D c) {
    double area2 = (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
    if (area2 < 0) return -1; // clockwise
    else if (area2 > 0) return +1; // counter-clockwise
    else return 0; // collinear
}
 ```