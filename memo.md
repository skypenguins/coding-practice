# 53. Maximum Subarray
- 問題: https://leetcode.com/problems/maximum-subarray/
- 言語: Python

- おおよそ15分以内に解答する

## Step1
- subarrayなので要素は連続している
  - 最初、順不同（組合せ）と勘違いしていた
- 候補となるsubarrayを取得するwindowを考え、その大きさを元の配列の大きさまでインクリメントしていき、window内の要素の合計を候補の最大値とする
- 同じ部分和の計算を何度も実行しているので、これを解消してTLEを回避したいが15分経っていたので正答を見る

### TLEとなるコード
```py
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return nums[0]
        
        window_size = 1
        start = 0
        end = 0
        result = min(nums)
        while window_size <= len(nums):
            result = max(result, sum(nums[start:end+1]))
            if end == len(nums) - 1:
                start = 0
                end = window_size
                window_size += 1
            else:
                start += 1
                end += 1
        
        return result
```
- 時間計算量: $O(n^{3})$ っぽいので、 $10^{4}$ あたりになるとTLE

### 正答
- ある時点までで最大の連続和を更新していく
- Kadane法というらしい
  - SWEの常識に入る？
    - > 名前がついている上に常識に入っていない程度のもの
      - cf. https://discord.com/channels/1084280443945353267/1206101582861697046/1207405733667410051
- 時間計算量: $O(n)$

```py
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        current_sum = nums[0]
        max_sum = nums[0]

        for num in nums[1:]:
            current_sum = max(num, current_sum + num)
            max_sum = max(max_sum, current_sum)

        return max_sum
```

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.qgjy53psjkn2

- https://discord.com/channels/1084280443945353267/1206101582861697046/1208414507735453747
  - > ちなみに、なんでこの問題が難しいかというと、何かをさせながら、何かをさせながら、何かをさせるからです。
    - https://discord.com/channels/1084280443945353267/1206101582861697046/1208414507735453747
      - Kadane法に至るまでの思考経路
    - https://discord.com/channels/1084280443945353267/1206101582861697046/1209351966329667585
      - 思考実験として、シフト勤務で引き継ぎ資料を残すとしたら何を残すか？

- https://github.com/sakupan102/arai60-practice/pull/33
  - Python
  - $O(n)$ で解く方法
    - 累積和の配列を事前に計算、`nums[j]` ~ `nums[i-1]` の区間の和は `prefix_sums[i] - prefix_sums[j]` で求める
    - 最小累積和の配列を事前に計算。「ここまでで最も小さかった累積和」を記録し続ける
    - 最大部分配列を探す。区間の和 = `prefix_sums[i] - min_prefix_sums[i-1]`  $(j < i)$
    - 2次元空間への拡張といった発展問題に強いと思った
    - 変数名から累積和であることが読み取りづらかった

- https://github.com/olsen-blue/Arai60/pull/32
  - Python
  - > 後ろを振り返った時に、最も標高の低い地点との差を求め、その差でmax()の最大値更新を行い続ければ良い。
    - この考え方はしっくりきた
    - DPを意識した書き方を見て、Kadane法はDPであることに気づいた

## Step3
- Kadene法で解答

```py
class Solution:
    def maxSubArray(self, nums: List[int]) -> int:
        current_sum = nums[0]
        max_sum = nums[0]

        for num in nums[1:]:
            current_sum = max(num, current_sum + num)
            max_sum = max(max_sum, current_sum)

        return max_sum
```
- 所要時間
  - 1回目: 1:51
  - 2回目: 1:05
  - 3回目: 1:10
