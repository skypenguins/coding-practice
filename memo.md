# 300. Longest Increasing Subsequence
- 問題: https://leetcode.com/problems/longest-increasing-subsequence/
- 言語: Python

## Step1
### 方針
- 最小値の位置を特定し、そこより右側に探索範囲を縮める
- 右側を一つずつ訪問していき、インクリメントして最長増加部分列の長さを算出。その時点での最大値を保持し、最大値未満のときはインクリメントしない
- `nums = [0, 1, 0, 3, 2, 3]` のようなテストケースにだと、`[0, 1, 3]` と誤判定する（正解は `[0, 1, 2, 3]`）
  - 最大値より下がって上がって下がるパターン
- 50分経過したため正答を見る

### 正しい方針
## 方針1: DP
- numsのi番目を最後尾としたときの最長増加部分列の長さ（求めたい結果）を要素に持つ配列（リスト）を定義
  - その時点での結果を情報として持っておく

```py
class Solution:
    def lengthOfLIS(self, nums):
        n = len(nums)
        LIS_length = [1] * n  # 各要素単体でも長さ1の部分列だから1で初期化
        for i in range(1, n):
            for j in range(i):
                if nums[j] < nums[i]:
                    LIS_length[i] = max(LIS_length[i], LIS_length[j] + 1)
        return max(LIS_length)
```

- 時間計算量: $O(n^{2})$
  - $n = 2500$ の場合、ステップ数: $n(n-1)/2$ から 3,123,750 ステップ、 Pythonの実行時間: $10^{7}$ ステップ/秒 とすると、およそ 312 ms
  - $n = 10000$ の場合、およそ 5秒
- 空間計算量: $O(n)$

## 方針2: 貪欲法 ＋ 二分探索
- 配列 `tails` を、長さ k の増加部分列を作るときの末尾（右端）の値をできるだけ小さく保つように定義。ソート済み配列になる。末尾が小さいほど、その後ろに要素を追加しやすくなる
- 各 `num` を順に処理し:
  1. `tails` の中で `num` 以上となる最初の位置を二分探索で探す
  2. 見つかれば、その位置を `num` で置き換える（末尾をより小さくする）
  3. 見つからなければ（ `num` が全要素より大きい）、tails の末尾に追加する

```py
class Solution:
    def lengthOfLIS(self, nums):
        tails = []
        for num in nums:
            pos = bisect_left(tails, num)
            if pos == len(tails):
                tails.append(num)
            else:
                tails[pos] = num
        return len(tails)
```

- 時間計算量: $O(n \log n)$
  - $n$ 個の各要素につき二分探索 $O(\log n)$
  - $n = 2500$ の場合、ステップ数: $2500 × \log_2 2500 ≈ 2500 × 11.29 ≈ 28,220$ ステップ、 Pythonの実行時間: $10^{7}$ ステップ/秒 とすると、およそ 2.82 ms
    - `bisect_left()` はC実装なので、実際は約10倍ほど速い
  - $n = 10000$ の場合、およそ 11.29 ms
- 空間計算量: $O(n)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.92aluhxkunm1

- https://github.com/hayashi-ay/leetcode/pull/27
  - Step1の2つの方法、自前実装のSegment Tree
  - Step1 DPの方法での命名にあまり気を使っていなかったが、 `tails` では中身を何を表しているのか分かりづらい
  - Segment Tree、BITは名前だけ知っていたので以下に調べた

- https://github.com/shining-ai/leetcode/pull/31
  - Step1の2つの方法、自前実装のSegment Tree、BIT

- https://github.com/TORUS0818/leetcode/pull/33/changes#r1817995240
  - Segment Treeの解説スレッド

### Segment Tree
- 「ある区間に対する操作（最大値・最小値・合計など）」と「1つの要素の更新」を、どちらも $O(\log n)$ でこなすためのデータ構造
- 座標圧縮: 「実際の値の大きさ」を無視して、値の大小関係だけを保ったまま、値を 0, 1, 2, ... という小さい連番に置き換えるテクニック
  - たとえば LIS の値が `nums = [1, 1000000000, 3, 999999999, 5]` のようなケースだと、値をそのままインデックスとして使おうとすると、実際に登場する値の個数は5個しかないのにセグメント木のサイズが10億必要になってしまう。
  - 計算量：ソートに $O(n \log n)$、辞書作成に $O(n)$ 、 全体で $O(n \log n)$

### BIT（Binary Indexed Tree / Fenwick Tree）
- 「累積和（prefix sum）の取得」と「1点更新」を、どちらも $O(\log n)$ で行うデータ構造

## Step3
### 読みやすく書き直したコード
- DP

```py
class Solution:
    def lengthOfLIS(self, nums):
        max_subsequence_lengths = [1] * len(nums)
        for current_index in range(1, len(nums)):
            for past_index in range(current_index):
                if nums[past_index] < nums[current_index]:
                    max_subsequence_lengths[current_index] = max(max_subsequence_lengths[current_index], max_subsequence_lengths[past_index] + 1)
        return max(max_subsequence_lengths)
```
- 所要時間: 
  - 1回目: 2:41
  - 2回目: 2:37
  - 3回目: 2:37
