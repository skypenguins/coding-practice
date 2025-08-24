# 283. Move Zeroes
* 問題: https://leetcode.com/problems/move-zeroes/
* 言語: Python

## Step1
* 単純にforループで `nums[i] == 0` のときにその位置の要素を削除し後ろに `0` を追加していく方針としたがWA
  - 要素を削除する前に取得した添字（インデックス）が指す要素と削除した後の添字が指す要素はズレる
* このことに気づくのと、コピーを作成せずにin-placeな実装を考えるのに時間がかかった

### 解答（AC）
```py
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        if len(nums) == 1:
            return None
        
        nums_i = 0
        nums_len = len(nums)
        for _ in range(nums_len):
            if nums[nums_i] == 0:
                nums.pop(nums_i)
                nums.append(0)
                nums_i -= 1
            nums_i += 1
```
* 解答時間: 40:00
* 時間計算量: $O(n^2)$
  - `pop()`は $O(n)$

## Step2
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.v62rdhwkdymb
* https://github.com/fhiyo/leetcode/pull/54
  - Python
  - `nonzeros` に非ゼロな値をコピーして退避し、それを`nums` に追加していく方法
    - （コピー作成してもOKなんだ…）
  - 非ゼロ要素とゼロ要素を交換するクイックソート的なアルゴリズム
    - forループ、whileループを使った方法
      - リストの代わりにジェネレーターとセイウチ演算子を使う方法
  - `nums` から `0` が存在しなくなるまで `nums.remove(0)` を試行する方法
* https://github.com/olsen-blue/Arai60/pull/55
  - Python
  - 上記と同じ方法
    - 挿入ソートっぽい方法
    - バブルソートっぽい方法
      - 探索範囲がすべて非ゼロの場合、その時点でループを打ち切る

### 読みやすく書き直したコード
* 非ゼロ要素を先頭に移動する部分とゼロ要素で埋める部分で分ける方法
```py
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        non_zero_pos = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            nums[non_zero_pos] = nums[i]
            non_zero_pos += 1
        
        for i in range(non_zero_pos, len(nums)):
            nums[i] = 0
```

## Step3
```py
class Solution:
    def moveZeroes(self, nums: List[int]) -> None:
        non_zero_pos = 0
        for i in range(len(nums)):
            if nums[i] == 0:
                continue
            nums[non_zero_pos] = nums[i]
            non_zero_pos += 1
        
        for i in range(non_zero_pos, len(nums)):
            nums[i] = 0
```
* 解答時間
  - 1回目: 1:54
  - 2回目: 1:38
  - 3回目: 1:37
