# Union Find (Disjoint Sets)
Algorithms, Part I (Princeton, Coursera), Week 1

## Overview
Union find is a data structure that maintains information about whether two nodes in a set are connected. It is used to solve dynamic connectivity problems.

## Dynamic Connectivity
A dynamic connectivity data structure maintains information about what 

### Assumptions
1. An object is connected to itself.
2. The connection is non-directional.
3. Objects can be indirectly connected through other objects, as long as a connected path exists between them.

## Union Find
* Union find is an approach to dynamic connectivity by partitioning the set into non-overlapping disjoint subsets (components).
* Objects within each disjoint set are connected. They are not connected to objects in another disjoint set.

### Main Operations
1. `union` connects two objects by placing them and they objects they are connected to into the same component.
2. `find` determines if two objects are in the same component, and so are connected.

### Implementations
1. Quick find.
2. Quick union.
3. Weighted quick union.
4. Quick union with path compression.
5. Weighted quick union with path compression.

## Quick Find
* An eager approach - all the work happens during `union` rather than `connected`.
* Store an array containing the ID of the component each object belongs too. Initialise as the index value so each object belongs to its own component.

`union` changes the component ID of all objects with the same component ID of $p$ to that of $q$ (or vice versa, as long as it is consistent).

`connected` checks if the component ID of $p$ and $q$ are equal.


### Java Code
```java
public class QuickFindUF
{
    private int[] id;
    
    public QuickFindUF(int N) {
        id = new int[N];
        for (int i = 0; i < N; i++)
            id[i] = i;
        }

    public boolean connected(int p, int q) { 
        return id[p] == id[q]; 
    }
 
    public void union(int p, int q) {
        int pid = id[p];
        int qid = id[q];
        for (int i = 0; i < id.length; i++) {
            if (id[i] == pid) id[i] = qid;
        }
    }
}
```

### Analysis
| Operation | Worst Case | Explanation |
| --------- | ---------- | ----------- |
| `initialize` | $O(n)$ | The value for each item of array size $n$ must be initialised. |
| `union`   | $O(n)$   | The number of array access for one union is $n$. |
| `connected` | $O(1)$ | Constant time as two array accesses. |

This algorithm is slow and considered bad, as the worst case scenario for $n$ `union` operations is $O(n^2)$.

## Quick Union
* A lazy approach - all the work happens when it is needed in `connected`, but is avoided before.
* Objects are arranged into trees, where each tree represents a component. 
* In code, the objects are stored as a 1D array. The value is equal to the index of the parent object. The value of the root object is the index of itself.

`union` sets the ID of $p$'s root to the ID of $p$'s root.

`connected` checks if $p$ and $q$ have the same root.

### Java Code
```java
public class QuickUnionUF
{
    private int[] id;

    public QuickUnionUF(int N) {
        id = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
        }
    }

    private int root(int i)
    {
        while (i != id[i]) {
            i = id[i];
        }
        return i;
    }

    public boolean connected(int p, int q) {
        return root(p) == root(q);
    }

    public void union(int p, int q)
    {
        int i = root(p);
        int j = root(q);
        id[i] = j;
    }
}
```

### Analysis
| Operation | Worst Case | Explanation |
| --------- | ---------- | ----------- |
| `initialize` | $O(n)$ | The value for each item of array size $n$ must be initialised. |
| `union`   | $O(n)$   | The worst case is that the height of the tree is $n$, so is $2n$ for both root finding operations. |
| `connected` | $O(n)$ | The worst case is that the height of the tree is $n$, so is $2n$ for both root finding operations. |

* Quick union an improvement over quick find in some cases. Quick find's `union` operation always performs $n$ array accesses, whereas quick union's worst case is $n$ accesses but otherwise will be quicker.
* Trees can get tall (worse case $n$), so root finding operations for `union` and `connected` can become expensive.
* In the worst case where there is a single component and the height of the tree is $n$ (a single vertical line), the total number of array accesses for `union` on each pair of objects is quadratic.

$$2i+2\text{ for all }i\\

=2(1+2+\cdots+n)\sim n^2.$$

## Weighted Quick Union
* Modification to quick union to avoid tall trees.
* Works by linking the root of the smaller tree to the root of the larger tree.
* Size of tree defined by number of objects, but height also acceptable.
* Maintain extra array `sz[i]` for size of tree rooted at `i`.

`union` 
* Link the root of the smaller tree to the root of the larger tree.
* Update the tree size array `sz`.

`connected` same as quick union.

### Java Code
```java
public class WeightedQuickUnionUF
{
    private int[] id;
    private int[] sz;

    public QuickUnionUF(int N) {
        id = new int[N];
        sz = new int[N];
        for (int i = 0; i < N; i++) {
            id[i] = i;
            sz[i] = 1;
        }
    }

    private int root(int i)
    {
        while (i != id[i]) {
            i = id[i];
        }
        return i;
    }

    public boolean connected(int p, int q) {
        return root(p) == root(q);
    }

    public void union(int p, int q)
    {
        int i = root(p);
        int j = root(q);

        if (i == j) { return; }
        if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; }
        else { id[j] = i; sz[i] += sz[j]; } 
    }
}
```

### Analysis
| Operation | Worst Case | Explanation |
| --------- | ---------- | ----------- |
| `initialize` | $O(N)$ | The value for each item of array size $n$ must be initialised. |
| `union`   | $O(\log_2 N)$   | See below. |
| `connected` | $O(\log_2 N)$ | See below. |
Note: $\log_2$ is also sometimes written as $\lg$.

**Proposition**: the depth of any node $x$ is at most $\log_2 N$.

**Proof**: The depth of $x$ increases when tree $T_1$ containing $x$ is merged on to a smaller tree $T_2$. As this can only happen if $|T_2|\geq |T_1|$, the size of the resulting tree containing $x$ at least doubles. Therefore $2^i\leq N$ (the tree doubles $i$ times). Rearranging this gives, $i\leq\log_2 N$.

## Weighted Quick Union with Path Compression
* Further performance modification on quick union.
* When computing the root for an object, set the ID of each node along the way to that root.
* Keeps the tree flat and speeds up root finding.

There are two possible implementations.

1. **Two-pass**: add a second loop in `root` to update the `id[]` of each examinated object on the way to the root.

2. **One-pass**: make every other node in the path point to it's grandparent, thereby halving the path length. See Java code below.

```java
private int root(int i)
{
    while (i != id[i])
    {
        id[i] = id[id[i]];
        i = id[i];
    }
    return i;
}
```
### Analysis
The analysis of this algorithm is quite complex. It was shown by Hopcroft-Ulman & Targan that $M$ union-find operations on $N$ objects takes $\leq c(N+M\lg^*N)$ array accesses. This means that in theory, it is nearly, but not quite, linear. However, in practice it is linear.

## Summary of Union Find Implementations

| Algorithm | `initialize` | `union` | `find` | Comments |
|-|-|-|-|-|
| Quick find | $N$ | $N$ | $1$ | Union always take $N$ array accesses.
| Quick union | $N$ | $N$ | $N$ | Union-find guarantees at $N$ array accesses in worst-case scenario, but can be less.
| Weighted QU | $N$ | $\log_2 N$ | $\log_2 N$ |
| Weighted QU + PC | $N$ | $\lg^* N$ | $\lg^* N$| Amortized cost. |

## Applications of Union Find
* Percolation
* Dynamic connectivity
* Least common ancestor
* Hinley-Milner polymorphic type inference
* Kruskal's minimum spanning tree algorithm

### Example: Percolation
* Percolation of a system refers a state when a connected route is fully available through the system.
* Used to model many physical processes.
* Example: a porous landscape with water on top. The system percolates if pores open so that a channel opens to allow water to flow from the top to the bottom of the system.
* Union-find used to check if sites at top of system are connected to those at the bottom. To avoid checking each combination, can use a virtual site at top and bottom and connect all nodes at each end to it.