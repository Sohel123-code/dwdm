==============================================================
EXPERIMENT 1
AVL TREE: INSERTION, DELETION AND INORDER TRAVERSAL
==============================================================

AIM
---
To construct an AVL tree for a given set of elements stored in a
file, implement insert and delete operations on the tree, and
write the contents of the tree into a new file using inorder
traversal.

THEORY
------
An AVL tree is a self-balancing Binary Search Tree. For every
node, the difference between the heights of the left and right
subtrees (the balance factor) must be -1, 0 or 1.

  Balance factor = height(left) - height(right)

If the balance factor goes outside this range after an insertion
or deletion, the tree is balanced using rotations:

  Case         | Condition                     | Fix
  -------------|-------------------------------|---------------------
  Left-Left    | balance > 1, key < left.key   | Right rotation
  Right-Right  | balance < -1, key > right.key | Left rotation
  Left-Right   | balance > 1, key > left.key   | Left, then Right
  Right-Left   | balance < -1, key < right.key | Right, then Left

Inorder traversal (Left, Root, Right) of a BST gives the keys in
sorted order.

Time complexity: O(log n) for insert and delete.

ALGORITHM
---------
1. Read the elements from the input file.
2. Insert each element as in a normal BST.
3. Update the height of every node on the path back to the root.
4. Calculate the balance factor. If it is out of range, perform
   the required rotation.
5. To delete a key:
     a. Find the node.
     b. If it has two children, replace it with its inorder
        successor (smallest node in the right subtree).
     c. Update heights and re-balance as in step 4.
6. Do an inorder traversal and write the result into the output
   file.

INPUT FILE (input.txt)
----------------------
  10 20 30 40 50 25

PROGRAM
-------
  class Node:
      def __init__(self, key):
          self.key = key
          self.left = None
          self.right = None
          self.height = 1

  class AVLTree:
      def get_height(self, root):
          return 0 if not root else root.height

      def get_balance(self, root):
          return 0 if not root else self.get_height(root.left) - self.get_height(root.right)

      def right_rotate(self, y):
          x = y.left
          T2 = x.right
          x.right = y
          y.left = T2
          y.height = 1 + max(self.get_height(y.left), self.get_height(y.right))
          x.height = 1 + max(self.get_height(x.left), self.get_height(x.right))
          return x

      def left_rotate(self, x):
          y = x.right
          T2 = y.left
          y.left = x
          x.right = T2
          x.height = 1 + max(self.get_height(x.left), self.get_height(x.right))
          y.height = 1 + max(self.get_height(y.left), self.get_height(y.right))
          return y

      def insert(self, root, key):
          if not root:
              return Node(key)
          elif key < root.key:
              root.left = self.insert(root.left, key)
          elif key > root.key:
              root.right = self.insert(root.right, key)
          else:
              return root                      # duplicate not allowed

          root.height = 1 + max(self.get_height(root.left), self.get_height(root.right))
          balance = self.get_balance(root)

          if balance > 1 and key < root.left.key:        # Left-Left
              return self.right_rotate(root)
          if balance < -1 and key > root.right.key:      # Right-Right
              return self.left_rotate(root)
          if balance > 1 and key > root.left.key:        # Left-Right
              root.left = self.left_rotate(root.left)
              return self.right_rotate(root)
          if balance < -1 and key < root.right.key:      # Right-Left
              root.right = self.right_rotate(root.right)
              return self.left_rotate(root)
          return root

      def min_value_node(self, node):
          current = node
          while current.left:
              current = current.left
          return current

      def delete(self, root, key):
          if not root:
              return root
          elif key < root.key:
              root.left = self.delete(root.left, key)
          elif key > root.key:
              root.right = self.delete(root.right, key)
          else:
              if not root.left:
                  return root.right
              elif not root.right:
                  return root.left
              temp = self.min_value_node(root.right)
              root.key = temp.key
              root.right = self.delete(root.right, temp.key)

          root.height = 1 + max(self.get_height(root.left), self.get_height(root.right))
          balance = self.get_balance(root)

          if balance > 1 and self.get_balance(root.left) >= 0:
              return self.right_rotate(root)
          if balance > 1 and self.get_balance(root.left) < 0:
              root.left = self.left_rotate(root.left)
              return self.right_rotate(root)
          if balance < -1 and self.get_balance(root.right) <= 0:
              return self.left_rotate(root)
          if balance < -1 and self.get_balance(root.right) > 0:
              root.right = self.right_rotate(root.right)
              return self.left_rotate(root)
          return root

      def inorder(self, root, result):
          if root:
              self.inorder(root.left, result)
              result.append(root.key)
              self.inorder(root.right, result)

  # create the input file with the elements
  with open("input.txt", "w") as f:
      f.write("10 20 30 40 50 25")

  # read the elements from the file
  with open("input.txt") as f:
      elements = list(map(int, f.read().split()))

  avl = AVLTree()
  root = None

  print("Elements read from file:", elements)
  for num in elements:
      root = avl.insert(root, num)

  insert_val = 15
  print("Inserting element:", insert_val)
  root = avl.insert(root, insert_val)

  delete_val = 20
  print("Deleting element:", delete_val)
  root = avl.delete(root, delete_val)

  inorder_result = []
  avl.inorder(root, inorder_result)

  with open("output.txt", "w") as f:
      f.write(" ".join(map(str, inorder_result)))

  print("Final AVL Tree (Inorder):", inorder_result)
  print("Written to output.txt")

OUTPUT
------
  Elements read from file: [10, 20, 30, 40, 50, 25]
  Inserting element: 15
  Deleting element: 20
  Final AVL Tree (Inorder): [10, 15, 25, 30, 40, 50]
  Written to output.txt

OUTPUT FILE (output.txt)
------------------------
  10 15 25 30 40 50

RESULT
------
The AVL tree was constructed from the elements in the file. Insert
and delete operations were performed with balancing, and the inorder
traversal was written to output.txt.


==============================================================
EXPERIMENT 3
BREADTH FIRST AND DEPTH FIRST TRAVERSAL OF A GRAPH
==============================================================

AIM
---
To implement Breadth First Traversal (BFT) and Depth First
Traversal (DFT) for a given graph when the graph is represented by
a) an adjacency matrix and b) adjacency lists.

THEORY
------
BFT visits all the neighbours of a vertex before going deeper. It
uses a QUEUE.
DFT goes as deep as possible along a path before backtracking. It
uses RECURSION (or a STACK).

Graph representations:
  - Adjacency Matrix: A V x V matrix. matrix[i][j] = 1 if there is
    an edge from i to j. Space: O(V^2).
  - Adjacency List: For each vertex, a list of its neighbours.
    Space: O(V + E).

Time complexity of BFT and DFT:
  - Adjacency Matrix : O(V^2)
  - Adjacency List   : O(V + E)

The graph used here:

      0
     / \
    1   2
     \ /
      3

ALGORITHM
---------
BFT:
1. Mark the start vertex visited and add it to the queue.
2. Remove a vertex from the queue and print it.
3. Add all its unvisited neighbours to the queue and mark them
   visited.
4. Repeat steps 2 and 3 until the queue is empty.

DFT:
1. Mark the start vertex visited and print it.
2. For each unvisited neighbour, call DFT recursively.
3. Return when all neighbours are visited.

PROGRAM
-------
  from collections import deque

  # ---------- a) Adjacency Matrix ----------
  graph_matrix = [
      [0, 1, 1, 0],
      [1, 0, 0, 1],
      [1, 0, 0, 1],
      [0, 1, 1, 0]
  ]

  def bft_matrix(graph, start):
      n = len(graph)
      visited = [False] * n
      queue = deque([start])
      visited[start] = True
      while queue:
          v = queue.popleft()
          print(v, end=' ')
          for i in range(n):
              if graph[v][i] == 1 and not visited[i]:
                  queue.append(i)
                  visited[i] = True

  def dft_matrix(graph, start, visited=None):
      if visited is None:
          visited = [False] * len(graph)
      visited[start] = True
      print(start, end=' ')
      for i in range(len(graph)):
          if graph[start][i] == 1 and not visited[i]:
              dft_matrix(graph, i, visited)

  print("BFT (Adjacency Matrix):")
  bft_matrix(graph_matrix, 0)
  print("\nDFT (Adjacency Matrix):")
  dft_matrix(graph_matrix, 0)

  # ---------- b) Adjacency List ----------
  graph_list = {
      0: [1, 2],
      1: [0, 3],
      2: [0, 3],
      3: [1, 2]
  }

  def bft_list(graph, start):
      visited = set([start])
      queue = deque([start])
      while queue:
          v = queue.popleft()
          print(v, end=' ')
          for neighbor in graph[v]:
              if neighbor not in visited:
                  visited.add(neighbor)
                  queue.append(neighbor)

  def dft_list(graph, start, visited=None):
      if visited is None:
          visited = set()
      visited.add(start)
      print(start, end=' ')
      for neighbor in graph[start]:
          if neighbor not in visited:
              dft_list(graph, neighbor, visited)

  print("\n\nBFT (Adjacency List):")
  bft_list(graph_list, 0)
  print("\nDFT (Adjacency List):")
  dft_list(graph_list, 0)
  print()

OUTPUT
------
  BFT (Adjacency Matrix):
  0 1 2 3 
  DFT (Adjacency Matrix):
  0 1 3 2 

  BFT (Adjacency List):
  0 1 2 3 
  DFT (Adjacency List):
  0 1 3 2 

RESULT
------
BFT and DFT were implemented for the graph using both an adjacency
matrix and adjacency lists. Both representations gave the same
traversal order.


==============================================================
EXPERIMENT 5
QUICK SORT AND MERGE SORT: EXECUTION TIME ANALYSIS
==============================================================

AIM
---
To implement Quick Sort and Merge Sort and observe the execution
time for various input sizes (Average, Worst and Best cases).

THEORY
------
Quick Sort: Picks a pivot, divides the array into elements
smaller than, equal to and greater than the pivot, and sorts the
parts recursively. (Divide and Conquer)

Merge Sort: Divides the array into two halves, sorts each half
recursively and merges the sorted halves. (Divide and Conquer)

  Algorithm   | Best       | Average    | Worst      | Space
  ------------|------------|------------|------------|-------
  Quick Sort  | O(n log n) | O(n log n) | O(n^2)     | O(log n)
  Merge Sort  | O(n log n) | O(n log n) | O(n log n) | O(n)

  - Quick Sort is O(n^2) in the worst case when the pivot is
    always the smallest or largest element (for example, first or
    last element as pivot on sorted data).
  - Merge Sort is always O(n log n) because it always splits the
    array exactly in half.
  - The program here uses the MIDDLE element as pivot, so sorted
    and reverse-sorted inputs do not give the O(n^2) case.

Input used:
  - Average case : random array
  - Best case    : sorted array
  - Worst case   : reverse-sorted array

ALGORITHM
---------
1. Generate a random array of the given size.
2. Make the sorted copy (best case) and the reverse copy (worst
   case).
3. Run Quick Sort and Merge Sort on a copy of each array.
4. Record the time taken using time.time().
5. Repeat for sizes 1000, 5000 and 10000.
6. Print the times.

PROGRAM
-------
  import random
  import time

  def quick_sort(arr):
      if len(arr) <= 1:
          return arr
      pivot = arr[len(arr) // 2]
      left = [x for x in arr if x < pivot]
      middle = [x for x in arr if x == pivot]
      right = [x for x in arr if x > pivot]
      return quick_sort(left) + middle + quick_sort(right)

  def merge_sort(arr):
      if len(arr) <= 1:
          return arr
      mid = len(arr) // 2
      left = merge_sort(arr[:mid])
      right = merge_sort(arr[mid:])
      return merge(left, right)

  def merge(left, right):
      result = []
      i = j = 0
      while i < len(left) and j < len(right):
          if left[i] < right[j]:
              result.append(left[i])
              i += 1
          else:
              result.append(right[j])
              j += 1
      result.extend(left[i:])
      result.extend(right[j:])
      return result

  def measure_time(sort_func, arr):
      start = time.time()
      sort_func(arr)
      end = time.time()
      return end - start

  sizes = [1000, 5000, 10000]
  cases = ["Average", "Best", "Worst"]

  for size in sizes:
      print(f"\nInput size: {size}")
      arr_avg = [random.randint(0, size) for _ in range(size)]
      arr_best = sorted(arr_avg)
      arr_worst = sorted(arr_avg, reverse=True)

      for case, arr in zip(cases, [arr_avg, arr_best, arr_worst]):
          time_quick = measure_time(quick_sort, arr.copy())
          time_merge = measure_time(merge_sort, arr.copy())
          print(f"{case} Case - Quick Sort: {time_quick:.5f}s, Merge Sort: {time_merge:.5f}s")

OUTPUT
------

  Input size: 1000
  Average Case - Quick Sort: 0.00105s, Merge Sort: 0.00123s
  Best Case - Quick Sort: 0.00060s, Merge Sort: 0.00089s
  Worst Case - Quick Sort: 0.00059s, Merge Sort: 0.00081s

  Input size: 5000
  Average Case - Quick Sort: 0.00581s, Merge Sort: 0.00744s
  Best Case - Quick Sort: 0.00344s, Merge Sort: 0.00495s
  Worst Case - Quick Sort: 0.00349s, Merge Sort: 0.00481s

  Input size: 10000
  Average Case - Quick Sort: 0.01231s, Merge Sort: 0.01622s
  Best Case - Quick Sort: 0.00767s, Merge Sort: 0.01059s
  Worst Case - Quick Sort: 0.00774s, Merge Sort: 0.01106s

NOTE: Times differ on every run and on every computer, because the
input is random. Your output will not match these numbers exactly,
but it should look similar.

RESULT
------
Quick Sort and Merge Sort were implemented and their execution
times were observed for different input sizes. The time grows with
the input size, and Quick Sort was faster than Merge Sort in these
runs.


==============================================================
EXPERIMENT 7
JOB SEQUENCING WITH DEADLINES USING GREEDY STRATEGY
==============================================================

AIM
---
To implement Job Sequencing with Deadlines using the Greedy
strategy.

THEORY
------
Each job has a deadline and a profit. Every job takes one unit of
time, and only one job can run at a time. A job gives its profit
only if it is finished on or before its deadline.

Greedy choice: consider the jobs in decreasing order of profit
and put each job in the latest free slot before its deadline.

Time complexity: O(n^2) (O(n log n) for sorting plus slot search).

ALGORITHM
---------
1. Sort the jobs in decreasing order of profit.
2. Find the maximum deadline and create that many empty slots.
3. For each job, look for a free slot starting from its deadline
   and moving backwards.
4. If a free slot is found, put the job there and add its profit.
5. If no free slot is found, skip the job.
6. Print the scheduled jobs and the total profit.

SAMPLE DATASET
--------------
Job | Deadline | Profit
----|----------|-------
 A  |    7     |   15
 B  |    2     |   20
 C  |    5     |   30
 D  |    3     |   18
 E  |    4     |   18
 F  |    5     |   10
 G  |    2     |   23
 H  |    7     |   16
 I  |    3     |   25

SAMPLE CALCULATION
------------------
Jobs in decreasing order of profit: C(30), I(25), G(23), B(20),
D(18), E(18), H(16), A(15), F(10)

  - C (deadline 5) -> slot 5
  - I (deadline 3) -> slot 3
  - G (deadline 2) -> slot 2
  - B (deadline 2) -> slot 1
  - D (deadline 3) -> no free slot, rejected
  - E (deadline 4) -> slot 4
  - H (deadline 7) -> slot 7
  - A (deadline 7) -> slot 6
  - F (deadline 5) -> no free slot, rejected

Total profit = 30 + 25 + 23 + 20 + 18 + 16 + 15 = 147

PROGRAM
-------
  class Job:
      def __init__(self, job_id, deadline, profit):
          self.id = job_id
          self.deadline = deadline
          self.profit = profit

  def job_sequencing(jobs):
      # Step 1: sort jobs by decreasing profit
      jobs.sort(key=lambda x: x.profit, reverse=True)

      n = max(job.deadline for job in jobs)
      result = [None] * n
      total_profit = 0

      # Step 2: put each job in the latest free slot before its deadline
      for job in jobs:
          for slot in range(job.deadline - 1, -1, -1):
              if result[slot] is None:
                  result[slot] = job.id
                  total_profit += job.profit
                  break

      scheduled_jobs = [job_id for job_id in result if job_id is not None]
      return scheduled_jobs, total_profit

  jobs = [
      Job('A', 7, 15),
      Job('B', 2, 20),
      Job('C', 5, 30),
      Job('D', 3, 18),
      Job('E', 4, 18),
      Job('F', 5, 10),
      Job('G', 2, 23),
      Job('H', 7, 16),
      Job('I', 3, 25)
  ]

  scheduled_jobs, total_profit = job_sequencing(jobs)
  print("Scheduled Jobs:", scheduled_jobs)
  print("Total Profit:", total_profit)

OUTPUT
------
  Scheduled Jobs: ['B', 'G', 'I', 'E', 'C', 'A', 'H']
  Total Profit: 147

RESULT
------
The jobs were scheduled using the Greedy strategy. The maximum
profit obtained is 147.


==============================================================
EXPERIMENT 9
0/1 KNAPSACK PROBLEM USING DYNAMIC PROGRAMMING
==============================================================

AIM
---
To write a program to solve the 0/1 Knapsack problem using
Dynamic Programming.

THEORY
------
In the 0/1 Knapsack problem, each item must be taken completely or
not taken at all. Greedy does not always give the best answer, so
Dynamic Programming is used.

dp[i][w] = maximum value using the first i items with capacity w.

  If weight[i-1] <= w:
      dp[i][w] = max( dp[i-1][w],
                      value[i-1] + dp[i-1][w - weight[i-1]] )
  Else:
      dp[i][w] = dp[i-1][w]

Time complexity  : O(n x W)
Space complexity : O(n x W)

ALGORITHM
---------
1. Create a table dp of size (n + 1) x (W + 1) filled with 0.
2. For each item i from 1 to n:
     For each capacity w from 0 to W:
       a. If the item fits, take the maximum of skipping it and
          taking it.
       b. Otherwise, copy the value from the row above.
3. The answer is dp[n][W].

SAMPLE DATASET
--------------
Item | Value | Weight
-----|-------|-------
  1  |   60  |   10
  2  |  100  |   20
  3  |  120  |   30

Capacity = 50

PROGRAM
-------
  def knapsack_dp(values, weights, capacity):
      n = len(values)
      dp = [[0] * (capacity + 1) for _ in range(n + 1)]

      for i in range(1, n + 1):
          for w in range(capacity + 1):
              if weights[i - 1] <= w:
                  dp[i][w] = max(
                      dp[i - 1][w],                                         # skip item
                      values[i - 1] + dp[i - 1][w - weights[i - 1]]         # take item
                  )
              else:
                  dp[i][w] = dp[i - 1][w]
      return dp[n][capacity]

  values = [60, 100, 120]
  weights = [10, 20, 30]
  W = 50

  print("Maximum value in Knapsack =", knapsack_dp(values, weights, W))

OUTPUT
------
  Maximum value in Knapsack = 220

RESULT
------
The 0/1 Knapsack problem was solved using Dynamic Programming. The
maximum value is 220 (items of weight 20 and 30).


==============================================================
EXPERIMENT 11
0/1 KNAPSACK PROBLEM USING BACKTRACKING
==============================================================

AIM
---
To use the Backtracking strategy to solve the 0/1 Knapsack
problem.

THEORY
------
Backtracking tries all choices step by step. For every item there
are two choices:
  - Include the item (if it fits).
  - Exclude the item.

The recursion explores both choices, and the better result is
returned. If an item is too heavy, only the exclude choice is
possible.

This forms a state-space tree of depth n.

Time complexity: O(2^n) in the worst case.

ALGORITHM
---------
1. If there are no items left or the capacity is 0, return 0.
2. If the weight of the last item is more than the capacity,
   skip it and solve for the remaining items.
3. Otherwise, calculate two values:
     a. Include = value of item + result for remaining capacity
     b. Exclude = result without the item
4. Return the maximum of the two values.

SAMPLE DATASET
--------------
Item | Value | Weight
-----|-------|-------
  1  |   60  |   10
  2  |  100  |   20
  3  |  120  |   30

Capacity = 50

PROGRAM
-------
  def knapsack_backtrack(values, weights, capacity, n):
      if n == 0 or capacity == 0:                 # no items or no space left
          return 0

      if weights[n - 1] > capacity:               # item too heavy, skip it
          return knapsack_backtrack(values, weights, capacity, n - 1)

      include = values[n - 1] + knapsack_backtrack(values, weights, capacity - weights[n - 1], n - 1)
      exclude = knapsack_backtrack(values, weights, capacity, n - 1)
      return max(include, exclude)

  values = [60, 100, 120]
  weights = [10, 20, 30]
  W = 50

  print("Maximum value in Knapsack =", knapsack_backtrack(values, weights, W, len(values)))

OUTPUT
------
  Maximum value in Knapsack = 220

RESULT
------
The 0/1 Knapsack problem was solved using Backtracking. The
maximum value is 220, the same as the Dynamic Programming answer.


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

  graph = [
      [0, 10, 15, 20],
      [10, 0, 35, 25],
      [15, 35, 0, 30],
      [20, 25, 30, 0]
  ]
  n = len(graph)

  best_cost = math.inf
  best_path = []

  def branch_and_bound(path, visited, cost):
      global best_cost, best_path

      if cost >= best_cost:                 # BOUND: cannot beat the best, prune
          return

      if len(path) == n:                    # all cities visited, return to start
          total = cost + graph[path[-1]][0]
          if total < best_cost:
              best_cost = total
              best_path = path + [0]
          return

      for city in range(n):                 # BRANCH: try every unvisited city
          if city not in visited:
              branch_and_bound(path + [city], visited | {city},
                               cost + graph[path[-1]][city])

  branch_and_bound([0], {0}, 0)

  print("Minimum cost:", best_cost)
  print("Path:", tuple(best_path))

OUTPUT
------
  Minimum cost: 80
  Path: (0, 1, 3, 2, 0)

RESULT
------
The TSP problem was solved using Branch and Bound. The minimum
cost of the tour is 80 and the path is 0 -> 1 -> 3 -> 2 -> 0.


==============================================================
                        END OF MANUAL
==============================================================
