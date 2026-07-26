# 560. Subarray Sum Equals K
- 問題: https://leetcode.com/problems/subarray-sum-equals-k/
- 言語: Python

## Step2
- おそらくTLEになるだろうが、まずはSliding Windowで考えてみる
- 想定通りTLEになったのでハッシュマップで累積和の再利用の方針で改善しようとしてみたが15分超えそうなので正答を見る

### TLEなコード
```py
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        num_subarrays = 0

        right_index = 0
        for left_index in range(len(nums)):
            while right_index <= len(nums):
                candidate_range = nums[left_index:right_index]
                if len(candidate_range) != 0 and sum(candidate_range) == k:
                    num_subarrays += 1
                
                right_index += 1
            
            right_index = left_index
        
        return num_subarrays
```
- 時間計算量: $(n^{3})$
  - $O(n^{2})$ 回のループ × 各回 $O(n)$ のスライスとsum計算
  - $n = 2 × 10^4, 10^7$ ステップ毎秒とすると、$(2×10^{4})^{3} / 6 ≈ 1.3×10^{12} 回$ で、 $1.3×10^{12} / 10^{7} ​≈ 1.3×10^{5} 秒 ≈ 37時間（約1.5日）$

### 正答
#### 累積和＋ハッシュマップ
```py
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        num_subarrays = 0
        prefix_sum = 0
        # 累積和の出現回数を記録（初期値: 累積和0が1回出現している状態）
        prefix_count = defaultdict(int)
        prefix_count[0] = 1

        for num in nums:
            prefix_sum += num
            # 擬似コードによる式変形
            # sum(left..right) == k
            # <=> prefix_sum[right] - prefix_sum[left - 1] == k
            # <=> prefix_sum[left - 1] == prefix_sum - k
            num_subarrays += prefix_count[prefix_sum - k]
            prefix_count[prefix_sum] += 1

        return num_subarrays
```
- 時間計算量: $O(n)$
  - $n = 2 × 10^4, 10^7$ ステップ毎秒とすると、 $2 × 10^4 / 10^7 = 2 × 10^{-3} 秒 = 2 \rm{ms}$
- 空間計算量: $O(n)$

#### 方針
- 区間和 `sum(nums[left:right])` は `prefix_sum[right] - prefix_sum[left - 1]` と等価
- 「和が `k` になる区間」を探す代わりに、各位置で「値 `prefix_sum - k` の累積和が過去に何回出現したか」をハッシュマップで探す
- スライスや `sum()` の再計算が一切不要になり、1周のループだけで全区間の情報を扱える

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.bp0g0ai41eln

- https://github.com/goto-untrapped/Arai60/pull/28
  - Java
  - 同じ方針だが、累積和の保持に配列を使う例
    - 配列のインデックスがそのままキーになる、条件によってはこの方法でも良さそう
    - 計算量: $O(n^2)/O(n)$
  - 和が K となる区間を列挙する問題とみなす、鉄道駅の標高**差**の例え
    - cf. https://discord.com/channels/1084280443945353267/1233603535862628432/1252232545056063548
    - cf. https://discord.com/channels/1084280443945353267/1195700948786491403/1253638682829783061

- https://github.com/Hurukawa2121/leetcode/pull/16
  - C++
  - 現実世界での性能不足はどういう流れを辿るか: https://github.com/Hurukawa2121/leetcode/pull/16#discussion_r1898332261

- https://github.com/katataku/leetcode/pull/15
  - Python
  - 同じ方法
  - やはり `prefix_sum_to_count` がわかりやすい

- https://discord.com/channels/1084280443945353267/1300342682769686600/1357376133695541480
  - Java
  - Java特有のよく知らないメソッドが結構出てきて、メソッド名から想像しながら読んだ


## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def subarraySum(self, nums: List[int], k: int) -> int:
        num_subarrays = 0
        prefix_sum = 0
        prefix_sum_to_count = defaultdict(int)
        prefix_sum_to_count[0] = 1

        for num in nums:
            prefix_sum += num
            num_subarrays += prefix_sum_to_count[prefix_sum - k]
            prefix_sum_to_count[prefix_sum] += 1

        return num_subarrays
```
- 所要時間:
  - 1回目: 1:59
  - 2回目: 1:55
  - 3回目: 2:04
