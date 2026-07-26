# 695. Max Area of Island
- 問題: https://leetcode.com/problems/max-area-of-island/
- 言語: Python

## Step1
### 方針
- `200. Number of Islands` の類題と考えて、Union-Findで島の数を数えるときに島の面積を求める処理を入れてみる
- unionするときに島の面積をどう求めるか考えていたら、15分経過してしまったので正答を見る

### 正答
- 以下の正答を読んで `200. Number of Islands` の時のDFSの方法で解いていれば、素直に面積を求められたかもしれないと思った。
- Union-Findは実装が複雑になりがち

#### 方針1: Union-Find
- それぞれの根に対応するsize配列を持ち、unionのたびに合算する
- waterのセルはunionされないため、対応する `size` の値は使われないまま残る

##### コード
```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        WATER = 0
        LAND = 1

        num_rows = len(grid)
        num_cols = len(grid[0])
        num_cells = num_rows * num_cols
        parent = list(range(num_cells))
        rank = [0] * num_cells
        size = [1] * num_cells  # size[i]: セルiが属す連結成分の要素数(初期値1)

        def flatten_index(x, y):
            return x * num_cols + y

        def find(x):
            while parent[x] != x:
                parent[x] = parent[parent[x]]
                x = parent[x]
            return parent[x]

        def union(x, y):
            root_x = find(x)
            root_y = find(y)

            if root_x == root_y:
                return

            if rank[root_x] < rank[root_y]:
                root_x, root_y = root_y, root_x

            parent[root_y] = root_x
            size[root_x] += size[root_y]  # マージ先root_xにサイズを足し込む

            if rank[root_x] == rank[root_y]:
                rank[root_x] += 1

        for row_index in range(num_rows):
            for col_index in range(num_cols):
                if grid[row_index][col_index] == WATER:
                    continue
                if row_index + 1 < num_rows and grid[row_index + 1][col_index] == LAND:
                    union(
                        flatten_index(row_index, col_index),
                        flatten_index(row_index + 1, col_index),
                    )
                if col_index + 1 < num_cols and grid[row_index][col_index + 1] == LAND:
                    union(
                        flatten_index(row_index, col_index),
                        flatten_index(row_index, col_index + 1),
                    )

        max_area = 0
        for row_index in range(num_rows):
            for col_index in range(num_cols):
                if grid[row_index][col_index] == LAND:
                    root = find(flatten_index(row_index, col_index))
                    max_area = max(max_area, size[root])

        return max_area
```
- 時間計算量: $O(mn)$
- 空間計算量: $O(mn)$

#### 方針2: DFS
- 各LAND未訪問セルを起点に「繋がっている陸セルを全部辿って数える」を全セルに対して行い、最大値を取る
- 訪問済みセルは二度と数えないように `visited` で管理

##### iterative DFS
```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        WATER = 0
        LAND = 1

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]

        def area_of_island(start_row, start_col):
            to_visit_cells = [(start_row, start_col)]
            visited[start_row][start_col] = True
            area = 0

            while to_visit_cells:
                row, col = to_visit_cells.pop()
                area += 1

                for d_row, d_col in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                    next_row, next_col = row + d_row, col + d_col
                    if (
                        0 <= next_row < num_rows
                        and 0 <= next_col < num_cols
                        and not visited[next_row][next_col]
                        and grid[next_row][next_col] == LAND
                    ):
                        visited[next_row][next_col] = True
                        to_visit_cells.append((next_row, next_col))

            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if grid[row][col] == LAND and not visited[row][col]:
                    max_area = max(max_area, area_of_island(row, col))

        return max_area
```
- 時間計算量: $O(mn)$
- 空間計算量: $O(mn)$

##### 再帰DFS
```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        WATER = 0
        LAND = 1

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]

        def area_of_island(row, col):
            if (
                row < 0
                or row >= num_rows
                or col < 0
                or col >= num_cols
                or visited[row][col]
                or grid[row][col] == WATER
            ):
                return 0

            visited[row][col] = True
            area = 1

            for d_row, d_col in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                area += area_of_island(row + d_row, col + d_col)

            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if grid[row][col] == LAND and not visited[row][col]:
                    max_area = max(max_area, area_of_island(row, col))

        return max_area
```
- 時間計算量: $O(mn)$
- 空間計算量: $O(mn)$
- 再帰DFSは、繋がった陸地が細長く伸びている場合（例：1000×1000のグリッドが蛇行した1本の細い陸地でほぼ埋まっている場合）、再帰の深さが $m·n$ に達するため再帰上限のエラーになる可能性がある

#### 方針3: BFS
- 方針はDFSとほぼ同じ

##### iterative BFS
```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        WATER = 0
        LAND = 1

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]

        def area_of_island(start_row, start_col):
            to_visit_cells = deque([(start_row, start_col)])
            visited[start_row][start_col] = True
            area = 0

            while to_visit_cells:
                row, col = to_visit_cells.popleft()
                area += 1

                for d_row, d_col in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                    next_row, next_col = row + d_row, col + d_col
                    if (
                        0 <= next_row < num_rows
                        and 0 <= next_col < num_cols
                        and not visited[next_row][next_col]
                        and grid[next_row][next_col] == LAND
                    ):
                        visited[next_row][next_col] = True
                        to_visit_cells.append((next_row, next_col))

            return area

        max_area = 0
        for row in range(num_rows):
            for col in range(num_cols):
                if grid[row][col] == LAND and not visited[row][col]:
                    max_area = max(max_area, area_of_island(row, col))

        return max_area
```
- 時間計算量: $O(mn)$
- 空間計算量: $O(mn)$


## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.f28i04p206ak

- https://github.com/YukiMichishita/LeetCode/pull/6
  - Python
  - やはり `search_land(x + 1, y)` 、 `search_land(x - 1, y)` 、 `search_land(x, y + 1)` 、 `search_land(x, y - 1)` のように方向ごとに再帰する方が分かりやすいか？
  - `nonlocal` の議論: https://github.com/YukiMichishita/LeetCode/pull/6#discussion_r1555974201

- https://github.com/colorbox/leetcode/pull/32
  - C++
  - この方もスタックに、方向を格納した配列のiterativeではなくハードコードで方向ごとに積んでいる
    - 配列の方がシンプルとの見解もある: https://github.com/colorbox/leetcode/pull/32#discussion_r1898537718
  - スタックに追加前に範囲チェックする考え方: https://github.com/colorbox/leetcode/pull/32#discussion_r1898178545

- https://github.com/t0hsumi/leetcode/pull/19
  - Python
  - 同じようなチェックを関数化しているが実装が複雑になりそう

- https://github.com/ryoooooory/LeetCode/pull/21
  - Java
  - `addToQueue` を4回呼び出していれば、それは4方向に探索すると伝わりやすいなと思った
   - cf. https://github.com/ryoooooory/LeetCode/pull/21#discussion_r1966729356
  - Javaの `record` は便利そう

- https://github.com/Fuminiton/LeetCode/pull/18
  - Python
  - 方向を書き下すか、配列で持つかは趣味の範囲っぽそう: https://github.com/Fuminiton/LeetCode/pull/18#discussion_r1986038739

## Step3
### 方針: iterative DFS
```py
class Solution:
    def maxAreaOfIsland(self, grid: List[List[int]]) -> int:
        WATER = 0
        LAND = 1

        num_rows = len(grid)
        num_cols = len(grid[0])
        visited = [[False] * num_cols for _ in range(num_rows)]

        def get_area_of_island(start_row_index, start_col_index):
            to_visit_cells = [(start_row_index, start_col_index)]
            visited[start_row_index][start_col_index] = True
            area = 0

            while len(to_visit_cells) != 0:
                row_index, col_index = to_visit_cells.pop()
                area += 1

                for direction_row, direction_col in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                    next_row_index = row_index + direction_row
                    next_col_index = col_index + direction_col
                    if (
                        0 <= next_row_index < num_rows
                        and 0 <= next_col_index < num_cols
                        and not visited[next_row_index][next_col_index]
                        and grid[next_row_index][next_col_index] == LAND
                    ):
                        visited[next_row_index][next_col_index] = True
                        to_visit_cells.append((next_row_index, next_col_index))

            return area

        max_area = 0
        for row_index in range(num_rows):
            for col_index in range(num_cols):
                if (
                    grid[row_index][col_index] == LAND
                    and not visited[row_index][col_index]
                ):
                    max_area = max(max_area, get_area_of_island(row_index, col_index))

        return max_area
```
- 所要時間:
  - 1回目: 8:11
  - 2回目: 7:19
  - 3回目: 8:16
