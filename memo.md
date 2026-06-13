# 46. Permutations
- 問題: https://leetcode.com/problems/permutations/
- 言語: Python

## Step1
- 与えられた配列から、普通の順列を配列の配列として返す
- 順列は樹形図を描いて求める
  - n個の要素がある配列をソートして、小さい値を訪れたらその値を含めずに残りn-1個の要素を小さい順に並べる、これを繰り返す
- スタックを使った解法が思いつきそうで思いつかず15分以上経っていたので正答を見る

### 正答
- 節を訪れる途中で何を覚えておくか？を考える
- 出典: https://leetcode.com/problems/permutations/solutions/993970/python-4-approaches-visuals-time-complex-053h

1. 再帰＋バックトラック（暗黙的なスタック）
- N!個の葉に訪れるには、N回（＝深さ）の呼び出しが必要
- 葉ではpathそのものを append するのではなく、パスのコピー `path[::]` を追加する
  - 結果リストにpathを追加した後でも、そのパスはエイリアス（別名）に対する変更の影響を受けるから
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        def permute_helper(nums, path=[], result=[]):
            if not nums:
                result.append(path[::]) # path のコピーを result に追加
                return
            
            for i in range(len(nums)):
                new_nums = nums[:i] + nums[i+1:] # 順列の定義から
                path.append(nums[i])
                permute_helper(new_nums, path, result)
                path.pop() # 選択を取り消す（バックトラック）
            
            return result

        return permute_helper(nums)
```
- 時間計算量: $O(N*N!)$
- 空間計算量: $O(N!)$

2. 再帰＋バックトラックなし
- 
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        def permute_helper(nums, path=[], result=[]):
            if not nums: 
                result.append(path) 
                return

            for i in range(len(nums)): 
                new_nums = nums[:i] + nums[i+1:]
                new_path = path + [nums[i]] # 毎回新しいリストを生成
                permute_helper(new_nums, new_path, result) 
            
            return result
        
        return permute_helper(nums)
```
- 時間計算量: $O(N*N!)$
- 空間計算量: $O(N!)$
  - 毎回リストを生成するため若干メモリ効率は悪い

3. DFS（スタック）
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        stack = [(nums, [])]

        while stack:
            nums, path = stack.pop()
            if not nums:
                result.append(path)

            for i in range(len(nums)):
                new_nums = nums[:i] + nums[i+1:]
                stack.append((new_nums, path + [nums[i]]))

        return result
```
- 時間計算量: $O(N*N!)$
- 空間計算量: $O(N!)$

4. BFS（キュー）
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        queue = deque()
        queue.append((nums, []))

        while queue:
            nums, path = queue.popleft()
            if not nums:
                result.append(path)
            
            for i in range(len(nums)):
                new_nums = nums[:i] + nums[i+1:]
                queue.append((new_nums, path + [nums[i]]))
            
        return result
```
- 時間計算量: $O(N*N!)$
- 空間計算量: $O(N!)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.xe8lirtynkse

1. https://github.com/Ryotaro25/leetcode_first60/pull/54
  - C++
  - 自分の場合はPythonのスライスを使って重複を防いでいたが、これのStep4ではSetを使っている
    - 個人的にはあまり直観的ではないかなと思った
    - Setの検索は定数時間のため、時間計算量の面では有利？

2. 処理系CPythonでのPermutation実装: https://github.com/python/cpython/blob/main/Modules/itertoolsmodule.c#L2725
  - 出力の長さを指定できる
  - CPythonの実装は「`indices` 配列をスワップして順列を生成する」という全く別の設計で、再帰もバックトラックも使っていないらしい by Claude

## Step3
- DFS（スタック）
```py
class Solution:
    def permute(self, nums: List[int]) -> List[List[int]]:
        result = []
        stack = [(nums, [])]

        while stack:
            nums, path = stack.pop()
            if not nums:
                result.append(path)

            for i in range(len(nums)):
                new_nums = nums[:i] + nums[i+1:]
                stack.append((new_nums, path + [nums[i]]))

        return result
```
- 所要時間
  - 1回目: 2:57
  - 2回目: 2:36
  - 3回目: 3:04
