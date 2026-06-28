# 103. Binary Tree Zigzag Level Order Traversal
- 問題: https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
- 言語: Python

## Step1
- 二分木を階層ごとにグループ化した値をジグザグに返す
### 方針
- 実行時間の見積: およそ 0.2ms （102と同じ）
- 大まかな方針は102. Binary Tree Level Order Traversalと同じだが、今回は偶数番目の階層の要素の順序を逆転させる。

### AC
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        zigzag_level = []
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

            if len(zigzag_level) % 2 != 0:
                zigzag_level.append(list(reversed(current_level)))
                continue
            zigzag_level.append(current_level)
        
        return zigzag_level
```
- 所要時間: 10:05
- `while pending_nodes` を `while pending_nodes is not None` とするとMLEとなった
  - `deque` オブジェクトは空になっても `None` にはならないため、`is not None` は常に `True` であるため

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.iwb0z9gj4fve
  - なし

- https://github.com/nicah4o/arai60/pull/26
  - C++
  - 節の値を詰める方向を交互に変更する方針

- https://github.com/hiroki-horiguchi-dev/leetcode/pull/27
  - Java
  - 同様に節の値を詰める方向を交互に変更する
  - 若干、こちらの方がメモリ使用量は少ないかもしれない

## Step3
### 読みやすく書き直したコード
```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def zigzagLevelOrder(self, root: Optional[TreeNode]) -> List[List[int]]:
        if root is None:
            return []
        
        zigzag_levels = []
        pending_nodes = deque([root])

        while len(pending_nodes) != 0:
            current_level = []
            
            for _ in range(len(pending_nodes)):
                node = pending_nodes.popleft()
                current_level.append(node.val)

                if node.left:
                    pending_nodes.append(node.left)
                
                if node.right:
                    pending_nodes.append(node.right)

            if len(zigzag_levels) % 2 != 0:
                zigzag_levels.append(list(reversed(current_level)))
                continue
            zigzag_levels.append(current_level)
        
        return zigzag_levels
```
- 所要時間:
  - 1回目: 4:14
  - 2回目: 3:04
  - 3回目: 4:59
