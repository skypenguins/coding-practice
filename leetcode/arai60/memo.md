# 108. Convert Sorted Array to Binary Search Tree
* 問題: https://leetcode.com/problems/convert-sorted-array-to-binary-search-tree/
* 言語: Python

# Step1
* 昇順の数値を持つ配列から、高さのバランスが取れた（height-balancedな）二分探索木（BST）を構築
  - BSTとは以下の条件を満たす木
    - 左の部分木（subtree）のすべてのノードの値はルートの値より小さい
    - 右の部分木（subtree）のすべてのノードの値はルートの値より大きい
  - 高さバランスのとれたBSTとは、各ノードの左右の部分木の高さ（深さ）の差が1以下の木。これにより、検索、挿入、削除などの操作が効率的な $O(\log n)$ になる。
* 昇順に並んでいる性質を利用して配列の中央の値を起点にして木を構築する方針を立てたが、5分経っていたので正答をみる

## 正答
* 深さ優先探索（DFS）
* 配列（部分配列）の中央の値からルート（根）ノードを決定
* 上記ロジックを再帰的に左半分と右半分に適用
* 配列の長さをnとすると:
  - 左部分木：$⌊n/2⌋$ 個の要素
  - 右部分木：$n - ⌊n/2⌋ - 1$ 個の要素
```py
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        if not nums:
            return None
        
        mid = len(nums) // 2
        root = TreeNode(nums[mid])

        root.left = self.sortedArrayToBST(nums[:mid])
        root.right = self.sortedArrayToBST(nums[mid+1:])

        return root
```
* 時間計算量: $O(n \log n)$
* スタックを使った方法でも書けそう？

 # Step2
 ## 他人のコードを読む
 * 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.9s63li7jygve
   - > 気になって調べてみたら一次元のものはmiddle、二次元のものにはcenterを使う感覚があるみたいです。
     -  ref. https://github.com/colorbox/leetcode/pull/38#discussion_r1999900824
* https://github.com/colorbox/leetcode/pull/38
  - C++
  - DFS
  - `108/step2_3.cpp` は、再帰ではなくスタックを使い、ポインタへのポインタ（pointer to pointer）による解法
    - ポインタへのポインタとは、ポインタのアドレスを格納するポインタ
      - Pythonばかり書いているので、C++ではオブジェクトの動的な生成ではオブジェクトを確保した位置のアドレスを返すことを明確に意識しないといけないことを忘れていた
    - `new TreeNode()` はTreeNodeオブジェクトのアドレスを返す
    - スタックに親ノードのポインタ、左インデックス、右インデックスのタプルを積んでいく
* https://github.com/irohafternoon/LeetCode/pull/27
  - C++
  - DFS
* `TreeNode`の `left` と `right` 、配列インデックスの `left` と `right` で一瞬混乱してしまう
  - 後者を `left_i` 、 `right_i` とするのもあり？
* https://github.com/ichika0615/arai60/pull/17
  - Python
  - DFSと幅優先探索（BFS）
    - BFSの場合、キューを使って木のレベル（高さ）ごとにノードを追加していく
  - 自分で概算をして「手の運動」をする
    - ref. https://github.com/ichika0615/arai60/pull/17#discussion_r2019099632
    - ref. https://github.com/ichika0615/arai60/pull/17/files#r2020138924
      - 時間計算量: $O(n \log n)$

$$
\begin{align*}
T(n) &= 2T(n/2) + cn \\
&= 2[2T(n/4) + cn/2] + cn \\
&= 4T(n/4) + cn + cn \\
&= 4T(n/4) + 2cn \\
&= 4[2T(n/8) + cn/4] + 2cn \\
&= 8T(n/8) + cn + 2cn \\
&= 8T(n/8) + 3cn \\
&... \\
&= 2^k T(n/2^k) + k×cn \\
\end{align*}
$$

* 終了条件: $n/2^k = 1$ のとき（つまり $k = \log_2 n$ ）

$$
\begin{align*}
T(n) &= 2^{\log_2 n} × T(1) + cn × \log_2 n \\
&= n × T(1) + cn × \log_2 n  \\
&= O(n) + O(n \log n) \\
&= O(n \log n)
\end{align*}
$$

* 直観的には、木の高さごとの $n$ 回の走査×再帰の深さ $\log n$ で $n\log n$

# Step3
* 再帰DFS
* スライスではなくインデックスを使う方法
```py
class Solution:
    def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
        def helper(left, right):
            if left > right:
                return None
            
            mid = (left + right) // 2
            root = TreeNode(nums[mid])
            root.left = helper(left, mid - 1)
            root.right = helper(mid + 1, right)
            
            return root
        
        return helper(0, len(nums) - 1)
```
* 解答時間
  - 1回目: 1:53
  - 2回目: 1:50
  - 3回目: 1:45
