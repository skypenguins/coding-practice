# 617. Merge Two Binary Trees
* 問題: https://leetcode.com/problems/merge-two-binary-trees/
* 言語: Python

## Step1
* 2つの二分木をBFSで探索しながら、mergeした二分木を構築する方針
  - 片方にしか葉が存在しない場合、2つの葉の位置が揃わないが処理が思い浮かばず次の方針に切り替える
* 2つの二分木のデータ構造をそれぞれ配列に変換し値を保持して先頭から配列同士を足していく方針
  - 葉が存在しない場合は、配列にNoneを追加する
  - 配列を再び二分木に再構築する
  - しかし、誤った二分木を返す
* 1時間23分経っていたので正答を見る

<details>

<summary>動かないコード</summary>

```py
# Definition for a binary tree node.
# class TreeNode:
#     def __init__(self, val=0, left=None, right=None):
#         self.val = val
#         self.left = left
#         self.right = right
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        if not root1:
            return root2
        if not root2:
            return root1
        
        root1_nodes_list = []
        root1_nodes = deque([root1])
        while root1_nodes:
            node1 = root1_nodes.popleft()
            root1_nodes_list.append(node1)
            if node1.left:
                root1_nodes.append(node1.left)
            else:
                root1_nodes_list.append(None)
            if node1.right:
                root1_nodes.append(node1.right)
            else:
                root1_nodes_list.append(None)

        root2_nodes_list = []
        root2_nodes = deque([root2])
        while root2_nodes:
            node2 = root2_nodes.popleft()
            root2_nodes_list.append(node2)
            if node2.left:
                root2_nodes.append(node2.left)
            else:
                root2_nodes_list.append(None)
            if node2.right:
                root2_nodes.append(node2.right)
            else:
                root2_nodes_list.append(None)
        
        merged_node_vals = []
        min_length_nodes = []
        the_other_nodes = []
        if len(root1_nodes_list) <= len(root2_nodes_list):
            min_length_nodes = root1_nodes_list
            the_other_nodes = root2_nodes_list
        else:
            min_length_nodes = root2_nodes_list
            the_other_nodes = root1_nodes_list
        
        for i, node in enumerate(min_length_nodes):
            if node and the_other_nodes[i]:
                merged_node_vals.append(node.val + the_other_nodes[i].val)
                continue
            if node:
                merged_node_vals.append(node.val)
                continue
            if the_other_nodes[i]:
                merged_node_vals.append(the_other_nodes[i].val)
        
        for node in the_other_nodes[:len(min_length_nodes)]:
            if node:
                merged_node_vals.append(node.val)
            else:
                merged_node_vals.append(None)
        
        merged_node_vals = deque(merged_node_vals)
        merged_root = TreeNode()
        merged_root.val = merged_node_vals.popleft()
        merged_root.left = TreeNode(merged_node_vals.popleft())
        merged_root.right = TreeNode(merged_node_vals.popleft())
        while merged_node_vals:
            node_val = merged_node_vals.popleft()
            node = TreeNode()
            node.val = node_val
            if merged_node_vals:
                node.left = TreeNode(merged_node_vals.popleft())
            if merged_node_vals:
                node.right = TreeNode(merged_node_vals.popleft())
        
        return merged_root
```

</details>

### 正答
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        
        merged = TreeNode(root1.val + root2.val)

        merged.left = self.mergeTrees(root1.left, root2.left)
        merged.right = self.mergeTrees(root1.right, root2.right)

        return merged
```
* 時間計算量：$O(n+m)$
* 空間計算量：$O(n+m)$
  - それぞれの木のノード数をn,mとする
* 再帰による深さ優先探索（DFS）の前順（pre-order）走査
* 葉ノードまで降りてから、ボトムアップで木を構築
* 2つの二分木で片方しか葉が存在しない場合、元の二分木の葉オブジェクトをそのまま使っているのに少し違和感がある

## Step2
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.cxy3cik6kyqx
* https://github.com/goto-untrapped/Arai60/pull/47
  - Java
  - 再帰DFS、スタックDFS
  - 入力を破壊するか？非破壊とするか？
    - 自分の方法は非破壊的アプローチ（新しくノードを作っている）
* https://github.com/hroc135/leetcode/pull/22
  - Go
  - ポインタへのポインタを使った再帰
    - https://discord.com/channels/1084280443945353267/1262688866326941718/1297934906189549599
    - https://discord.com/channels/1084280443945353267/1262688866326941718/1298575468353556501
* https://github.com/fhiyo/leetcode/pull/25
  - Java
  - >新しいのを作るか作らないのか、古い入力を壊すのか壊さないのか、共有するのかしないのか(変更しない前提ならばメモリー使用量が減る)、などのオプションがあって、自分がどれを「選択」したかを意識しましょう。
    - cf. https://github.com/fhiyo/leetcode/pull/25#discussion_r1656461334
* https://github.com/seal-azarashi/leetcode/pull/22
  - Java
  - https://github.com/seal-azarashi/leetcode/pull/22#discussion_r1778932434
    - 葉ノードを返すときに木の一部を共有せずdeep copyをするか否か？ライブラリとして他人から使われるのであれば、やはりdeep copyした方が良いと思う
      - Pythonのdeepcopyについて
      - cf. https://docs.python.org/ja/3.13/library/copy.html
* https://github.com/Yoshiki-Iwasa/Arai60/pull/66
  - Rust
  - Rust特有の概念を理解していないので、現時点ではあまり理解できず
* https://github.com/irohafternoon/LeetCode/pull/26/
  - C++
  - 番兵（sentinel）を使ったDFS、BFS
  - > C++ の場合は、ダブルポインターで merged_node->left へのリファレンスを取ることによって、次のループの中で判断して代入することができますね。
    > 
    > そうすると、上の方の条件分岐をマージできます。
      - `merged_node->left` 、 `merged_node->right` へのポインタへのポインタで参照を取ると、`node1->left` 、 `node2->left` 、 `node1->right` 、 `node2->right` の存在確認（両方がnullでないかの確認）が不要になる。次のループで両方のノードが存在しないことを確認したら、nullptrを代入する。
      - Pythonで再現しようとすると、メンバ変数へのポインタを持てないためフラグが必要になる
        - 「BFS＋番兵＋参照」を参照
* https://github.com/tarinaihitori/leetcode/pull/23
  - Python
  - BFS
  - 右優先DFS
    - cf. https://github.com/tarinaihitori/leetcode/pull/23/files#r1919824481
### 読みやすく書き直したコード
#### 再帰DFS（非破壊的アプローチ+deep copy）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return self.deepCopy(root2)
        if not root2:
            return self.deepCopy(root1)
        
        merged = TreeNode(root1.val + root2.val)
        
        merged.left = self.mergeTrees(root1.left, root2.left)
        merged.right = self.mergeTrees(root1.right, root2.right)
        
        return merged
    
    def deepCopy(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        """木をディープコピーするヘルパー関数"""
        if not root:
            return None
        
        new_node = TreeNode(root.val)
        
        new_node.left = self.deepCopy(root.left)
        new_node.right = self.deepCopy(root.right)
        
        return new_node
```
#### 再帰DFS（破壊的アプローチ）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        
        root1.val += root2.val
        
        root1.left = self.mergeTrees(root1.left, root2.left)
        root1.right = self.mergeTrees(root1.right, root2.right)
        
        return root1
```
#### スタックDFS（破壊的アプローチ）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        
        stack = [(root1, root2)]
        while stack:
            node1, node2 = stack.pop()
            
            node1.val += node2.val
            
            if node2.left:
                if node1.left:
                    stack.append((node1.left, node2.left))
                else:
                    node1.left = node2.left
            
            if node2.right:
                if node1.right:
                    stack.append((node1.right, node2.right))
                else:
                    node1.right = node2.right
        
        return root1
```
#### スタックDFS（非破壊的アプローチ）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1:
            return root2
        if not root2:
            return root1
        
        new_root = TreeNode()
        stack = [(root1, root2, new_root)]

        while stack:
            node1, node2, merged = stack.pop()
            if node1 and node2:
                merged.val = node1.val + node2.val
            elif node1:
                merged.val = node1.val
            elif node2:
                merged.val = node2.val
            
            if (node1 and node1.left) or (node2 and node2.left):
                merged.left = TreeNode()
                stack.append((
                    node1.left if node1 else None,
                    node2.left if node2 else None,
                    merged.left
                ))
            
            if (node1 and node1.right) or (node2 and node2.right):
                merged.right = TreeNode()
                stack.append((
                    node1.right if node1 else None,
                    node2.right if node2 else None,
                    merged.right
                ))
        
        return new_root
```
* cf. https://github.com/tarinaihitori/leetcode/pull/23/files#r1919824481
  - 右優先DFS
#### スタックDFS＋番兵
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        sentinel = TreeNode(0)
        new_root = TreeNode()
        stack = [(root1, root2, new_root)]
        while stack:
            node1, node2, merged_node = stack.pop()

            if not node1:
                node1 = sentinel
            if not node2:
                node2 = sentinel
            
            merged_node.val = node1.val + node2.val 
                       
            if node1.left or node2.left:
                merged_node.left = TreeNode()
                stack.append((node1.left, node2.left, merged_node.left))
            if node1.right or node2.right:
                merged_node.right = TreeNode()
                stack.append((node1.right, node2.right, merged_node.right))

        return new_root
```
#### BFS（非破壊的アプローチ）
```py
class Solution:
    def mergeTrees(self, root1: TreeNode, root2: TreeNode) -> TreeNode:
        if not root1:
            return root2
        if not root2:
            return root1

        merged_root = TreeNode(root1.val + root2.val)

        queue = deque([(root1, root2, merged_root)])

        while queue:
            node1, node2, merged_node = queue.popleft()
            if node1.left and node2.left:
                merged_left = TreeNode(node1.left.val + node2.left.val)
                merged_node.left = merged_left
                queue.append((node1.left, node2.left, merged_left))
            elif node1.left:
                merged_node.left = node1.left
            elif node2.left:
                merged_node.left = node2.left

            if node1.right and node2.right:
                merged_right = TreeNode(node1.right.val + node2.right.val)
                merged_node.right = merged_right
                queue.append((node1.right, node2.right, merged_right))
            elif node1.right:
                merged_node.right = node1.right
            elif node2.right:
                merged_node.right = node2.right

        return merged_root
```
#### BFS＋番兵（非破壊的アプローチ）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        
        sentinel = TreeNode(0)
        
        new_root = TreeNode()
        nodes = deque([(root1, root2, new_root)])
        
        while nodes:
            node1, node2, merged_node = nodes.popleft()
            
            if not node1:
                node1 = sentinel
            if not node2:
                node2 = sentinel
            
            merged_node.val = node1.val + node2.val
            
            if node1.left or node2.left:
                merged_node.left = TreeNode()
                nodes.append((node1.left, node2.left, merged_node.left))
            
            if node1.right or node2.right:
                merged_node.right = TreeNode()
                nodes.append((node1.right, node2.right, merged_node.right))
        
        return new_root
```
#### BFS＋番兵＋参照（非破壊的変更）
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        
        # 番兵ノード（値0、子はNone）
        sentinel = TreeNode(0)
        
        # ダミールートを作成（最終的にdummy.leftが実際のルートになる）
        dummy = TreeNode(0)
        
        # (node1, node2, 親ノード, 左右フラグ)をキューに入れる
        # フラグ: 'L'=左の子, 'R'=右の子
        nodes = deque([(root1, root2, dummy, 'L')])
        
        while nodes:
            node1, node2, parent, side = nodes.popleft()
            
            if not node1 and not node2:
                if side == 'L':
                    parent.left = None
                else:
                    parent.right = None
                continue
            
            if not node1:
                node1 = sentinel
            if not node2:
                node2 = sentinel
            
            merged_node = TreeNode(node1.val + node2.val)
            
            # 親ノードの適切な位置に設定
            if side == 'L':
                parent.left = merged_node
            else:
                parent.right = merged_node
            
            # 子ノードをキューに追加
            nodes.append((node1.left, node2.left, merged_node, 'L'))
            nodes.append((node1.right, node2.right, merged_node, 'R'))
        
        return dummy.left
```
* `side` の設定には`setattr` を使うのが望ましい？
  - cf. https://github.com/tarinaihitori/leetcode/pull/23/files#r2105078697
* 以下のコードは、 `merged_node_ref[0]` の親の属性 `.left` と `.right` にアクセスする手段が無いので、両者を `None` にできず正しく動かない
<details>

<summary>動かないコード</summary>

```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None        
        sentinel = TreeNode(0)
        new_head = TreeNode()
        nodes = deque([(root1, root2, [new_head])])
        while nodes:
            node1, node2, merged_node_ref = nodes.popleft()
            if not node1 and not node2:
                merged_node_ref[0] = None
                continue
            if not node1:
                node1 = sentinel
            if not node2:
                node2 = sentinel
            # 参照のため、merged_node[0]でアクセス
            merged_node_ref[0].val = node1.val + node2.val
            merged_node_ref[0].left = TreeNode()
            nodes.append((node1.left, node2.left, [merged_node_ref[0].left]))
            merged_node_ref[0].right = TreeNode()
            nodes.append((node1.right, node2.right, [merged_node_ref[0].right]))
    
        return new_head
```

</details>

## Step3
### スタックDFS＋番兵
```py
class Solution:
    def mergeTrees(self, root1: Optional[TreeNode], root2: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root1 and not root2:
            return None
        
        sentinel = TreeNode(0)
        new_root = TreeNode()
        stack = [(root1, root2, new_root)]
        while stack:
            node1, node2, merged = stack.pop()
            if not node1:
                node1 = sentinel
            if not node2:
                node2 = sentinel
            
            merged.val = node1.val + node2.val
            if node1.left or node2.left:
                merged.left = TreeNode()
                stack.append((node1.left, node2.left, merged.left))
            if node1.right or node2.right:
                merged.right = TreeNode()
                stack.append((node1.right, node2.right, merged.right))
            
        return new_root
```
* 解答時間
  - 1回目: 3:33
  - 2回目: 4:18
  - 3回目: 4:38
