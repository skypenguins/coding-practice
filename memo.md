# 78. Subsets
- 問題: https://leetcode.com/problems/subsets/
- 言語: Python

## Step1
- 集合の部分集合を求める
- 順列を求める時の方法を少し変形する方針
  - `nums` から 1個取り除いた配列を再帰的に結果として保持する
- 以下のコードだと `new_nums not in candidates` しか見ていないので、すでに `result` に入った部分集合が後からまた追加されてしまう
- 時間計算量は $O(n 2^{n})$ だが、入力は最大10個なので部分集合の個数は $2^{10} = 1024$ 個、実行時間は数ms
- 15分経過していたので正答を見る

### WAなコード
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        candidates = [nums]

        while candidates:
            current_nums = candidates.pop()
            result.append(current_nums)

            for i in range(len(current_nums)):
                new_nums = current_nums[:i] + current_nums[i+1:]
                if new_nums not in candidates:
                    candidates.append(new_nums)

        return result
```

### 正答
#### 再帰＋バックトラック
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []

        def subsets_helper(start: int, path: List[int]):
            result.append(path[:])
            
            # このif文でベースケースを明示しなくても、
            # 次のループで `start == len(nums)` であるときに関数が終了しreturnする
            if start == len(nums):
                return

            for i in range(start, len(nums)):
                path.append(nums[i])
                subsets_helper(i+1, path)
                path.pop()
        
        subsets_helper(0, [])
        return result
```

#### ループ（iterative）＋バックトラック
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        candidates = [(0, [])]

        while candidates:
            start, path = candidates.pop()
            # pathは後から変更しないので path[:] でなくて良い
            result.append(path)

            for i in range(start, len(nums)):
                candidates.append((i+1, path + [nums[i]]))
        
        return result
```

##### リスト（配列）のコピーをスタックに追加する場合
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        candidates = [(0, [])]

        while candidates:
            start, path = candidates.pop()
            result.append(path[:])

            for i in range(start, len(nums)):
                path.append(nums[i])
                candidates.append((i+1, path[:]))
                path.pop()
        
        return result
```

#### 元のコードを生かしたバージョン
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        candidates = [nums]
        seen = set()

        while candidates:
            current_nums = candidates.pop()
            result.append(current_nums)

            for i in range(len(current_nums)):
                new_nums = current_nums[:i] + current_nums[i+1:]
                key = tuple(new_nums)

                if key not in seen:
                    seen.add(key)
                    candidates.append(new_nums)
        
        return result
```

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.2x7tej35wxct

- https://github.com/fhiyo/leetcode/pull/51
  - Python
  - 「選ぶ or 選ばない」で分岐
    - ジェネレータ `yield` を使った2分岐DFS（再帰）
    - ビットマスクによる反復的な全探索
  - 反復的に部分集合を増やす方法
    - 再帰やループを使う方法ばかり考えていたので、今回はこの方法の方が分かりやすいと思った

- https://github.com/Ryotaro25/leetcode_first60/pull/55
  - C++
  - `auto` を使うとコピーが発生するのは初めて知った

## Step3
### ループ（iterative）＋バックトラック
```py
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        result = []
        candidates = [(0, [])]

        while candidates:
            start, path = candidates.pop()
            # pathは後から変更しないので path[:] でなくて良い
            result.append(path)

            for i in range(start, len(nums)):
                candidates.append((i+1, path + [nums[i]]))
        
        return result
```
- 所要時間: 
  - 1回目: 1:42
  - 2回目: 1:30
  - 3回目: 1:47
