==============================================================
EXPERIMENT 2
CONSTRUCTION OF MIN HEAP AND MAX HEAP USING ARRAYS
==============================================================

AIM
---
To construct a Min Heap and a Max Heap using arrays, delete an
element, and display the contents of the heaps.

THEORY
------
A heap is a complete binary tree stored in an array.

  - Min Heap: Every parent is smaller than or equal to its
    children, so the smallest element is at the root.
  - Max Heap: Every parent is greater than or equal to its
    children, so the largest element is at the root.

For the element at index i:
  - Left child  = 2i + 1
  - Right child = 2i + 2
  - Parent      = (i - 1) // 2

Python's heapq module only provides a Min Heap. A Max Heap is
made by storing the numbers as negatives.

Time complexity:
  - Insertion          : O(log n)
  - Deleting an element: O(n) to find it, then heapify to rebuild

ALGORITHM
---------
1. Create two empty lists, min_heap and max_heap.
2. Insert each element into min_heap using heappush.
3. Insert the negative of each element into max_heap.
4. Print both heaps (negate the values to show the Max Heap).
5. To delete an element, remove it from both lists.
6. Call heapify to restore the heap property.
7. Print both heaps again.

PROGRAM
-------
import heapq

min_heap, max_heap = [], []
elements = [40, 10, 30, 50, 20]
print("Inserting elements:", elements)

for e in elements:
    heapq.heappush(min_heap, e)
    heapq.heappush(max_heap, -e)      # max heap stored as negatives

print("Min Heap:", min_heap)
print("Max Heap:", [-x for x in max_heap])

val = 30
print(f"\nDeleting {val}...")
min_heap.remove(val)
max_heap.remove(-val)
heapq.heapify(min_heap)
heapq.heapify(max_heap)

print("Min Heap after deletion:", min_heap)
print("Max Heap after deletion:", [-x for x in max_heap])

OUTPUT
------
Inserting elements: [40, 10, 30, 50, 20]
Min Heap: [10, 20, 30, 50, 40]
Max Heap: [50, 40, 30, 10, 20]

Deleting 30...
Min Heap after deletion: [10, 20, 50, 40]
Max Heap after deletion: [50, 40, 10, 20]

RESULT
The Min Heap and Max Heap were constructed using arrays, an element
was deleted, and the contents of both heaps were displayed


==============================================================
EXPERIMENT 4
FINDING BI-CONNECTED COMPONENTS IN A GRAPH
==============================================================

AIM
---
To write a program for finding the bi-connected components in a
given graph.

THEORY
------
  - A graph is bi-connected if it stays connected after removing
    any single vertex.
  - An articulation point (cut vertex) is a vertex whose removal
    disconnects the graph.
  - A bi-connected component is a maximal sub-graph that has no
    articulation point.

The algorithm uses DFS with two values for each vertex:
  - disc[u] : Discovery time of u.
  - low[u]  : Lowest discovery time reachable from u through the
              DFS subtree and one back edge.

Edges are pushed on a stack during DFS. If low[v] >= disc[u] for
a child v, then u separates that part. The edges are popped from
the stack up to (u, v) and form one component.

Time complexity: O(V + E)

ALGORITHM
---------
1. Build the graph using adjacency lists.
2. Set disc[] and low[] of all vertices to -1.
3. Start DFS from an unvisited vertex u.
4. Set disc[u] = low[u] = current time.
5. For each neighbour v of u:
     a. If v is not visited, push edge (u, v) on the stack and
        call DFS on v. Then update low[u] = min(low[u], low[v]).
        If low[v] >= disc[u], pop edges up to (u, v). These
        edges form one bi-connected component.
     b. If v is already visited (and is not the parent), it is a
        back edge. Push it on the stack and update
        low[u] = min(low[u], disc[v]).
6. Repeat for all unvisited vertices.
7. Print all the components.

PROGRAM
-------
from collections import defaultdict

graph = defaultdict(list)
for u, v in [(0,1), (1,2), (2,0), (1,3), (3,4)]:
    graph[u].append(v)
    graph[v].append(u)

n = 5
disc, low = [-1] * n, [-1] * n
stack, components, timer = [], [], [0]

def dfs(u, parent):
    disc[u] = low[u] = timer[0]
    timer[0] += 1
    for v in graph[u]:
        if disc[v] == -1:
            stack.append((u, v))
            dfs(v, u)
            low[u] = min(low[u], low[v])
            if low[v] >= disc[u]:             # u is an articulation point
                comp = []
                while True:
                    e = stack.pop()
                    comp.append(e)
                    if e == (u, v):
                        break
                components.append(comp)
        elif v != parent and disc[v] < disc[u]:   # back edge
            stack.append((u, v))
            low[u] = min(low[u], disc[v])

for i in range(n):
    if disc[i] == -1:
        dfs(i, -1)

for i, comp in enumerate(components, 1):
    print(f"Biconnected Component {i}: {comp}")

OUTPUT
------
Biconnected Component 1: [(3, 4)]
Biconnected Component 2: [(1, 3)]
Biconnected Component 3: [(2, 0), (1, 2), (0, 1)]

RESULT
------
The bi-connected components of the given graph were found
successfully using DFS.



==============================================================
EXPERIMENT 6
SINGLE SOURCE SHORTEST PATH USING ADJACENCY MATRIX
AND ADJACENCY LIST (GREEDY METHOD)
==============================================================

AIM
---
To compare the performance of Single Source Shortest Paths using
the Greedy method when the graph is represented by an adjacency
matrix and by adjacency lists.

THEORY
------
Dijkstra's algorithm is a greedy algorithm. At each step it picks
the unvisited vertex with the smallest known distance and relaxes
its edges.

Comparison:

Representation        | Time Complexity   | Best For
----------------------|-------------------|--------------
Adjacency Matrix      | O(V^2)            | Dense graphs
Adjacency List + heap | O((V + E) log V)  | Sparse graphs

  - Adjacency matrix: To find the neighbours of a vertex, the
    whole row of V entries is scanned.
  - Adjacency list: Only the real neighbours are visited.

Both representations give the same shortest distances. Only the
running time differs.

ALGORITHM
---------
1. Set dist[source] = 0 and dist of all other vertices = infinity.
2. Put (0, source) in a min priority queue.
3. Remove the vertex u with the smallest distance from the queue.
4. If the distance is out of date, skip it.
5. For each neighbour v of u:
     if dist[u] + weight(u, v) < dist[v], update dist[v] and push
     (dist[v], v) into the queue.
6. Repeat steps 3 to 5 until the queue is empty.
7. Print dist[].

PROGRAM
-------
import heapq

matrix = [[0, 2, 0, 6, 0],
          [2, 0, 3, 8, 5],
          [0, 3, 0, 0, 7],
          [6, 8, 0, 0, 9],
          [0, 5, 7, 9, 0]]

alist = [[(1, 2), (3, 6)],
         [(0, 2), (2, 3), (4, 5)],
         [(1, 3), (4, 7)],
         [(0, 6), (1, 8), (4, 9)],
         [(1, 5), (2, 7), (3, 9)]]

def dijkstra(neighbors, n, src):
    dist = [float('inf')] * n
    dist[src] = 0
    pq = [(0, src)]
    while pq:
        d, u = heapq.heappop(pq)
        if d > dist[u]:
            continue
        for v, w in neighbors(u):
            if d + w < dist[v]:
                dist[v] = d + w
                heapq.heappush(pq, (dist[v], v))
    return dist

# matrix: scan the whole row; list: only real neighbours
nb_matrix = lambda u: [(v, w) for v, w in enumerate(matrix[u]) if w != 0]
nb_list = lambda u: alist[u]

print("Shortest distances from node 0")
print("Adjacency Matrix distances:", dijkstra(nb_matrix, 5, 0))
print("Adjacency List distances:  ", dijkstra(nb_list, 5, 0))

OUTPUT
------
Shortest distances from node 0
Adjacency Matrix distances: [0, 2, 5, 6, 7]
Adjacency List distances:   [0, 2, 5, 6, 7]

RESULT
------
The shortest paths were found using both the adjacency matrix and
the adjacency list. Both gave the same distances. The adjacency
list with a heap is faster for sparse graphs, and the adjacency
matrix suits dense graphs.



==============================================================
EXPERIMENT 8
KNAPSACK PROBLEM USING THE GREEDY STRATEGY
==============================================================

AIM
---
To find the optimal solution for the Knapsack problem using the
Greedy strategy.

THEORY
------
In the Fractional Knapsack problem, items can be broken into
fractions. The greedy choice is to take the items with the highest
value/weight ratio first.

The greedy method gives the optimal answer for the fractional
version, but not for the 0/1 version.

Time complexity: O(n log n), because of the sorting.

ALGORITHM
---------
1. Calculate the value/weight ratio of every item.
2. Sort the items in decreasing order of ratio.
3. Take the items one by one.
4. If the whole item fits, take it and reduce the capacity.
5. If it does not fit, take only the fraction that fills the
   remaining capacity, and stop.
6. Print the total value.

SAMPLE CALCULATION
------------------
Item | Value | Weight | Ratio
-----|-------|--------|------
  1  |   60  |   10   |   6
  2  |  100  |   20   |   5
  3  |  120  |   30   |   4

Capacity = 50

  - Take item 1 (weight 10)  -> value 60,  capacity left = 40
  - Take item 2 (weight 20)  -> value 100, capacity left = 20
  - Take 20/30 of item 3     -> value 120 x 20/30 = 80

Total value = 60 + 100 + 80 = 240

PROGRAM
-------
def fractional_knapsack(values, weights, capacity):
    items = sorted(zip(values, weights), key=lambda x: x[0] / x[1], reverse=True)
    total = 0.0
    for v, w in items:
        take = min(w, capacity)           # whole item or the fraction that fits
        total += v * take / w
        capacity -= take
    return total

values = [60, 100, 120]
weights = [10, 20, 30]
print("Maximum value in Knapsack =", fractional_knapsack(values, weights, 50))

OUTPUT
------
Maximum value in Knapsack = 240.0

RESULT
------
The optimal solution of the Knapsack problem was found using the
Greedy strategy. The maximum value is 240.



==============================================================
EXPERIMENT 10
N-QUEENS PROBLEM USING BACKTRACKING
==============================================================

AIM
---
To implement the N-Queens problem using the Backtracking
strategy.

THEORY
------
The problem is to place N queens on an N x N chessboard so that
no two queens attack each other. No two queens may share a row, a
column or a diagonal.

Backtracking builds the solution step by step. If a choice leads
to a dead end, it goes back and tries another choice.

Two queens are on the same diagonal if:
  |row1 - row2| == |col1 - col2|

Time complexity: O(N!) in the worst case.

ALGORITHM
---------
1. Start with column 0.
2. For the current column, try every row.
3. Check whether the position is safe (no queen in the same row
   or on the same diagonal in the earlier columns).
4. If it is safe, place the queen and move to the next column.
5. If no row works in a column, go back to the previous column
   (backtrack) and move that queen to the next row.
6. Stop when all N queens are placed.
7. Print the board (Q = queen, . = empty).

PROGRAM
-------
def is_safe(board, row, col, n):
    for i in range(n):
        for j in range(col):
            if board[i][j] and (i == row or abs(i - row) == abs(j - col)):
                return False
    return True

def solve(board, col, n):
    if col == n:
        return True
    for row in range(n):
        if is_safe(board, row, col, n):
            board[row][col] = 1
            if solve(board, col + 1, n):
                return True
            board[row][col] = 0           # backtrack
    return False

n = 4
board = [[0] * n for _ in range(n)]
if solve(board, 0, n):
    for r in board:
        print(" ".join('Q' if x else '.' for x in r))
else:
    print("No solution exists")

OUTPUT
------
. . Q .
Q . . .
. . . Q
. Q . .

RESULT
------
Four queens were placed on a 4 x 4 board so that no two queens
attack each other, using the Backtracking strategy.



==============================================================
EXPERIMENT 12
TRAVELLING SALES PERSON PROBLEM USING BRANCH AND BOUND
==============================================================

AIM
---
To implement the Travelling Sales Person (TSP) problem using the
Branch and Bound approach.

THEORY
------
The Travelling Sales Person problem is to find the shortest tour
that visits every city exactly once and returns to the starting
city.

Branch and Bound:
  - BRANCH: Build the tour city by city. Every choice of the next
    city creates a new branch.
  - BOUND: Keep the cost of the best complete tour found so far.
    If the cost of a partial tour is already greater than or equal
    to this best cost, that branch cannot give a better answer, so
    it is cut off (pruned).

Pruning avoids checking many useless tours. The worst case is
still O(n!), but it is usually much faster than checking every
tour.

ALGORITHM
---------
1. Start from city 0 with cost 0 and best cost = infinity.
2. If the current cost is >= best cost, stop (prune).
3. If all cities are visited, add the cost of returning to city 0.
   If it is less than the best cost, save it as the new best.
4. Otherwise, try every unvisited city as the next city and repeat
   from step 2.
5. Print the minimum cost and the path.

SAMPLE DATASET (Cost Matrix)
----------------------------
      | 0  | 1  | 2  | 3
------|----|----|----|----
  0   | 0  | 10 | 15 | 20
  1   | 10 | 0  | 35 | 25
  2   | 15 | 35 | 0  | 30
  3   | 20 | 25 | 30 | 0

Tour 0 -> 1 -> 3 -> 2 -> 0 = 10 + 25 + 30 + 15 = 80

PROGRAM
-------
import math

graph = [[0, 10, 15, 20],
         [10, 0, 35, 25],
         [15, 35, 0, 30],
         [20, 25, 30, 0]]
n = len(graph)
best = [math.inf, ()]

def branch_and_bound(path, cost):
    if cost >= best[0]:                   # bound: cannot beat best, prune
        return
    if len(path) == n:                    # all cities visited, return to start
        total = cost + graph[path[-1]][0]
        if total < best[0]:
            best[:] = [total, tuple(path) + (0,)]
        return
    for city in range(n):                 # branch: try each unvisited city
        if city not in path:
            branch_and_bound(path + [city], cost + graph[path[-1]][city])

branch_and_bound([0], 0)
print("Minimum cost:", best[0])
print("Path:", best[1])

OUTPUT
------
Minimum cost: 80
Path: (0, 1, 3, 2, 0)

RESULT
------
The TSP problem was solved using Branch and Bound. The minimum
cost of the tour is 80 and the path is 0 -> 1 -> 3 -> 2 -> 0.

------
The Min Heap and Max Heap were constructed using arrays, an element
was deleted, and the contents of both heaps were displayed.
