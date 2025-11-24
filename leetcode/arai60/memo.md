# 33. Search in Rotated Sorted Array
- 問題: https://leetcode.com/problems/search-in-rotated-sorted-array/
- 言語: Python

## Step1
- 回転した後の配列 `nums` 、整数 `target` が与えられ、`target` が `nums` の要素の値に含まれている場合は `target` の位置を返し、含まれていない場合は `-1` を返す
- 時間計算量 $O(\log n)$ を指定
- 最初、左回転の意味がよくわからなかったが、部分配列をそっくりそのまま（順序を保ったまま）前方に持ってきて連結させることと解釈した
  - 回転軸としての境界の位置かどうか判定するわけではなさそう
- 二分探索はソート済み配列が対象なので、そのままでは二分探索できない？
  - 配列を、回転軸で2つの部分配列に分割してそれぞれの二分探索してみる？
    - と思ったが回転軸（最小値の位置）を探すのに $O(n)$ かかるので、最終的に $O(n \log n)$ になってしまう？
- 15分経っていたので正答を見る

### 正答
```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] == target:
                return mid
            
            if nums[left] <= nums[mid]:
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            else:
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
            
        return -1
```
- 二分探索は探索範囲を縮めていく方法なので別に使えないわけではない
- 通常の二分探索のアルゴリズムを少し修正し、 `mid` の左半分と右半分のどちらの部分配列が完全に昇順にソートされているか調べる（どちらかは必ずソートされている）
- `left` 〜 `mid` の間に「下がる箇所」（＝回転点）がある/ないを調べる
- ソートされている側で、`target` がその範囲内にあるかを判定し、二分探索を実行

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.427rioitx1u6
- https://github.com/sakupan102/arai60-practice/pull/44
  - Python
  - 二分探索を2回に分けて行っている
      1. 回転点を見つける
      2. 通常の二分探索
  - 時間計算量: $O(log n + log n)$
    - 全体として昇順にソートされているので、回転点＝最小値の位置を探すのに二分探索を使えばよい
      - 終了条件 `left == right` となったとき
    - 座標変換 `rotated_middle = (middle + rotate) % len(nums)` 
      - 仮想的な配列の位置 `middle` ＋　回転量 `rotate` ＝ 実際の配列の位置 `rotated_middle` を求める。
      - `% len(nums)` は位置を先頭にループバックさせるための演算

- https://discord.com/channels/1084280443945353267/1233295449985650688/1239594872697262121
  - Python
  - ```py
    class Solution:
    def search(self, nums: List[int], target: int) -> int:
        def cmp(a, b):
            return (a > b) - (a < b)
        def priority(x):
            return (nums[-1] < x) * -2 + cmp(x, target)
        i = bisect_left(nums, priority(target), key=priority)
        if nums[i] != target:
            return -1
        return i
    ```
    - `bisect_left` を使った方法
    - `-2` を掛ける理由
      - `target` より前/後を明確に分離するため
      - cf. https://github.com/Yoshiki-Iwasa/Arai60/pull/36

## Step3
```py
class Solution:
    def search(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] == target:
                return mid
            
            if nums[left] <= nums[mid]:
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            else:
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1
            
        return -1
```
- 解答時間:
  - 1回目: 2:17
  - 2回目: 3:03
  - 3回目: 2:41
