# 1. Two Sum
* 問題リンク: https://leetcode.com/problems/two-sum/description/
* 使用言語: Python3

## Step1
* 最大要素数10^4なので、たぶん二重ループで十分だと思った
* Listのindexの取得方法はenumerate()を知っていたのでそれを使った
* 解答時間: 10:04

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i, n in enumerate(nums):
            for j, m in enumerate(nums):
                if i != j:
                    summation = n + m
                    if target == summation:
                        return [i, j]

```
* 実行時間: 4113ms
* メモリ使用量: 18.55MB


## Step2
### 参考にした方々
* https://github.com/rinost081/LeetCode/pull/11/files/07e916a6d3a44aa5f5bfc6f79e0e8b69b63c2b84#diff-fca24c5d0e14d287dcb41d14c53c51015fe58cb17c82216b07c72821c321fd83
  - 辞書（ハッシュテーブル）を使ってkeyに数値、valueにindexを保持して検索する方法
* https://github.com/takumihara/leetcode/pull/1/files/3f122c2d68019f3f302f8681cf7db31de4a24b44#diff-0e987cffc3503d02426293fe621110aa1c7f49e3940742ac0e27bd758446c3ea
  - 引き算を使う発想はなかった
  - `Exception`は正直頭になかった
  - `complement`は補数という意味は知っていた
* 自分のはネストが深くて読みづらい気がする

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        index_by_num = {}
        for i, n in enumerate(nums):
            complement = target - n
            if complement in index_by_num:
                return [i, index_by_num[complement]]
            else:
                index_by_num[n] = i
        raise ValueError("No tuple in the given list.")
```
* `Exception`の代わりに`ValueError`を使った
## Step3
```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        index_by_num = {}
        for i,n in enumerate(nums):
            complement = target - n
            if complement in index_by_num:
                return [i, index_by_num[complement]]
            else:
                index_by_num[n] = i
        raise ValueError("No tuple in the given list")
```
* 1回目: 2:26
* 2回目: 2:15
* 3回目: 1:53

* 実行時間: 0ms
* メモリ使用量: 18.83MB
