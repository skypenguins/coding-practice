# 112. Path Sum
* 問題: https://leetcode.com/problems/path-sum/
* 言語: Python

## Step1
* 深さ優先探索（DFS）でノードを探索しながら、そのノードまでの和を情報として保持していく
* スタックにノードオブジェクトとそのノードまでの和の組を積んでいく

### 解答（AC）
```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        
        node_and_val = [(root, root.val, root.val)]

        while node_and_val:
            node, val, sum = node_and_val.pop()
            if node.left is None and node.right is None:
                if sum == targetSum:
                    return True
            if node.right:
                node_and_val.append((node.right, node.right.val, node.right.val + sum))
            if node.left:
                node_and_val.append((node.left, node.left.val, node.left.val + sum))
        
        return False
```
* 解答時間: 20:03
* 時間計算量: $O(n)$
* 空間計算量: $O(n)$
* `val` は変数に置いたが積まなくて良い
* Step2で再帰バージョンも書いてみる

## Step2
### 他の人のコードを読む
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.ed3x3pkyeqkp
  * https://github.com/rossy0213/leetcode/pull/14
    - Java
    - 再帰DFS
    - 与えられたノード値に直接足し上げているのが気になる
  * https://github.com/SuperHotDogCat/coding-interview/pull/37
    - Python
    - `targetSum` を引き算していき最終的に0になるかどうかで判定
    - 再帰DFS
    - ```py
        class Solution:
        def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
            if root is None:
                return False
            
            targetSum = targetSum - root.val
            if targetSum == 0 and root.left is None and root.right is None:
                return True
            return self.hasPathSum(root.left, targetSum) or self.hasPathSum(root.right, targetSum)
        ```

# Step3
```py
class Solution:
    def hasPathSum(self, root: Optional[TreeNode], targetSum: int) -> bool:
        if root is None:
            return False
        
        targetSum -= root.val
        if targetSum == 0 and root.left is None and root.right is None:
            return True
        
        return self.hasPathSum(root.left, targetSum) or self.hasPathSum(root.right, targetSum)
```
* 解答時間:
  - 1回目: 1:32
  - 2回目: 1:42
  - 3回目: 1:39
