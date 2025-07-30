# 111. Minimum Depth of Binary Tree
* 問題: https://leetcode.com/problems/minimum-depth-of-binary-tree/
* 言語: Python

## Step1
* 木構造の最小の高さを求める
* 深さ優先探索（DFS）だとすべてのノードを訪れる必要があるので幅優先探索（BFS）でノードを訪れていき、葉ノードを見つけたらその時点の深さを返し早期終了する方針

### すべてのテストケースを通過しないコード
```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        visited_node = deque([root])
        min_tree_depth = 1

        while visited_node:
            node = visited_node.popleft()
            if node.left and node.right:
                min_tree_depth += 1
            if not node.left and not node.right:
                return min_tree_depth
            if node.left:
                visited_node.append(node.left)
            if node.right:
                visited_node.append(node.right)
```

* 最初の15分は入力を配列と勘違いしていた
* 子ノードを2つ持つノードが左側に存在すると余計に1つカウントする
* 1時間ほど経過していたので正答を見る

### 正答
```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        node_and_depth = deque([(root, 1)])

        while node_and_depth:
            node, depth = node_and_depth.popleft()
            if not node.left and not node.right:
                return depth
            if node.left:
                node_and_depth.append((node.left, depth + 1))
            if node.right:
                node_and_depth.append((node.right, depth + 1))
```
* 考え方はだいたい同じだが、木の深さをレベルごとに保持するためにキューにノードとそのレベルでの深さのタプルを格納

## Step2
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.prowafrzksyh
  * https://github.com/sakupan102/arai60-practice/pull/23
    - Python
    - 同じBFSだが、2つのlistを使った方法
  * https://github.com/Yoshiki-Iwasa/Arai60/pull/25
    - Rust
    - キューによるBFS、再帰によるDFS
    - 以下のように、左右の子ノードの処理をまとめているのが印象的
    ```rust
        for child in [node_ref.left.as_ref(), node_ref.right.as_ref()] {
        if let Some(child_node) = child {
            queue.push_back((Rc::clone(child_node), depth + 1));
        }
    }
    ```
  * https://github.com/olsen-blue/Arai60/pull/22
    - Python
    - > あー、この問題が、たとえば4分木だったら、全部の組み合わせ16通りを全部書き出しますか、という気持ちですね。

### 別解（読みやすく書き直す）
#### 再帰DFSによる方法
```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
            
        if not root.left and not root.right:
            return 1
        
        min_depth = float("inf")
        if root.left:
            min_depth = min(min_depth, self.minDepth(root.left) + 1)
        if root.right:
            min_depth = min(min_depth, self.minDepth(root.right) + 1)
        
        return min_depth
```
* 時間計算量: $O(n)$

#### スタックDFSによる方法
```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        node_and_depth = [(root, 1)]
        min_depth = float("inf")
        while node_and_depth:
            node, depth = node_and_depth.pop()
            if not node.left and not node.right:
                min_depth = min(min_depth, depth)
            if node.left:
                node_and_depth.append((node.left, depth + 1))
            if node.right:
                node_and_depth.append((node.right, depth + 1))
        
        return min_depth
```
* 時間計算量: $O(n)$

# Step3
* BFSによる方法
```py
class Solution:
    def minDepth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0
        
        node_and_depth = deque([(root, 1)])
        while node_and_depth:
            node, depth = node_and_depth.popleft()
            if not node.left and not node.right:
                return depth
            if node.left:
                node_and_depth.append((node.left, depth + 1))
            if node.right:
                node_and_depth.append((node.right, depth + 1))
```
* 解答時間:
  - 1回目: 1:50
  - 2回目: 1:57
  - 3回目: 1:55
