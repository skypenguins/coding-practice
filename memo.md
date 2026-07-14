# 276. Paint Fence
- 問題: https://leetcode.com/problems/paint-fence/
  - 代替: https://www.lintcode.com/problem/514/
  - 代替: https://algo.monster/liteproblems/276
- 言語: Python

## Step1
- 同じ色の柱は2つまで隣接して良い
- 組合せかと思ったが、組合せではない？
- 15分ほど考えたが思い浮かばず

### 正しい方針
- 自分の方針は塗り方のパターンをリストでそのまま再現しようとしていた。塗り方の数（組合せ）を数える方針でよい。
- DPで「直前と同じ色か」つまり「現在の柱が前の柱と同じ色で塗装できるかどうか」を状態としてもつ。これは、最後の2本の柱の塗装パターンによって決まる。
- i番目の柱まで塗り終えた状態を2つに分けて数える。
  - `same_patterns[i]`：i番目とi-1番目が同じ色である塗り方の数
    - i-1番目とi番目を同じ色にする。このとき、もしi-2番目とi-1番目も同じ色だったら3連続になってしまうので、i-2番目とi-1番目は必ず違う色でなければならない。色の選び方はi-1番目の色に合わせるだけなので1通り。
  - `different_patterns[i]`：i番目とi-1番目が異なる色である塗り方の数
    - i-1番目までの塗り方（同色でも異色でも良い）に対して、i番目をi-1番目と違う色にする。i-1番目以外のk-1色から選べる。

### 正答
```py
class Solution:
    def numWays(n: int, k: int) -> int:
        if n == 0:
            return 0

        if n == 1:
            return k

        different_patterns = [0] * n
        same_patterns = [0] * n

        different_patterns[0] = k
        same_patterns[0] = 0 

        for i in range(1, n):
            different_patterns[i] = (different_patterns[i - 1] + same_patterns[i - 1]) * (k - 1)
            same_patterns[i] = different_patterns[i - 1]

        return different_patterns[n - 1] + same_patterns[n - 1]
```
- 時間計算量: $O(n)$
- 空間計算量: $O(n)$

### 正答（配列を使わない方法）
```py
class Solution:
    def numWays(n: int, k: int) -> int:
        if n == 0:
            return 0
        if n == 1:
            return k

        same_patterns = k              # same_patterns[2]
        different_patterns = k * (k - 1)    # different_patterns[2]

        for i in range(3, n + 1):
            same_patterns, different_patterns = different_patterns, (same_patterns + different_patterns) * (k - 1)

        return same_patterns + different_patterns
```
- 時間計算量: $O(n)$
- 空間計算量: $O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.leqr94ydg2be

- https://github.com/goto-untrapped/Arai60/pull/44
  - Java
  - Step1の方法に加えて、再帰（Top-down DP）、メモ化再帰（Top-down DP）の方法
    - LRU Cacheを自前実装し、メモ化
  - 今回の漸化式からフィボナッチ数列を連想するし、213. House Robber と方針は同じだと思った
    - つまり、状態を2つの配列で管理せずとも、1つの配列で「i本塗る場合の総数」を管理できる
  - フィボナッチ数列の極限によるステップ数の見積: https://github.com/goto-untrapped/Arai60/pull/44/changes#r1706360077

- https://github.com/hayashi-ay/leetcode/pull/17
  - Python
  - Step1の方法に加えて、再帰（Bottom-up DP、Top-down DP）、メモ化再帰（Top-down DP）の方法
    - LRU Cacheを自前実装し、メモ化

### Step3
#### 読みやすく書き直したコード
##### 方針
- iterative Bottom-up DP
- 1個の配列（リスト）で「i本塗る場合の総数」を表現する
  - 違う色にする場合：i-1本までの塗り方（`num_ways[i-1]`通り）に対して、i番目をi-1番目と違うk-1色から選ぶ
→ `num_ways[i-1] * (k-1)`
  - 同じ色にする場合：3連続を避けるため、i-1番目とi-2番目は必ず違う色でなければならない。i-2本までの塗り方（`num_ways[i-2]`通り）に対して、i-1番目をi-2番目と違う色にして（これで自動的にk-1通り選んだことになる）、i番目はi-1番目と同じ色にする（1通り）
→ `num_ways[i-2] * (k-1) * 1` = `num_ways[i-2] * (k-1)`

```py
class Solution:
    def numWays(self, n: int, k: int) -> int:
        if n == 0:
            return 0
        if n == 1:
            return k
        
        num_ways = [0] * n
        num_ways[0] = k
        num_ways[1] = k * k
        for i in range(2, n):
            num_ways[i] = (k - 1) * (num_ways[i - 1] + num_ways[i - 2])
        
        return num_ways[-1]
```
- 所要時間:
  - 1回目: 2:32
  - 2回目: 1:43
  - 3回目: 1:41
