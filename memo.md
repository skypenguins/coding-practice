# 82. Remove Duplicates from Sorted List II
- 問題: https://leetcode.com/problems/remove-duplicates-from-sorted-list-ii/
- 言語: Python

## Step1
- 一瞬Setを作成せよということかと思ったが重複する要素（節）を全て削除する
- 入力の最大長が300であるため、線形探索で十分と判断
- 節の値をキー、節オブジェクトに値（配列）としてハッシュマップ（辞書）で持つ
- ハッシュマップからリストを再構成し、ハッシュマップの値の配列の長さが2以上である場合を節の重複とみなし、読み飛ばすようにする
- 重複検出はできたが、削除されずSetを作成している状態で20分経過したので正答を見る

### WAとなるコード
```py
# Definition for singly-linked list.
# class ListNode:
#     def __init__(self, val=0, next=None):
#         self.val = val
#         self.next = next
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        value_to_node = defaultdict(list)
        node = head

        while node:
            value_to_node[node.val].append(node)
            node = node.next
        
        v_to_n_lst = list(value_to_node.values())
        i = 0
        while i < len(v_to_n_lst) - 1:
            if len(v_to_n_lst[i]) > 1:
                i += len(v_to_n_lst[i])
                continue
            v_to_n_lst[i][0].next = v_to_n_lst[i+1][0]
            i += 1
        
        return v_to_n_lst[0][0]
```

### 正答
#### 元の方針を修正した解法
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        value_to_node = defaultdict(list)
        node = head

        while node:
            value_to_node[node.val].append(node)
            node = node.next
        
        # 1個だけのグループ（重複なし）だけ残す
        unique_groups = [nodes for nodes in value_to_node.values() if len(nodes) == 1]

        # 全て重複していた場合
        if not unique_groups:
            return None

        # ユニークな節を順番に繋ぎ直す
        for i in range(len(unique_groups) - 1):
            unique_groups[i][0].next = unique_groups[i + 1][0]
        
        # 末尾節の古いnextを切る
        unique_groups[-1][0].next = None

        return unique_groups[0][0]
```
- 空間計算量: $O(n)$ 
- `if len(...) > 1` → `if len(...) == 1` でフィルタ
- `i += len(v_to_n_lst[i])` → そもそもフィルタ済みハッシュマップのグループ単位でアクセスすれば良い
- 古い `next` を切る

#### 2つのポインタを使う方法
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        dummy = ListNode(0, head)
        prev = dummy
        curr = head

        while curr:
            # curr が重複しているか確認
            if curr.next and curr.val == curr.next.val:
                val = curr.val

                # 同じ値の節を全部スキップ
                while curr and curr.val == val:
                    curr = curr.next

                prev.next = curr
            else:
                prev = curr
                curr = curr.next

        return dummy.next
```
- こちらは空間計算量: $O(1)$ で済む
- だいぶ前に（別の問題だが）似た解法を使ったのを思い出した

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.xzxd7jwvkwc5

- https://github.com/goto-untrapped/Arai60/pull/43
  - C++
  - 確かに、命名として `node` 、 `nextNode` だとちょっと分かりづらいと思った

- https://discord.com/channels/1084280443945353267/1195700948786491403/1197102971977211966
  - Python
  - 列車の例え

- https://github.com/nittoco/leetcode/pull/9/changes
  - Python
  - 二重whileループでない場合は、状態管理の変数が必要になる

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def deleteDuplicates(self, head: Optional[ListNode]) -> Optional[ListNode]:
        sentinel = ListNode(0, head)
        previous = sentinel
        current = head

        while current:
            if current.next and current.val == current.next.val:
                visited_value = current.val

                while current and current.val == visited_value:
                    current = current.next

                previous.next = current
                continue

            previous = current
            current = current.next

        return sentinel.next
```
- 所要時間:
  - 1回目: 2:31
  - 2回目: 2:16
  - 3回目: 3:41
