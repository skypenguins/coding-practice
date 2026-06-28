# 102. Binary Tree Level Order Traversal
- 問題: https://leetcode.com/problems/binary-tree-level-order-traversal/
- 言語: Python

## Step1
- 二分木の階層（レベル）ごとに節の値をグループ化して返す
### 方針
- 実行時間の見積: 入力長の最大は2000、計算量 $O(n)$ としてPythonでは $10^{7}$ ステップ/秒としたときに、$2000 / 10^{7} = 2 \times 10^{3} / 10^{7} = 2 \times 10^{-4}$ より、およそ0.2ms
- 階層ごとに探索するので、BFSを使う
- 手作業で考える
  - 最初は情報として、根は階層1であること、階層1では節は1つ、現在の節（根）に左節、右節があるか（＝次の階層の節の個数）が判明する
  - 次の階層を探索するときに、階層番号、節の数、節自体を引き継ぎ情報として渡す
  - 節の個数を階層ごとの探索範囲（ループ回数）とする
- ここまで考えていたら15分経過したので、正答を見る

### 正答
- 方針自体はOK
#### BFS版
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        level_by_level = []
        pending_nodes = deque([root])

        while pending_nodes:
            level_size = len(pending_nodes)
            vals_of_level = []

            for _ in range(level_size):
                node = pending_nodes.popleft()
                vals_of_level.append(node.val)

                if node.left:
                    pending_nodes.append(node.left)
                if node.right:
                    pending_nodes.append(node.right)
            
            level_by_level.append(vals_of_level)
        
        return level_by_level
```
- BFSでは階層番号、節の数は明示的に引き継がなくても良い

### 再帰DFS版
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        result = []

        def build_level_vals(node, level):
            if node is None:
                return
            
            # その階層のリストがまだなければ追加
            if level == len(result):  
                result.append([])

            # level番号で仕分け
            result[level].append(node.val)

            # 前順（pre-order）で再帰
            build_level_vals(node.left, level + 1)
            build_level_vals(node.right, level + 1)

        build_level_vals(root, 0)
        return result
```
- DFSでは明示的に階層番号を引き継ぐ

### 再帰iterative版
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []
        
        result = []
        stack = [(root, 0)]  # (node, level)
        
        while stack:
            node, level = stack.pop()
            
            if level == len(result):
                result.append([])
            
            result[level].append(node.val)
            
            if node.right:
                stack.append((node.right, level + 1))
            if node.left:
                stack.append((node.left, level + 1))
        
        return result
```
- DFSでは明示的に階層番号を引き継ぐ

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.hmkwp3k9djib


- https://github.com/akmhmgc/arai60/pull/22
   - Ruby
   - Rubyの記法に慣れていないため、簡潔だがすぐに理解できなかった
   - 節の個数でループを回す方法は「一つの変数に、2つの違う種類のものを入れておいて、その境界を個数で管理」する方法であり、好ましくないこともあるらしい
     - cf. https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.ho7q4rvwsa1g
  - flat mapで階層ごとに処理

### 個数の情報を使わずに2つのキューで管理するBFS
```py
def level_order(root):
    if root is None:
        return []

    current_queue = deque([root])
    result = []

    while current_queue:
        next_queue = deque()   # 次の階層用
        current_level = []

        while current_queue:
            node = current_queue.popleft()
            current_level.append(node.val)
            if node.left:
                next_queue.append(node.left)
            if node.right:
                next_queue.append(node.right)

        result.append(current_level)
        current_queue = next_queue  # 次の階層へ

    return result
```

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def levelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        level_order = []
        pending_nodes = deque([root])

        while pending_nodes:
            current_level = []

            for _ in range(len(pending_nodes)):
                node = pending_nodes.popleft()
                current_level.append(node.val)

                if node.left:
                    pending_nodes.append(node.left)
                if node.right:
                    pending_nodes.append(node.right)
                
            level_order.append(current_level)
        
        return level_order
```
- 所要時間: 
  - 1回目: 4:49
  - 2回目: 3:06
  - 3回目: 3:08
