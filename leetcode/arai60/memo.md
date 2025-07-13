# 35. Search Insert Position
* 問題リンク: https://leetcode.com/problems/search-insert-position/
* 言語: Python

# Step1
* 二分探索を使う問題
* 時間計算量 $O(\log n)$ の制約

## 全てのテストケースを通過しないコード
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        init_nums_size = len(nums)
        left = 0
        right = len(nums) - 1
        index = 0
        
        while True:
            mid = (left + right) // 2
            if nums[mid] == target:
                return index
            if nums[mid] > target:
                nums = nums[mid+1:]
            else:
                nums = nums[:mid]
            if len(nums) == 2:
                if nums[-1] <= target:
                    return init_nums_size
            if len(nums) <= 1:
                return index
            index += 1
```
* `target` の要素のインデックスを返すのにCPythonの `list.index` を使うと $O(n)$ かかる
* かといって、`while` ループで `index` をインクリメントしていく方法だと `nums` のサイズが変化するため単なる探索回数を記録しているだけになる
* 20分ぐらい経っていたので正答を見る

## 正答
```python
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] == target:
                return mid
            elif nums[mid] > target:
                right = mid - 1
            else:
                left = mid + 1
            
        return left
```
* そもそも `nums` をスライスしてリストのサイズを変化させるのではなく、 `left` 、 `mid` を移動させる
* `target` が `nums` に存在する場合、その要素のインデックスを返す
* `left` > `right` となった場合、`target` が `nums` の要素に存在しない（最後まで探索した）
* `left` と `right` が重なる場合があるのに気を付ける

# Step2
## 他人のコードを読む
* 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.e13uiztrq2u9
  - 石碑の例えを読むと、気軽に `nums` をスライスして長さを変えることはしない方が良いかも？と思った
  - Pythonではすべての整数は任意の長さをもつ "long" 整数なので、今回の入力の整数範囲では `mid = right + (right - left) // 2` としなくても良い
    - ref. https://docs.python.org/ja/3.12/c-api/long.html
* https://github.com/ryosuketc/leetcode_arai60/pull/30
  * Python
  * 同じアルゴリズム
  * > すべての値が異なる昇順の配列と target という値が与えられる、target があるならばその場所を、ないならば、それよりも左がすべて target よりも小さくなる場所を返せ」
    - このコメントは「挿入位置」をスッキリ説明していて分かりやすい
  * > 不変条件は何なのか、つまり、仕事の引き継ぎだとすると、何を前の人から保証してもらって、後ろの人に何を保証するのか、をはっきりさせましょう、という程度の話なのに、なぜか通じなくて、「要は、簡単に言うと」と執拗に破壊している、と感じています。
    - `nums[mid]` < `target` が真を確認してから `left = mid + 1` としているのだから`left` の指す値を含めて、それより左の値はすべて `target` よりも小さい（ `target` 以上の値は存在しない）ことを保証している
  * `bisect_left` は存在は知っているが真面目に読んだことはない
    - docs: https://docs.python.org/3/library/bisect.html
    - 処理系CPythonの実装: https://github.com/python/cpython/blob/cfbdce72083fca791947cbb18114115c90738d99/Lib/bisect.py#L74
      - `lo` と `hi` を命名規則に使っている

* https://github.com/Fuminiton/LeetCode/pull/41
  - Python
  - 「めぐる式」なる書き方を初めて知った
  - 探索区間は[`left`, `right`], [`left`, `right`)、(`left`, `right`]、(`left`, `right`) のどれか？
    - 探索の終了条件を `left` と `right` が重なる時にするか？差分が1の時か？
  - 幅のある書き方を読める方がより大事
  - > [false, false, false, ..., false, true, true, ture, ..., true] と並んだ配列があったとき、 false と true の境界の位置を求める問題とみなす。
[false, false, false, ..., false, true, true, ture, ..., true] と並んだ配列があったとき、一番左の true の位置を求める問題とみなす。
のいずれかで考える
  - ref. https://discord.com/channels/1084280443945353267/1196498607977799853/1269532028819476562

## 読みやすく書き直したコード

```py
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums)

        while left < right:
            mid = (left + right) // 2
            if nums[mid] < target:
                left = mid + 1
            elif target <= nums[mid]:
                right = mid
        
        return left
```
`elif target <= nums[mid]:` は `else:` でも良いが、個人的にはこちらの方が `target` の値の境界条件が読みやすくて好み

# Step3
```py
class Solution:
    def searchInsert(self, nums: List[int], target: int) -> int:
        left = 0
        right = len(nums)

        while left < right:
            mid = (left + right) // 2
            if nums[mid] < target:
                left = mid + 1
            else:
                right = mid
        
        return left
```
記述量が少ないので暗記にならないように気を付ける
* 解答時間
  - 1回目: 1:00
  - 2回目: 0:56
  - 3回目: 1:02
