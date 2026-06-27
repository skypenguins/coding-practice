# 98. Validate Binary Search Tree
- 問題: https://leetcode.com/problems/validate-binary-search-tree/
- 言語: Python

## Step1
### 方針
- 与えられた二分探索木（BST）を検証するタスク
- 入力長の最大は $10^4$ で、完全二分木をDFS $O(\log n)$ で探索するから、実行時間は数ms
- DFSで、現在の節の値 <= 左節の値、もしくは現在の節の値 >= 右節の値であればFalseを返す
- `[5,4,6,null,null,3,7]` のように、数ホップ離れた右側の節の値が小さい場合にTrueと誤判定する
- 20分経過したため正答を見る

### WA
```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        max_val = float("-inf")
        visited = [(root)]

        while visited:
            current = visited.pop()
            max_val = max(max_val, current.val)

            if current.left:
                if current.val <= current.left.val:
                    return False
                visited.append(current.left)
            
            if current.right:
                if current.val >= current.right.val:
                    return False
                visited.append(current.right)
                  
        return True
```
- `current.left < current < current.right` の親子関係だけを見ているため、BST の条件を完全には判定できない

### 正答
#### スタックDFS版
```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        stack = [(root, float("-inf"), float("inf"))]

        while stack:
            node, lower, upper = stack.pop()

            if node is None:
                continue

            if not (lower < node.val < upper):
                return False

            stack.append((node.left, lower, node.val))
            stack.append((node.right, node.val, upper))

        return True
```

#### 再帰DFS版
```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        def is_valid_nodes(node, lower, upper):
            if node is None:
                return True

            if not (lower < node.val < upper):
                return False

            return is_valid_nodes(node.left, lower, node.val) \ 
               and is_valid_nodes(node.right, node.val, upper)
        
        return is_valid_nodes(root, float("-inf"), float("inf"))
```
- 現在の節から左部分木に進むと、現在の値は上限値になる
- 現在の節から右部分木に進むと、現在の値は下限値になる
  - 定義から考えたら確かに自明だなと思った

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.r5pdnx6retf4

- https://github.com/YukiMichishita/LeetCode/pull/8
  - Python
  - return時にandで繋げて評価するのではなく、右部分木の方から見ていくパターン
  - BFS、中順走査 in-order traversal（再帰、iterative）、ジェネレーターを使った方法
    - 中順走査は方針はシンプルだがコード量が多い気がする

| 方針       | 見ているもの              |
| -------- | ------------------- |
| 範囲制約DFS | 各ノードが祖先から決まる範囲内にあるか |
| 中順走査     | ノードの値が昇順に並ぶか        |

- https://discord.com/channels/1084280443945353267/1192736784354918470/1235116690661179465
- https://github.com/nittoco/leetcode/pull/35
  - Python
  - ジェネレーター＋再帰を使うとループで回せるのでin-order sortでも割と読みやすいと思った
  - > Generator + 再帰、強力ですね。pros cons の cons としては、再帰の深さの限界があること、走りかけの Generator を木の深さ分だけ作るのでそこそこ重いことがありそうですね。
    - src: https://github.com/nittoco/leetcode/pull/35/changes#r1739978684

- https://github.com/SuperHotDogCat/coding-interview/pull/41
- https://github.com/naoto-iwase/leetcode/pull/33#discussion_r2479195403
  - Python
  - 葉に潜ってから走査（後順走査 post-order）

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def isValidBST(self, root: Optional[TreeNode]) -> bool:
        pending_nodes = [(root, -float("inf"), float("inf"))]

        while pending_nodes:
            node, lower_bound, upper_bound = pending_nodes.pop()

            if node is None:
                continue
            
            if not (lower_bound < node.val < upper_bound):
                return False
            
            pending_nodes.append((node.left, lower_bound, node.val))
            pending_nodes.append((node.right, node.val, upper_bound))
        
        return True
```
- 所要時間:
  - 1回目: 2:46
  - 2回目: 2:54
  - 3回目: 2:34
