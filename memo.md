# 209. Minimum Size Subarray Sum
- 問題: https://leetcode.com/problems/minimum-size-subarray-sum/
- 言語: Python

## Step1
### 方針
- 見積: $1 <= nums.length <= 10^{5}$ 、計算量: $O(n)$ 、 Pythonの実行時間: $10^{7}$ ステップ/秒 の時、最大 $10^{-2}$ 秒
- 先頭から順に数値を足していって、合計が `target` 以上になったらその時の足した数値（要素）の個数の最小値を求める
- `target = 11, nums = [1,2,3,4,5]` のようなテストケースでWA、正しい方法は尺取り法だったと思い出すも15分経過したため正答を見る

### WA
```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        if len(nums) <= 1:
            return len(nums)
        
        if sum(nums) < target:
            return 0
        
        min_length = float("inf")
        current_length = 0
        current_sum = 0
        for n in nums:
            current_sum += n
            current_length += 1

            if current_sum >= target:
                min_length = min(min_length, current_length)
                current_sum = 0
                current_length = 0

        return min_length
```

### 正答
- 尺取り法（Sliding Window）
  - 右端を伸ばし、条件を満たす間、左端を縮める
```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        if sum(nums) < target:
            return 0
        
        i = 0
        j = 0
        current_sum = 0
        min_length = float("inf")
        while j < len(nums):
            current_sum += nums[j]

            while current_sum >= target:
                current_sum -= nums[i]
                min_length = min(min_length, j - i + 1)
                i += 1
            
            j += 1

        return min_length
```
- `nums` の要素はすべて正の整数なので、次の単調性が成立
  - ウィンドウの右端 `j` を伸ばす → 和は増える
  - ウィンドウの左端 `i` を縮める → 和は減る
- 時間計算量: $O(n)$
  - `i`、 `j` がそれぞれ高々 $n$ 回しか動かないため
- 空間計算量: $O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.p6d6fndbrthh

- https://github.com/SuperHotDogCat/coding-interview/pull/31
  - Python
  - `i`, `j`の命名は `left`, `right` の方が読みやすい
  - 論理的には問題ないが、以下の順序が自然か
    - ```py
        while current_sum >= target:
            min_length = min(min_length, j - i + 1)
            current_sum -= nums[i]
            i += 1
        ```

- https://github.com/olsen-blue/Arai60/pull/50
  - Python
  - 累積和の配列を作れば、要素の値は単調増加であるため二分探索（bisect_left）でも解ける
  - 二分探索（bisect_left）での方法
    ```py
    class Solution:
        def minSubArrayLen(self, target: int, nums: List[int]) -> int:
            prefix_sums = [0] * (len(nums) + 1)
            for i in range(1, len(nums) + 1):
                prefix_sums[i] = prefix_sums[i-1] + nums[i-1]
    
            min_length = sys.maxsize
            for from_index in range(len(prefix_sums)):
                target_sum = prefix_sums[from_index] + target
                target_index = bisect.bisect_left(prefix_sums, target_sum)
                if target_index == len(prefix_sums):
                    break
                min_length = min(min_length, target_index - from_index)
    
            if min_length == sys.maxsize:
                return 0
            return min_length
    ```

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def minSubArrayLen(self, target: int, nums: List[int]) -> int:
        if sum(nums) < target:
            return 0
        
        left = 0
        right = 0
        current_sum = 0
        min_length = float("inf")
        while right < len(nums):
            current_sum += nums[right]

            while current_sum >= target:
                min_length = min(min_length, right - left + 1)
                current_sum -= nums[left]
                left += 1
            
            right += 1

        return min_length
```
- 所要時間: 
  - 1回目: 2:24
  - 2回目: 2:59
  - 3回目: 2:07
