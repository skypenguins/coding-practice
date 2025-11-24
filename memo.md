# 31. Next Permutation
- 問題: https://leetcode.com/problems/next-permutation/
- 言語: Python

## Step1
- まず手作業でどうやるか考える
  - 順列を生成していく
    - 昇順にソートした配列から樹形図を描いていく
    - 3つの要素を持つ配列の場合
        - 1列目: 昇順に縦に数字を3行並べる
        - 2列目: 1列目の数字以外を縦に2行並べる
        - 3列目: 2列目の数字以外を縦に1行並べる
  - 作成途中で与えられた順列と一致したらフラグを立てて、次の順列の生成でin-placeで返す
- ただ最悪 $n!$ 個生成しないといけないのでTLEになりそう
- 思考時間と実装で30分経ったので正答を見る

<details>
<summary>途中まで書いたコード</summary>

```py
class Solution:
    def nextPermutation(self, nums: List[int]) -> None:
        """
        Do not return anything, modify nums in-place instead.
        """
        sorted_nums = sorted(nums, reverse=True)
        nodes = sorted_nums
        permutations = []
        permutation = []
        count = 0
        
        while nodes:
            node = nodes.pop()

            if len(permutation) == len(nums):
                count += 1
                permutations.append(permutation)
                permutation = []
                continue
            
            permutation.append(node)
```

</details>

### 正答
```py
class Solution:
    def nextPermutation(self, nums):
        pos = -1
        
        # step 1: find breaking point
        for i in range(len(nums) - 2, -1, -1):
            if nums[i] < nums[i + 1]:
                pos = i
                break
        
        # if there is no breaking point
        if pos == -1:
            # in-place modification
            nums[:] = list(reversed(nums))
            return

        # step 2: find next greater element and swap
        for i in range(len(nums) - 1, -1, -1):
            if nums[i] > nums[pos]:
                nums[pos], nums[i] = nums[i], nums[pos]
                break
        
        # step 3: reverse the rest right half
        nums[pos + 1:] = list(reversed(nums[pos + 1:]))
```

- 基本的な方針: 辞書順で「次の順列」を求めるには、できるだけ右側の要素を変更する
  1. ブレークポイント（境界値）を求める
      - 右から要素を訪れていき、初めて `nums[i] < nums[i+1]` となる位置（ `nums[i]` より右側はすべて降順）を探す
      - 降順の配列＝その範囲ではもう増やせない配列
        - 例: `[1,3,5,4,2]` → 右端の `[5,4,2]` は降順なので最大、変更するには3を増やす
        - このときの `i` を `pos` とする
  2. 次に大きい要素を探してswap
      - 位置 `pos` の値より大きい最小の値の要素を探し、値を入れ替える（swap）
      - `nums[pos]` をできるだけ小さく増やしたい（次の順列は最小の増加）
      - 右側（降順）から探すと、 `nums[pos]` より大きい要素の中で最小のものが見つかる
        - 例： `[1,3,5,4,2]`
        - `pos = 1`（値3）、右側は `[5,4,2]` で降順
        - 3より大きい最小の値 → 4
        - Swap後: `[1,4,5,3,2]`
  3. 右側を反転（reverse）
        - swapした後、 `pos + 1` 以降はまだ降順のまま
        - この部分を最小の順列にして次の順列を得る
        - 例: ` [1,4,5,3,2]`
          - `pos + 1` 以降の `[5,3,2]` をreverse
          - 結果: `[1,4,2,3,5]`
            - これが `[1,3,5,4,2]` の次の順列
  - エッジケース
    - `pos == -1` の場合（全体が降順）：
      - これは最大の順列 → 次は最小の順列（全体を昇順にする）
      - 例: `[3,2,1]` → `[1,2,3]`

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.5y5ifenu4u32

- https://github.com/shining-ai/leetcode/pull/58
  - Python
  - 同じ方針
  - breakせず書くとネストが深くなり少し読みづらい
  - cf. C++の `std::next_permutation` https://en.cppreference.com/w/cpp/algorithm/next_permutation.html

- https://github.com/usatie/leetcode/pull/2
  - C++
  - 同じ方針
  - 変数名は `pos` より `pivot` のほうがよさそう

- https://github.com/Ryotaro25/leetcode_first60/pull/63
  - C++
  - 同じ方針
  - `nums` の探索では `while` よりは `for` の方が無限ループになりにくくていいと思った

- https://github.com/olsen-blue/Arai60/pull/59
  - Python
  - 同じ方針
  - 時間計算量は $O(n^2)$ 
    - 自分のは二重ループでないので $O(n)$ か？
    - cf. https://github.com/olsen-blue/Arai60/pull/59#discussion_r2030531369

### 読みやすく書き直したコード
```py
class Solution:
    def nextPermutation(self, nums):
        pivot = -1

        for i in range(len(nums) - 2, -1, -1):
            if nums[i] < nums[i+1]:
                pivot = i
                break
        
        if pivot == -1:
            nums[:] = list(reversed(nums))
            return
        
        for i in range(len(nums) - 1, -1, -1):
            if nums[i] > nums[pivot]:
                nums[i], nums[pivot] = nums[pivot], nums[i]
                break
        
        nums[pivot + 1:] = list(reversed(nums[pivot + 1:]))
```

## Step3
```py
class Solution:
    def nextPermutation(self, nums):
        pivot = -1

        for i in range(len(nums) - 2, -1, -1):
            if nums[i] < nums[i+1]:
                pivot = i
                break
        
        if pivot == -1:
            nums[:] = list(reversed(nums))
            return
        
        for i in range(len(nums) - 1, -1, -1):
            if nums[i] > nums[pivot]:
                nums[i], nums[pivot] = nums[pivot], nums[i]
                break
        
        nums[pivot + 1:] = list(reversed(nums[pivot + 1:]))
```
- 解答時間: 
  - 1回目: 2:35
  - 2回目: 2:43
  - 3回目: 3:08
