# 776. Split BST
- 問題: https://leetcode.com/problems/split-bst
  - 代替: https://algo.monster/liteproblems/776
- 言語: Python

## Step1
### 方針
- 元の木をDFSで探索していき、ある頂点の値が target 以下なら（BSTだから）その左部分木のすべての値も target 以下である
- 木の再構築をどうするか考えていたら15分経過したので正答を見る

### WA
```py
def split_bst(bst: Node, target: int) -> list[Node | None]:
    dummy = Node(float("inf"), bst)
    stack = [dummy]
    
    while stack:
        node = stack.pop()
        
        if node.left and node.left.val < target:
            right_tree_root = node
            left_tree_root = node.left
            return [left_tree_root, right_tree_root]

        if node.left:
            stack.append(node.left)
        if node.right:
            stack.append(node.right)
            
    return []
```
- 分割条件が間違っている
- スタック(DFS)の巡回順序にBSTの性質が全く活きていない
- 見つかった時点ですぐreturnしてしまい、木の再構築をしていない
- 左の子が存在しないパスでは条件が一度も成立しない

### 正しい方針
- target の位置まで一直線に降りて、そこから1段ずつ戻りながらつなぎ直す
- BSTの「左部分木の全頂点 < 自分 < 右部分木の全頂点」という不変条件があるからこそ、経路を外れた部分木を一切訪問せずに、それぞれの頂点自身の値と target を比較する一本道の訪問で済む

#### 感想
- 紙に部分木、呼出と再帰するときのスタックフレームをすべて書き出してようやく理解した

### 再帰DFS
```py
def split_bst(root, target):
    if root is None:
        return [None, None]

    if root.val <= target:
        left, right = split_bst(root.right, target)  # 右を訪問
        root.right = left  # <=target の部分を自分の右に付け替え
        return [root, right]
    else:
        left, right = split_bst(root.left, target)  # 左を訪問
        root.left = right  # >target の部分を自分の左に付け替え
        return [left, root]
```
- 時間・空間計算量ともに $O(H)$
  - H は木の高さ

### iterative DFS
```py
def split_bst(root, target):
    if root is None:
        return [None, None]

    smaller_head = smaller = Node(0)  # <= target 側を繋いでいくダミー
    larger_head = larger = Node(0)  # >  target 側を繋いでいくダミー

    node = root
    while node:
        if node.val <= target:
            smaller.right = node
            smaller = smaller.right
            node = node.right
        else:
            larger.left = node
            larger = larger.left
            node = node.left

    smaller.right = None  # 末端の余分なリンクを切る
    larger.left = None

    return [smaller_head.right, larger_head.left]
```
- 時間計算量: $O(H)$
- 空間計算量: $O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.3czeid3ovy2a

- https://github.com/goto-untrapped/Arai60/pull/54
  - Java
  - 再帰DFS
  - `left`, `right` より `smaller`, `larger` の方が分かりやすい、確かに
  - まずは手作業で単純なケースからやってみる: https://github.com/goto-untrapped/Arai60/pull/54#discussion_r1778944205

- https://github.com/Ryotaro25/leetcode_first60/pull/50
  - C++
  - ヘルパー関数による再帰DFS
  - 再帰とループの中間を念頭において、対応関係から相互に変換できるようにしておくとよい: https://github.com/Ryotaro25/leetcode_first60/pull/50#discussion_r1912058276

## Step3
### 再帰DFS
```py
def split_bst(root, target):
    if root is None:
        return [None, None]

    if root.val <= target:
        smaller, larger = split_bst(root.left, target)  # 右を訪問
        root.right = smaller  # <=target の部分を自分の右に付け替え
        return [root, larger]
    else:
        smaller, larger = split_bst(root.right, target)  # 左を訪問
        root.left = larger  # >target の部分を自分の左に付け替え
        return [smaller, root]
```
- 所要時間: 
  - 1回目: 6:59
  - 2回目: 3:49
  - 3回目: 3:14
