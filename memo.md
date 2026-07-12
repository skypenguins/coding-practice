# 200. Number of Islands
- 問題: https://leetcode.com/problems/number-of-islands/
- 言語: Python

## Step1
### 方針
- DPかなと思ったが迷路の探索みたいなのでBFS/DFSか？
- マインスイーパー？
- 手作業でやるなら？
  - 島は1が隣接（連続）している
  - 全ての座標に調査員を配置して、上下左右の状態を調べさせる
    - 上下左右が全て0なら、1が1個しかない島
- 10分経過しても続きの方針が思いつかない

### 正答
- DFS、BFSを使う方法

#### 共通の方針（DFS）
- グリッドを2次元配列とみなし、'1'（陸地）のマスに出会ったら、そこから上下左右に繋がっている陸地を全部「訪問済み」にする（沈める）。これの1回ごとに島の数を+1する。
- 時間計算量: $O(M×N)$

##### 再帰DFS
```py
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if grid is None:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        count = 0
        
        def erase_land(r, c):
            # 範囲外 or 海 or 訪問済みなら終了
            if r < 0 or r >= rows or c < 0 or c >= cols or grid[r][c] != '1':
                return
            
            grid[r][c] = '0'  # 訪問済みマークとして自分自身を'0'に書き換える
            
            erase_land(r + 1, c)
            erase_land(r - 1, c)
            erase_land(r, c + 1)
            erase_land(r, c - 1)
        
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == '1':
                    count += 1
                    erase_land(r, c)  # この島を全部沈める(0にする)
        
        return count
```
- 留意点
  - gridをin-placeで変更している
  - Pythonはデフォルトの再帰上限が1000程度。gridが大きい（例: $300×300 = 90000$ マスが一直線に繋がっている）と`RecursionError`になり得る

##### iterative DFS
```py
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if grid is None:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        count = 0
        
        def erase_land(r, c):
            pending_cells = [(r, c)]
            grid[r][c] = '0'  # スタックに入れる時点でマークしておく
            
            while pending_cells:
                current_row, current_col = pending_cells.pop()
                
                for direction_row, direction_col in [(1,0), (-1,0), (0,1), (0,-1)]:
                    next_row, next_col = current_row + direction_row, current_col + direction_col
                    if 0 <= next_row < rows and 0 <= next_col < cols and grid[next_row][next_col] == '1':
                        grid[next_row][next_col] = '0'  # ここでマーク
                        pending_cells.append((next_row, next_col))
        
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == '1':
                    count += 1
                    erase_land(r, c)
        
        return count
```
- 留意点
  - gridをin-placeで変更している
  - マークするタイミングはスタックに積む時
- 探索順序: LIFOのため奥まで一直線に進む

#### 共通の方針（BFS）
- 今いる場所から1マス隣（上下左右）を全部見てから、その次の輪へ広げていき、同心円状に探索する

```py
from collections import deque

class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        if not grid:
            return 0
        
        rows, cols = len(grid), len(grid[0])
        count = 0
        
        def erase_land(r, c):
            pending_cells = deque([(r, c)])
            grid[r][c] = '0' # 探索の開始点をすぐに「訪問済み」にする
            
            while pending_cells:
                current_row, current_col = pending_cells.popleft()  # 左から取り出す ← ここがDFSとの唯一の違い
                
                for direction_row, direction_col in [(1,0), (-1,0), (0,1), (0,-1)]:
                    next_row, next_col = current_row + direction_row, current_col + direction_col
                    if 0 <= next_row < rows and 0 <= next_col < cols and grid[next_row][next_col] == '1':
                        grid[next_row][next_col] = '0' # キューに入れる時点で「訪問済み」にする
                        pending_cells.append((next_row, next_col))
        
        for r in range(rows):
            for c in range(cols):
                if grid[r][c] == '1':
                    count += 1
                    erase_land(r, c)
        
        return count
```
- iterative DFSとほとんど同じ
- 時間計算量: $O(M×N)$
- 探索順序: FIFOのため、近い順に層状（同心円状）に広がる
- DFSとBFSでの状態遷移（探索順序）の違い:
  - DFS: `[左, 右, 上, 下]` → `[左, 右, 上, 左, 右, 上, 下]`
  - BFS: `[左, 右, 上, 下]` → `[右, 上, 下, 左, 右, 上, 下]`

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.6z8kd1z4d8gp

- https://github.com/colorbox/leetcode/pull/31
  - C++
  - 4方向のチェックが8方向になった時のためにループにするという方針もある

- https://github.com/Ryotaro25/leetcode_first60/pull/18
  - C++
  - `1`, `0` はマジックナンバーなので、`LAND`、 `SEA` のように定数化すると読みやすい

- https://github.com/tarinaihitori/leetcode/pull/17
  - Python
  - Union-Findは名前くらいしか知らなかったので、今回調べた
    - 別名: 素集合データ構造（disjoint-set data structure）
    - Union-Findは「同じグループに属するかどうか」を高速に管理するデータ構造。隣接する陸地同士を同じグループ（=同じ島）としてunion（結合）していくという発想で解く。
  - 調べた方法: 
    ```py
    class UnionFind:
        def __init__(self, n):
            self.parent = list(range(n))  # 最初は全員が自分自身の親(=バラバラのグループ)
            self.rank = [0] * n
            self.count = n  # グループの数(初期値は全マス分)
        
        def find(self, x):
            if self.parent[x] != x:
                self.parent[x] = self.find(self.parent[x])  # 経路圧縮(path compression)
            return self.parent[x]
        
        def union(self, x, y):
            root_x, root_y = self.find(x), self.find(y)
            if root_x == root_y:
                return  # すでに同じグループなら何もしない
            
            # union by rank: 低い木を高い木の下にぶら下げる
            if self.rank[root_x] < self.rank[root_y]:
                root_x, root_y = root_y, root_x
            self.parent[root_y] = root_x
            if self.rank[root_x] == self.rank[root_y]:
                self.rank[root_x] += 1
            
            self.count -= 1  # 2つのグループが1つに統合されたので数を減らす
    
    
    class Solution:
        def numIslands(self, grid: List[List[str]]) -> int:
            if not grid:
                return 0
            
            rows, cols = len(grid), len(grid[0])
            uf = UnionFind(rows * cols)
            water_count = 0
            
            def index(r, c):
                return r * cols + c  # 2次元座標を1次元インデックスに変換
            
            for r in range(rows):
                for c in range(cols):
                    if grid[r][c] == '0':
                        water_count += 1
                        continue
                    
                    # 右と下だけ見れば、全方向カバーできる(重複チェック不要)
                    if r + 1 < rows and grid[r + 1][c] == '1':
                        uf.union(index(r, c), index(r + 1, c))
                    if c + 1 < cols and grid[r][c + 1] == '1':
                        uf.union(index(r, c), index(r, c + 1))
            
            return uf.count - water_count
    ```
    - Union-Findの例え:
      - > UnionFind の仕事をしてくれる人間いるとしましょう。自分は、numIslands の仕事をしていて電話をかけます。
        > UnionFind に3種類の電話をかけているわけですが、つまり、それぞれ抽象的には「初期化」「2つが繋がっているので繋いでくれ」「ある陸地の親はどこか」を連絡しているわけですね。
        - src: https://github.com/ichika0615/arai60/pull/9#discussion_r1954436002

## Step3
### 読みやすく書き直したコード
- iterative DFS

```py
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        SEA = '0'
        LAND = '1'

        if grid is None:
            return 0
        
        num_rows, num_cols = len(grid), len(grid[0])
        num_islands = 0
        
        def erase_land(r, c):
            visited_cells = [(r, c)]
            grid[r][c] = SEA
            
            while len(visited_cells) != 0:
                current_row, current_col = visited_cells.pop()
                
                for direction_row, direction_col in [(1, 0), (-1, 0), (0, 1), (0, -1)]:
                    next_row, next_col = current_row + direction_row, current_col + direction_col
                    if 0 <= next_row < num_rows and 0 <= next_col < num_cols and grid[next_row][next_col] == LAND:
                        grid[next_row][next_col] = SEA
                        visited_cells.append((next_row, next_col))
        
        for r in range(num_rows):
            for c in range(num_cols):
                if grid[r][c] == LAND:
                    num_islands += 1
                    erase_land(r, c)
        
        return num_islands
```

- 所要時間: 
  - 1回目: 7:31
  - 2回目: 6:15
  - 3回目: 5:30
