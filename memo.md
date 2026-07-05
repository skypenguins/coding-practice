# 105. Construct Binary Tree from Preorder and Inorder Traversal
- 問題: https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/
- 言語: Python

## Step1
### 考えていた方針 
- 「二分木が与えられた時に、DFSで左からpre-order, in-orderで節の値を出力する」の逆の操作を行う
- pre-orderのある値Aの同じインデックスのin-orderの値Bとした場合、Aの次の節の値はBである
  - 例: `preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]` なら、 `3` の次の節の値は `9`
- in-orderのある値Cと一つ前のインデックスのpre-orderの値が同じ場合、その節は葉である
  - 例: `preorder = [3,9,20,15,7], inorder = [9,3,15,20,7]` なら、 `inorder[1]` = `preorder[0]`
- 最後の葉のみ、値は同じになる
- 二分木の条件があるため、ある節に「左節、右節が存在」もしくは「存在しない」のどちらかである
- 入力値の最大長 $3000$ , 計算量 $O(n)$ で、Pythonの場合: $10^{7}$ ステップ/秒 のため、$3000 / 10^{7} ≒ 0.3ms$ 
- ここまで考えていたら15分経過したので一旦正答を見る

## Step2
### 正しい方針
```
preorder = [root, left subtree..., right subtree...]
inorder  = [left subtree..., root, right subtree...]
```
- preorder は `root < left < right` の順序
- inorder は `left < root < right` の順序

1. preorder の先頭が現在の部分木の root
2. その root を inorder の中で探す
3. root より左側が左部分木
4. root より右側が右部分木
5. 左部分木の大きさが分かるので、preorder も左右に分割できる

#### 感想
- 部分木 subtree の意識が自分の中になかった
- DFS traversal の性質の理解が甘かった

### 正答
#### 再帰DFS版
```py
class Solution:
    def buildTree(self, preorder: List[int], inorder: List[int]) -> Optional[TreeNode]:
        in_pos = {val: i for i, val in enumerate(inorder)}

        def build_nodes(pre_l, pre_r, in_l, in_r):
            if pre_l > pre_r:
                return None

            root_val = preorder[pre_l]
            root = TreeNode(root_val)

            mid = in_pos[root_val]
            left_size = mid - in_l

            root.left = build_nodes(
                pre_l + 1,
                pre_l + left_size,
                in_l,
                mid - 1
            )

            root.right = build_nodes(
                pre_l + left_size + 1,
                pre_r,
                mid + 1,
                in_r
            )

            return root

        return build_nodes(0, len(preorder) - 1, 0, len(inorder) - 1)
```

#### iterative DFS版
```py
class Solution:
    def buildTree(self, preorder, inorder):
        if not preorder:
            return None

        root = TreeNode(preorder[0])
        stack = [root]
        in_i = 0 # 左方向にたどって必ず最初に訪問される節

        for val in preorder[1:]:
            node = stack[-1]

            # 全体の左部分木を作っている途中
            if node.val != inorder[in_i]:
                node.left = TreeNode(val)
                stack.append(node.left)
                continue
            
            # 一致した場合は、そのノードの左部分木とノード自身の処理が完了
            # 連続して一致する限りstackからpop
            while stack and stack[-1].val == inorder[in_i]:
                node = stack.pop()
                in_i += 1
            # 右部分木を作る
            node.right = TreeNode(val)
            stack.append(node.right)

        return root
```

## Step3
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.1rv0z8fm6lc3

- https://github.com/goto-untrapped/Arai60/pull/53
  - Java
  - 思いつかない時は小さい例から考えてみる、遅くても良いから動くものを作ってみる

- https://github.com/kazukiii/leetcode/pull/30
  - C++
  - 再帰DFS、iterativeな方法2種類（コメント）
  - iterativeな方法では、preorderを基準に回していくパターンが多い
  - `current_index`: preorderのindex、 `inorder_position`: inorderでのインデックス、 `right_limit`: その節の subtree が inorder 上で越えてはいけない右端としたとき、 
    - `node_position` < `current_index`
        - → current の左子
    - `current_index` < `node_position` < `right_limit`
        - → current の右子
    - `node_position` >= `right_limit`
        - → current の subtree には入らないので pop して祖先へ戻る
  - https://github.com/kazukiii/leetcode/pull/30/changes#r1821628634
    - inorderを基準にして構築する方法。あまり理解できず
    - cf. https://github.com/fuga-98/arai60/pull/29#discussion_r2020242408
    - cf. https://github.com/tarinaihitori/leetcode/pull/29#discussion_r2044913447

- https://github.com/nittoco/leetcode/pull/37
  - preorderでの構築
    - cf. https://github.com/nittoco/leetcode/pull/37#discussion_r1821720967
    - cf. https://github.com/nittoco/leetcode/pull/37#discussion_r1831463376

- https://discord.com/channels/1084280443945353267/1478763507963924522/1492871022511390843
  - inorder を並び順、preorder の index を優先度として Cartesian tree を作っている
    - Cartesian tree: 「配列上の順番」と「優先度」の2つの条件を同時に満たす二分木

- コメントを読んでいても、iterativeな方法はMediumにしてはけっこう難しいように思える。再帰アプローチなら理解してしまえばMediumレベルな気がする

## Step3
### 再帰DFS
```py
class Solution:
    def buildTree(self, preorder, inorder):
        in_pos = {value: i for i, value in enumerate(inorder)}

        def build_nodes(pre_l, pre_r, in_l, in_r):
            if pre_l > pre_r:
                return None
            
            root_val = preorder[pre_l]
            root = TreeNode(root_val)

            mid = in_pos[root_val]
            left_size = mid - in_l

            root.left = build_nodes(pre_l + 1, pre_l + left_size, in_l, mid - 1)
            root.right = build_nodes(pre_l + left_size + 1, pre_r, mid + 1, in_r)

            return root
        
        return build_nodes(0, len(preorder) - 1, 0, len(inorder) - 1)
```
- 所要時間:
  - 1回目: 4:50
  - 2回目: 5:34
  - 3回目: 4:32
