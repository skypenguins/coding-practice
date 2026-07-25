# 347. Top K Frequent Elements
- 問題: https://leetcode.com/problems/top-k-frequent-elements/
- 言語: Python

## Step1
### 方針
- key: 「`nums` の値」、value: 「`nums` の値の出現頻度」を持つハッシュマップ（dict）を作る
- 「`nums` の値の出現頻度」を基準に降順にソートし、上位 k のkeyを持つリストを返す
- 所要時間: 8:00

### AC
```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_frequency = defaultdict(int)

        for num in nums:
            num_to_frequency[num] += 1

        sorted_num_to_frequency = dict(
            sorted(num_to_frequency.items(), key=lambda x: x[1], reverse=True)
        )

        top_k = []
        for i in range(k):
            top_k.append(list(sorted_num_to_frequency.keys())[i])

        return top_k
```
- 今回の問題の場合、よく考えたら `num_to_frequency` はハッシュマップじゃなくて単なるリストで良かったかも
- 最後のループはスライスで良かった
- 時間計算量: $O(k・u)$
  - 最悪ケース $k = u = 20001$（全要素ユニーク）、$10^{7}$ ステップ/秒として：
    - カウント: $n = 10^{5}$ とすると、 $10^{5} / 10^{7} = 10^{-2} = 約 0.01 秒 $ 
    - ソート: $u \log u ≈ 20001×14.3 ≈ 2.9×10^5 = 約 0.03 秒$
    - ループ（C実装）: $k×u ≈ 20001² ≈ 4×10^8 = 4×10^8 / 10^8〜10^9 ≈ 0.4〜4秒$
- 空間計算量: $O(n)$

## Step2
### 他の方針
- 以下は調べた例

#### 方針1: ヒープ（優先度キュー）を使う方法
- `Counter(nums)` で各値の出現頻度を数える（$O(n)$）
- `heapq.nlargest(k, ...)` は内部的にサイズ `k` のヒープを使って、全要素（`u` 個）を1回ずつ見ながら「上位k個」を維持する
  - 公式ドキュメント: https://docs.python.org/3/library/heapq.html
- 全体を並べ替える必要がないので、`u` 個の要素に対して各 $O(\log k)$ の操作で済み、合計 $O(u \log k)$

```py
import heapq
from collections import Counter


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)
        return heapq.nlargest(k, count.keys(), key=count.get)
```

- 時間計算量: $O(n \log k)$

#### 方針2: バケットソート
- 出現頻度の理論上の最大値は `n`（配列が全部同じ値の場合）なので、バケット配列「頻度 → 値のリスト」を `n+1` 個用意する
- 各値をその頻度に対応するバケットに放り込む（$O(u)$）
- バケットを頻度の高い順（`n` から `1`）に走査し、`k` 個集まったら終了

```py
from collections import Counter


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)
        n = len(nums)

        # buckets[freq] = そのfreqを持つ値のリスト
        buckets = [[] for _ in range(n + 1)]
        for num, freq in count.items():
            buckets[freq].append(num)

        result = []
        for freq in range(n, 0, -1):
            for num in buckets[freq]:
                result.append(num)
                if len(result) == k:
                    return result
        return result
```
- 時間計算量: $O(n)$

#### 方針3: `Counter.most_common(k)` を使う方法
- `Counter.most_common(k)` は標準ライブラリの機能で、内部的には `k` が全体の要素数より十分小さい場合に `heapq.nlargest` を使い、そうでなければソートにフォールバックする実装になっている
  - 公式ドキュメント: https://docs.python.org/3/library/collections.html#collections.Counter
  - CPython: https://github.com/python/cpython/blob/66e313f471516bfa04c5c8966c159fafe6be082e/Lib/collections/__init__.py#L625

```py
from collections import Counter


class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        count = Counter(nums)
        return [num for num, freq in count.most_common(k)]
```

### 他の人のコードを読む
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.dkkbub5o1tvz

- https://github.com/fhiyo/leetcode/pull/12
  - Python
  - `Counter` を使った方法
    - 出題者の意図として `Counter` は意図していないっぽい
  - Quick Selectを知らなかったので調べた
   - 「配列の中で `k` 番目に小さい（または大きい）要素を見つける」ことに特化したアルゴリズム
   - クイックソートのpartition処理だけを流用し、片側だけを再帰的に探索することで、ソートせずに目的の要素を高速に見つける
   - 平均計算量: $O(n)$
     - cf. Quick SortとQuick Selectについて: https://discord.com/channels/1084280443945353267/1183683738635346001/1185972070165782688
       - クイックソートで何が常識か
       - Quick Selectも常識の範囲内らしい

- https://github.com/sakupan102/arai60-practice/pull/10
  - Python
  - 大きい順に並べた状態で管理という観点だと、平衡木（平衡二分探索木）が使える。平衡木の実装にはLinkedHashMapが使われ、LinkedHashMapはLRUの実装にも使われる
    - ストリーミングデータ（頻度が動的に変化し続けるようなケース）で特に有利
    - cf. https://discord.com/channels/1084280443945353267/1227073733844406343/1231268645628416020

- https://github.com/katataku/leetcode/pull/9
  - Python
  - 1行に書く関数は7個が限界、実行順序を考えても目が左右に動くのは避けたい
    - cf. https://github.com/katataku/leetcode/pull/9#discussion_r1860305454

- https://github.com/fuga-98/arai60/pull/10
  - Python
  - key functionでdictのkeyだけを取得するようにする
    - cf. https://github.com/fuga-98/arai60/pull/10/changes#r1967591652

- https://github.com/potrue/leetcode/pull/9
  - Python
  - 実際の仕事での状況を想像する
    - cf. https://github.com/potrue/leetcode/pull/9#discussion_r2083755650

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def topKFrequent(self, nums: List[int], k: int) -> List[int]:
        num_to_frequency = defaultdict(int)

        for num in nums:
            num_to_frequency[num] += 1

        sorted_num_to_frequency = sorted(
            num_to_frequency, key=num_to_frequency.get, reverse=True
        )

        return sorted_num_to_frequency[:k]
```
- 所要時間:
  - 1回目: 2:38
  - 2回目: 1:39
  - 3回目: 1:33
