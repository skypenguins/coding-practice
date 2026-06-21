# 63. Unique Paths II
- 問題: https://leetcode.com/problems/unique-paths-ii/
- 言語: Python

## Step1
- `63. Unique Paths` の2次元配列を使ったDPの解法をもとに考える
- 障害物のある座標を訪れた場合、経路として数えないようにする
- 以下の解法では一部テストケースは通過するが、最初の行と最初の列の初期化時に情報量が失われるため最初の行と最初の列のどこかのマスに障害物があった時に障害物を検出できない
- 15分経過したため正答を見る

### 一部のテストケースのみ通過するコード
```py
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        column_n = len(obstacleGrid[0])
        row_n = len(obstacleGrid)
        num_of_paths = [[0] * column_n for _ in range(row_n)]
        for r in range(row_n):
            num_of_paths[r][0] = 1
        for c in range(column_n):
            num_of_paths[0][c] = 1

        for c in range(1, column_n):
            for r in range(1, row_n):
                if obstacleGrid[r][c] == 1:
                    continue
                num_of_paths[r][c] = num_of_paths[r-1][c] + num_of_paths[r][c-1]
        
        return num_of_paths[-1][-1]
```

### 正答
- 最初の行・列は、障害物が現れたマス以降は到達できないため、前のマスの値を引き継ぐ形で初期化する

```py
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        row_n = len(obstacleGrid)
        column_n = len(obstacleGrid[0])

        num_of_paths = [[0] * column_n for _ in range(row_n)]

        # スタート地点
        if obstacleGrid[0][0] == 1:
            return 0
        num_of_paths[0][0] = 1

        # 最初の列
        for r in range(1, row_n):
            if obstacleGrid[r][0] == 0:
                num_of_paths[r][0] = num_of_paths[r - 1][0]

        # 最初の行
        for c in range(1, column_n):
            if obstacleGrid[0][c] == 0:
                num_of_paths[0][c] = num_of_paths[0][c - 1]

        # DP
        for r in range(1, row_n):
            for c in range(1, column_n):
                if obstacleGrid[r][c] == 1:
                    continue
                num_of_paths[r][c] = num_of_paths[r - 1][c] + num_of_paths[r][c - 1]

        return num_of_paths[-1][-1]
```
- 時間計算量: $O(mn)$
- 空間計算量: $O(mn)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.qdbwc4eyd7p0

- https://github.com/olsen-blue/Arai60/pull/34
  - Python
  - Step1の正答の初期化の方法より、こちらの初期化の方が `63. Unique Paths` の方法の自然な拡張になっている気がした
  - 解法2（エッジ初期化をせずに訪れる段階で調べる）は当初自分がやろうとしていた方針だが、条件分岐の順序を考えるのがやはり大変そう
  - 障害物があることを `OBSTACLE` で定数化するのは良いと思った


- https://github.com/Fuminiton/LeetCode/pull/34
  - Python
  - 1次元配列を使う解法
    - やはり2次元配列の方が直観的で分かりやすい
  - 座標を表すのだから、 `x`, `y` の方が文字数も少なくて何を表しているのかは分かりやすい

### 1次元配列での解法
```py
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        OBSTACLE = 1
        if obstacleGrid is None:
            return -1
        if obstacleGrid[0][0] == OBSTACLE:
            return 0
        height = len(obstacleGrid)
        width = len(obstacleGrid[0])
        num_paths = [0] * width
        num_paths[0] = 1

        for y in range(height):
            for x in range(width):
                if obstacleGrid[y][x] == OBSTACLE:
                    num_paths[x] = 0
                    continue
                if x > 0:
                    num_paths[x] += num_paths[x - 1]
        return num_paths[width - 1]
```

### Step3
- 2次元配列の解法
  - 最初の行と最初の列の初期化の方法を変更
  - 変数名の改善

```py
class Solution:
    def uniquePathsWithObstacles(self, obstacleGrid: List[List[int]]) -> int:
        if not obstacleGrid or not obstacleGrid[0]:
            return 0
        
        OBSTACLE = 1
        num_rows = len(obstacleGrid)
        num_cols = len(obstacleGrid[0])
        num_paths = [[0] * num_cols for _ in range(num_rows)]

        if obstacleGrid[0][0] == OBSTACLE:
            return 0

        for r in range(num_rows):
            if obstacleGrid[r][0] == OBSTACLE:
                break
            num_paths[r][0] = 1

        for c in range(num_cols):
            if obstacleGrid[0][c] == OBSTACLE:
                break
            num_paths[0][c] = 1

        for r in range(1, num_rows):
            for c in range(1, num_cols):
                if obstacleGrid[r][c] == OBSTACLE:
                    continue
                num_paths[r][c] = num_paths[r - 1][c] + num_paths[r][c - 1]

        return num_paths[-1][-1]
```
- 所要時間:
  - 1回目: 5:07
  - 2回目: 4:46
  - 3回目: 5:26
