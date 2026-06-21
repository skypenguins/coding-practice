# 62. Unique Paths
- 問題: https://leetcode.com/problems/unique-paths/
- 言語: Python

## Step1
- DPではあるかなと思ったが、単純に組合せ数を求めるだけで良いと考えた
- 右に $n-1$ 回、下に $m-1$ 回だけ移動する
- 移動回数の合計 $(n-1)+(m-1)$ から右移動（または下移動）の回数分だけ選ぶ場合の数（組合せの総数、二項係数）がユニークな経路数である

### ACした解答
```py
import math
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        return math.comb((m-1)+(n-1), m-1)
```
- 所要時間: 13:17
  - 多分、問題の意図したところとしてはmathライブラリを使わずに自前実装でfactorialを作ってcombinationを求める解答かも？
- 処理系CPythonでの `math.comb()` の実装: https://github.com/python/cpython/blob/17d5b9df10f53ae3c09c8b22f27d25d9e83b4b7e/Modules/mathmodule.c#L3805

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.brtd7l7oqr0f

- https://github.com/olsen-blue/Arai60/pull/33
  - Python
  - 1次元配列、2次元配列でDP、再帰、自前実装で二項係数を求める方法

### 他の正答
#### 2次元DP
```py
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        num_of_paths = [[0] * n for _ in range(m)]
        for c in range(n):
            num_of_paths[0][c] = 1
        for r in range(m):
            num_of_paths[r][0] = 1
        
        for r in range(1, m):
            for c in range(1, n):
                num_of_paths[r][c] = num_of_path[r-1][c] + num_of_path[r][c-1]

        return num_of_path[-1][-1]
```
- 上端、左端の値を1で初期化
- 位置 (r,c) に到達する経路数は下記の和
  - 上の (r-1, c) から下に移動してくる
  - 左の (r, c-1) から右に移動してくる
- 時間計算量: $O(n * m)$

#### 1次元DP
```py
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        num_of_paths = [0] * n
        num_of_paths[0] = 1
        for _ in range(m):
            for c in range(1, n):
                num_of_paths[c] += num_of_paths[c-1]
        
        return num_of_paths[-1]
```
- 時間計算量: $O(n * m)$

#### 階乗（自前実装）による二項係数
```py
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        def factorial(k: int) -> int:
            if k == 0:
                return 1
            return k * factorial(k-1)
        combination = factorial(m + n - 2) // (factorial(m-1) * factorial(n-1))
        return combination
```

#### 再帰
```py
class Solution:
    @cache
    def uniquePaths(self, m: int, n: int) -> int:
        if m == 1 or n == 1:
            return 1
        return self.uniquePaths(m-1, n) + self.uniquePaths(m, n-1)
```
- 時間計算量: `O((n+m-2)C(m-1))`

- https://github.com/saagchicken/coding_practice/pull/19
  - Python
  - > 常に同じものが複製されるが、複製される物が immutable ならば問題がない、のほうが近い感覚です。
    - cf. https://github.com/saagchicken/coding_practice/pull/19#discussion_r2007868751
    - これは気をつけたい
  - > 私は二重ループを、右の添字のほうが内側のループになるように回しますが、Python の場合はあまり気にするところではないかもしれません。
    > C++ とかだと、メモリーの配置と速度にわずかに関係があったりします。
    - cf. https://github.com/saagchicken/coding_practice/pull/19#discussion_r2007873330
    - 読みやすさの観点以外で添え字の位置まであまり気にしたことはなかった

## Step3
- 2次元DPで練習
```py
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        num_of_paths = [[0] * n for _ in range(m)]
        for c in range(n):
            num_of_paths[0][c] = 1
        for r in range(m):
            num_of_paths[r][0] = 1
        
        for r in range(1, m):
            for c in range(1, n):
                num_of_paths[r][c] = num_of_paths[r-1][c] + num_of_paths[r][c-1]

        return num_of_paths[-1][-1]
```
- 所要時間:
  - 1回目: 2:38
  - 2回目: 2:44
  - 3回目: 2:20
