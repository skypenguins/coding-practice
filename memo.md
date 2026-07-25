# 373. Find K Pairs with Smallest Sums
- 問題: https://leetcode.com/problems/find-k-pairs-with-smallest-sums/
- 言語: Python

## Step1
### 方針
- `num1`, `num2` は昇順であることが保証されているため、 `347. Top K Frequent Elements` とほぼ同じ方針で解いてみる
- MLEになる。後ろの方の組はどう考えても不要だなと思いつつも、この時点で20分経過していたため正答を見る

### MLEなコード
```py
class Solution:
    def kSmallestPairs(
        self, nums1: List[int], nums2: List[int], k: int
    ) -> List[List[int]]:
        sum_to_pair = defaultdict(list)

        for num1 in nums1:
            for num2 in nums2:
                total = num1 + num2
                sum_to_pair[total].append([num1, num2])

        flatten_pairs = []
        for _, pairs in sorted(sum_to_pair.items(), key=lambda x: x[0]):
            for pair in pairs:
                flatten_pairs.append(pair)

        return flatten_pairs[:k]
```
- 制約上 `nums1`, `nums2` はそれぞれ最大 $10^{5}$ 個の要素を持ちうるため、最悪ケースでは $10^{10}$ 個の組をメモリに保持しようとすることになる

### 正答
#### 方針
- 全部の組を作ってからソートではなく、最初に積むのは `nums1[i] + nums2[0]` の形の組のみとする（最大 `min(k, len(nums1))` 個）。
- `nums1` と `nums2` はソート済みという前提があるので、`nums2[0]` と組んだものが各 `i` に対する最小和

#### 解答
```py
class Solution:
    def kSmallestPairs(
        self, nums1: List[int], nums2: List[int], k: int
    ) -> List[List[int]]:
        if not nums1 or not nums2:
            return []

        candidates = []
        # 初期候補: nums1の各要素 と nums2[0] の組
        for i in range(min(k, len(nums1))):
            heapq.heappush(candidates, (nums1[i] + nums2[0], i, 0))

        pairs = []
        while candidates and len(pairs) < k:
            _, i, j = heapq.heappop(candidates)
            pairs.append([nums1[i], nums2[j]])

            if j + 1 < len(nums2):
                heapq.heappush(candidates, (nums1[i] + nums2[j + 1], i, j + 1))

        return pairs
```
- 時間計算量: $O(k log k)$
- 空間計算量: $O(k)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.527w0lse8gbd

- https://github.com/hayashi-ay/leetcode/pull/66
  - Python
  - `seen` のような訪問済みのリストを別で持っておく方法

- https://github.com/fhiyo/leetcode/pull/13
  - Python
  - namedTuple は使ったことがなかったので勉強になった
    - cf. https://docs.python.org/3/library/collections.html#collections.namedtuple
  - k個未満の時に時にあるだけ全部返すというフォールバック
    - cf. https://discord.com/channels/1084280443945353267/1201211204547383386/1206515949579145216

- https://github.com/TORUS0818/leetcode/pull/12
  - Python
  - 座標で管理する方法
  - 各行、各列でどこまで入れたかの配列をもつ
    - 意図と操作を分離する: https://github.com/TORUS0818/leetcode/pull/12/changes#r1697964514

- https://github.com/nittoco/leetcode/pull/33
  - Python
  - ジェネレータの再帰について: https://github.com/nittoco/leetcode/pull/33/changes#r1705956329

- https://github.com/Yoshiki-Iwasa/Arai60/pull/9
  - Rust
  - 優先度付きキューに何を入れるか: https://github.com/Yoshiki-Iwasa/Arai60/pull/9#discussion_r1647019606

## Step3
```py
class Solution:
    def kSmallestPairs(
        self, nums1: List[int], nums2: List[int], k: int
    ) -> List[List[int]]:
        if nums1 is None or nums2 is None:
            return []

        candidates = []
        for i in range(min(k, len(nums1))):
            heapq.heappush(candidates, (nums1[i] + nums2[0], i, 0))

        pairs = []
        while candidates is not None and len(pairs) < k:
            _, i, j = heapq.heappop(candidates)
            pairs.append([nums1[i], nums2[j]])

            if j + 1 < len(nums2):
                heapq.heappush(candidates, (nums1[i] + nums2[j + 1], i, j + 1))

        return pairs
```
- 所要時間:
  - 1回目: 2:44
  - 2回目: 3:22
  - 3回目: 2:46
