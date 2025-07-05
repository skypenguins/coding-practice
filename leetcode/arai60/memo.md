# 142. Linked List Cycle II
問題リンク: https://leetcode.com/problems/linked-list-cycle-ii/
言語: Python

# Step1
* [141. Linked List Cycle](https://leetcode.com/problems/linked-list-cycle/) とほぼ同じ
* 141. Linked List Cycleでは、循環が存在するかどうか（bool値）を返していたが、今回は循環が開始するnodeを返す

## 自分の解答
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited_node = set()
        node = head

        while node:
            if node in visited_node:
                return node
            visited_node.add(node)
            node = node.next
```
* 解答時間: 6:14

# Step2
## 他人のコードを読む
* https://github.com/mptommy/coding-practice/pull/2
  - C++
  - 自分の方法と同様に訪れたnodeをSetに追加していき、訪れたnodeがSetに存在したらそのnodeを返す方法
  - フロイドの循環検出法
    - `fast` と `slow` が一致した時に `slow` を `head` の位置まで戻すと `fast` と `slow` の差分が循環の長さとなる
    - 上記の位置関係を維持したまま、両者を1づつ移動させて再び一致した位置が循環の開始位置
### フロイドの循環検出法
```python
lass Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = head
        slow = head

        while (fast and fast.next):
            fast = fast.next.next
            slow = slow.next

            if (fast is slow):
                break
        
        if (fast is None or fast.next is None):
            return None
        
        slow = head
        while (fast is not slow):
            fast = fast.next
            slow = slow.next
        
        return fast
```

### 読みやすく整え直したコード
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited_nodes = set()
        current_node = head

        while current_node:
            if current_node in visited_nodes:
                return current_node
            visited_nodes.add(current_node)
            current_node = current_node.next
        
        return None
```
* 変数名を `node` から `current_node` に変更して、より分かりやすく
* 明示的に `return None` とするようにした

# Step3
```python
class Solution:
    def detectCycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited_nodes = set()
        current_node = head

        while current_node:
            if current_node in visited_nodes:
                return current_node
            visited_nodes.add(current_node)
            current_node = current_node.next
        
        return None
```
* 解答時間
  - 1回目: 1:40
  - 2回目: 1:39
  - 3回目: 1:20
