# 83. Remove Duplicates from Sorted List
* 問題: https://leetcode.com/problems/remove-duplicates-from-sorted-list/
* 言語: Python

# Step1
## 方針
* キー: `ListNode.val`、値: `ListNode` を要素にもつ辞書を作成していき、 `ListNode` の値とオブジェクトの対応関係を保持する
* 重複する `ListNode` は必ず隣り合わせ
  - その `ListNode` は辞書に追加しない
  - 同じ値で既に存在する `ListNode` の `next` を次の `ListNode` のポインタに接続し直す

## 解答（AC）
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        if head is None:
            return head
        
        val_to_node = dict()
        node = head

        while node is not None:
            if node.val in val_to_node:
                val_to_node[node.val].next = node.next
                node = node.next
            else:
                val_to_node[node.val] = node
                node = node.next
        
        return list(val_to_node.values())[0]
```
* 解答時間: 11:46
* 時間計算量: $O(n)$
* 解いた後で気づいたが、最終行は単に `return head` で良かった

# Step2
# 他の人のコードを読む
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.voz9njv1gtqy
* https://github.com/mptommy/coding-practice/pull/3
   - C++
   - およそ2パターンのアルゴリズム
     1. 現在ノードと最後に見たノードの2種類のノードを変数として保持し、最後に見たノードの値と現在ノードの値が異なるときに、最後に見たノードの次を現在ノードに接続し、最後に見たノードを現在ノードで更新
     2. 現在ノードを変数として保持し、現在ノードの値と次ノードの値が同じなら、次ノードへの訪問を飛ばして次の次ノードを訪問
        - 次の次ノードも同じ値なら、次の次の次ノード…と異なる値が出るまで飛ばし続ける
        - 二重 `while` ループ、 `while` と `if-continue` 、 再帰による実装
         - 以下は二重ループによる実装
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head
        
        while node and node.next:
            while node.next and node.val == node.next.val:
                node.next = node.next.next
            node = node.next
        
        return head
```
  - * 自分の方法はソートされていて同じ値が連続していることを上手く利用していない（逆に言えば、ソートされていなくても動く）

* https://github.com/Kaichi-Irie/leetcode-python/pull/1
  - Python
  - Setを使って訪問済みノードを記録していく方法

## 読みやすく書き直したコード
`if-continue` の方法（`else` を使っても書ける）
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head

        while node:
            if node.next and node.val == node.next.val:
                node.next = node.next.next
                continue
            node = node.next
        
        return head
```

# Step3
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        node = head

        while node:
            if node.next and node.val == node.next.val:
                node.next = node.next.next
                continue
            node = node.next
        
        return head
```
* 解答時間
  - 1回目: 1:32
  - 2回目: 1:03
  - 3回目: 1:07
