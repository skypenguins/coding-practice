# 153. Find Minimum in Rotated Sorted Array
- 問題: https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/
- 言語: Python

## Step1
- 回転された配列から最小値を $O(\log n)$ で返す
### 方針
- 配列から最小値を返す問題と同じだと思い、以下で解答

### AC（？）
```py
class Solution:
    def findMin(self, nums: List[int]) -> int:
        return min(nums)
```
- 所要時間: 0:10
- 多分 `min()` に相当する処理を書かないといけないのだと思った

### 方針
- 配列の値の最大値（変曲点） `pivot` の次の値が最小値である
- `pivot` を線形に探すと最悪時間計算量 $O(n)$ となり、 $O(\log n)$ を達成できない
  - 線形でいいならそもそも初めから最小値を探せば良い
- 回転した回数が判明すれば、自ずと最小値は判明する
- 回転した回数をどう求めるか？
  - 例えば: `[11,13,15,17]` は4回だが、どう求めるか？
- 15分経過していたので正答を見る

### とりあえずの回答（WA）
```py
class Solution:
    def findMin(self, nums: List[int]) -> int:
        mid = len(nums) // 2
        min_n = float("inf")
        
        for i in range(mid, len(nums)):
            if nums[i] < min_n:
                min_n = nums[i]
        
        return min_n
```

### 正答
```py
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = -1, len(nums) - 1
        while right - left > 1:
            mid = (left + right) // 2
            if nums[mid] > nums[right]:
                left = mid
            else:
                right = mid
        return nums[right]
```
- 回転した回数の情報は別にいらない
- 二分探索のアプローチで素直に考えれば良い
#### 方針
- 半開区間 `(left, right]` 
- 不変条件: `right`  は常に現時点での最小値の候補
- `right` は必ず右半分側に留まる
- `left` は必ず左半分側に留まる

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.tzbo5j6mbqc2

- CPythonの `min()` 実装: 

- https://github.com/thonda28/leetcode/pull/7
  - 正答はこの方のものを参照
  - 二分探索の時に考えるべきこと: https://github.com/thonda28/leetcode/pull/7#issuecomment-3287682725

- https://discord.com/channels/1084280443945353267/1230079550923341835/1233971372946882600
  - 閉区間 `[left, right]`
  - 
    ```py
    class Solution:
      def findMin(self, nums: List[int]) -> int:
          left, right = 0, len(nums) - 1
          while left < right:
              mid = (left + right) // 2
              if nums[mid] > nums[right]:
                  left = mid + 1
              else:
                  right = mid
          return nums[left]
    ```
  - Pythonのbisect: https://docs.python.org/3/library/bisect.html
  - 考察の選択肢を広げる

- https://discord.com/channels/1084280443945353267/1192736784354918470/1236853602262319125
  - Pythonのbisectを使った方法
  - 開区間の方法と同じ
  - 
    ```py
    class Solution:
      def findMin(self, nums: List[int]) -> int:
          return nums[bisect_left(nums, -nums[-1], key=lambda x: -x)]
    ```
  - 等価コード
    ```py
    lo, hi = 0, len(nums)
    while lo < hi:
        mid = (lo + hi) // 2
        if nums[mid] > nums[-1]:
            lo = mid + 1
        else:
            hi = mid
    return nums[lo]
    ```

## Step3
- 閉区間の方法
```py
class Solution:
    def findMin(self, nums: List[int]) -> int:
        left, right = 0, len(nums) - 1
        while left < right:
            mid = (left + right) // 2
            if nums[mid] > nums[right]:
                left = mid + 1
            else:
                right = mid
        return nums[left]
```
- 所要時間:
  - 1回目: 2:09
  - 2回目: 1:27
  - 3回目: 1:36
