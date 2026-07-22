# 323. Number of Connected Components in an Undirected Graph
- 問題: https://leetcode.com/problems/number-of-connected-components-in-an-undirected-graph
  - 代替: https://neetcode.io/problems/count-connected-components
- 言語: Python

## Step1
### 方針
- 辺: `edges[i]` どうしの要素（節）が重複していれば、その辺と辺は接続している
  - a, bが同じ値かどうかで接続しているグループを数えられないか？
- ループを検出できない
- 20分ほど経過したため正答を確認

### WA
```py
class Solution:
    def countComponents(self, n: int, edges: List[List[int]]) -> int:
        previous_edge = edges[0]
        num_connected_group = 0

        for edge in edges:
            previous_b = previous_edge[1]
            current_a = edge[0]
            if previous_b != current_a:
                num_connected_group += 1
            
            previous_edge = edge
        
        return num_connected_group
```

### 正しい方針
- n 個の節（0 ~ n-1）と、無向グラフの辺のリスト `edges` が与えられたとき、連結成分（connected components）の数を求める
  - 連結成分とは、あるグループ内のすべての節が他のすべての節に到達可能な節の集合


#### 方針1: DFS
- グラフを隣接リストで構築し、未訪問の節から探索を開始するたびに連結成分数を1増やす
- なぜ隣接リストを作成するか？
  - `edges` のリストのままだと、例えば節1の隣を調べるのに毎回全edgeの線形探索 $O(E)$ が必要だから

##### 再帰DFS
```py
class Solution:
    def countComponents(self, n: int, edges: list[list[int]]) -> int:
        graph = [[] for _ in range(n)]
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        visited = [False] * n
        count = 0

        def visit_node(node):
            visited[node] = True
            for neighbor in graph[node]:
                if not visited[neighbor]:
                    visit_node(neighbor)

        for i in range(n):
            if not visited[i]:
                count += 1
                visit_node(i)

        return count
```
- 時間計算量: $O(N + E)$
  - 見積: 最大ケース $n = 2000, E = 5000$ 、$10^{7}$ ステップ/秒 としたとき、 
    - 隣接リスト構築: 約 $5000 × 2$ ステップ
    - `visited` 配列初期化: 約 $2000$ ステップ
    - DFS走査: 約 $2000 + 10000$ ステップ
      - $24000 / 10^{7} = 2.4 × 10^{−3} = 約2.4 \rm{ms}$ 

- 空間計算量: $O(N + E)$
- 直観的で理解しやすいと思った

#### 方針2: BFS
- グラフを隣接リストで構築し、未訪問の節から探索を開始するたびに連結成分数を1増やす

```py
class Solution:
    def countComponents(self, n: int, edges: list[list[int]]) -> int:
        # 隣接リストを構築
        graph = [[] for _ in range(n)]
        for u, v in edges:
            graph[u].append(v)
            graph[v].append(u)

        visited = [False] * n
        count = 0

        def visit_node(start):
            queue = deque([start])
            visited[start] = True

            while queue:
                node = queue.popleft()
                for neighbor in graph[node]:
                    if not visited[neighbor]:
                        visited[neighbor] = True
                        queue.append(neighbor)

        for i in range(n):
            if not visited[i]:
                count += 1
                visit_node(i)  # 新しい連結成分を発見するたびにBFSで全域を訪問済みにする

        return count
```

- 時間計算量: $O(N + E)$
- 空間計算量: $O(N + E)$
- これも直観的で理解しやすいと思った。最短経路探索に応用できる


#### 方針3: Union-Find（素集合データ構造）
1. 最初は、それぞれの節が独立した集合（自分自身が親）として n 個の連結成分が存在するとみなす
2. 各辺 `(u, v)` を処理するたびに、`u` と `v` が属する集合をマージ（union）する
3. マージが実際に行われた（＝それまで別の集合だった）たびに、連結成分の数を1減らす
4. 全ての辺を処理し終えたら、残った連結成分の数が答えとなる

```py
class Solution:
    def countComponents(self, n: int, edges: list[list[int]]) -> int:
        parent = list(range(n)) # listのindexが節の値と対応
        rank = [0] * n
        self.count = n  # 連結成分の数

        def find(x):
            while parent[x] != x: # 自分自身でない間ずっと探索
                parent[x] = parent[parent[x]] # 経路圧縮
                x = parent[x]
            
            return x

        def union(x, y):
            rootX = find(x)
            rootY = find(y)
            # すでに同じ集合（根が同じ）なら何もしない
            if rootX == rootY:
                return
                
            # union by rank
            # rankが小さい方を大きい方の下に繋げるために入れ替える
            if rank[rootX] < rank[rootY]:
                rootX, rootY = rootY, rootX
            # rootYを子にする、rankが大きい方を必ずrootX（親）にする
            parent[rootY] = rootX

            if rank[rootX] == rank[rootY]:
                rank[rootX] += 1
            # マージに成功したので連結成分を1つ減らす
            self.count -= 1

        for u, v in edges:
            union(u, v)

        return self.count
```

- 時間計算量: $O(N + E · α(N))$
  - $α$ は逆アッカーマン関数と呼ばれるものらしい、ほぼ一定 $O(1)$
  - 見積: 最大ケース $n = 2000, E = 5000$ 、$10^{7}$ ステップ/秒 としたとき、 
    - `parent` 、 `rank` の配列初期化: 約 $2000$ ステップ
    - 各辺の union 処理(find×2 + 比較・更新): 約 $5000 × 4$ ステップ
      - $22000 / 10^{7} = 2.2 × 10^{−3} = 約2.2 \rm{ms}$ 

- 空間計算量: $O(N)$
- Union by Rank: それぞれの根に「自分の木のだいたいの高さ（rank）」を記録しておき、union のたびに「低い方の木を、高い方の木の下に繋げる」操作をしていくことで、木の高さを $O(\log N)$ 以下に抑える
- このデータ構造（アルゴリズム）を知っていれば、使ったかもしれない

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.aza5ygjw59gj
  - コメントなし

- https://github.com/quinn-sasha/leetcode/pull/31
  - C++
  - 探索部分は関数化した方が確かに認知負荷は低い
  - Union-Findで連結成分を1つ減らすのは `union()` に入れない方がいいかも？

- https://github.com/h1rosaka/arai60/pull/60
  - Python
  - `visited` (`seen`) に Set を使っている
  - Rank by sizeという考え方もあるらしい

- https://github.com/tom4649/Coding/pull/56
  - Python
  - 命名規則として根の意味で parent を使っている？

## Step3
### 読みやすく書き直したコード: Union-Find
```py
class Solution:
    def countComponents(self, n: int, edges: list[list[int]]) -> int:
        parent = list(range(n))
        rank = [0] * n
        num_components = n

        def find(x):
            while parent[x] != x:
                parent[x] = parent[parent[x]]
                x = parent[x]
            
            return x
        
        def union(x, y):
            root_x = find(x)
            root_y = find(y)

            if root_x == root_y:
                return False
            
            if rank[root_x] < rank[root_y]:
                root_x, root_y = root_y, root_x
            
            parent[root_y] = root_x

            if rank[root_x] == rank[root_y]:
                rank[root_x] += 1
            
            return True
        
        for u, v in edges:
            if union(u, v):
                num_components -= 1
        
        return num_components
```
- 所要時間:
  - 1回目: 3:52
  - 2回目: 3:53
  - 3回目: 3:45
