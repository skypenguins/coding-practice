# 206. Reverse Linked List
* 問題: https://leetcode.com/problems/reverse-linked-list/
* 言語: Python

## Step1
* 単純に `node.next = node` とすると循環参照となり、TLE
* 1つの一時変数に `node.next` を退避しても循環参照となる
* 20分ほどWAで手が止まって5分経ったので正答を見る

### 正答 
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        prev = None
        node = head

        while node:
            node_next = node.next
            node.next = prev
            prev = node
            node = node_next
        
        return prev
```
* 時間計算量: $O(n)$
* 空間計算量: $O(1)$
* ひとつ前、現在、ひとつ後のノードを保持する変数を使う
* ひとつ後のノードを一時変数 `next_temp` に退避してから、現在のノードのひとつ後のノードへのポインタをひとつ前のノードで更新
* ひとつ前のノードを現在のノードで更新
* 現在のノードを次のノードで更新
* スタックと再帰を使った方法もありそう

## Step2
### 他の人のコードを読む
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.x5w37bodndgj
    * https://github.com/TORUS0818/leetcode/pull/9
    - Python
    - 繋ぎ替え、スタックとループ、再帰
      - スタックとループの方法では、新たにノードを生成しているので空間計算量が増える
      - 一番わかりやすい気がする
        - ```py
            class Solution:
                def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
                    if not head:
                        return head
                    
                    node = head
                    visited_nodes = []
                    while node:
                        visited_nodes.append(node.val)
                        node = node.next
                    
                    reversed_head = ListNode(visited_nodes.pop(), None)
                    reversed_node = reversed_head
                    while visited_nodes:
                        reversed_node.next = ListNode(visited_nodes.pop(), None)
                        reversed_node = reversed_node.next
                    
                    return reversed_head
            ```
        - 再帰
          - 現在のノードの次のノードから始まる部分リストを再帰的に反転
          - 次のノードのnext `node.next.next` を現在のノードに付け替え
          - 現在のノードのnext `node.next` を `None` にする
          - ```py
            class Solution:
                def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
                    if not head or not head.next:
                        return head
                    
                    new_head = self.reverseList(head.next)
                    head.next.next = head
                    head.next = None
                    return new_head
            ```
        - 再帰
          - 逆順になった先頭と末尾を戻すヘルパー関数を使う方法
          - ```py
            class Solution:
                def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
                    def reverse_list_helper(node):
                        if not node:
                            return None, None
                        if not node.next:
                            return node, node
                        node_next = node.next
                        node.next = None
                        reversed_head, reversed_tail = reverse_list_helper(node_next)
                        reversed_tail.next = node
                        return reversed_head, node

                    reversed_head, _ = reverse_list_helper(head)
                    return reversed_head
            ```
          - cf. https://discord.com/channels/1084280443945353267/1231966485610758196/1239417493211320382
    * https://github.com/goto-untrapped/Arai60/pull/27/
      - Java
      - 再帰
        - 先頭から自分まで逆順にしたものを次のノードに渡して、全部が逆順になったものを返してもらう
        - 前から順番に付け替えていく
        - ```java
            class Solution {
                ListNode reverseListHelper(ListNode reversed, ListNode rest) {
                    if (rest == null) {
                        return reversed;
                    }
                    ListNode rest_tail = rest.next;
                    rest.next = reversed;
                    return reverseListHelper(rest, rest_tail);
                }

                public ListNode reverseList(ListNode head) {
                    return reverseListHelper(null, head);
                }
            }
          ```

### 読みやすく書き直したコード
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_head = None
        rest = head

        while rest:
            rest_tail = rest.next
            rest.next = reversed_head
            reversed_head = rest
            rest = rest_tail
        
        return reversed_head
```

## Step3
```py
class Solution:
    def reverseList(self, head: Optional[ListNode]) -> Optional[ListNode]:
        reversed_head = None
        rest = head

        while rest:
            rest_tail = rest.next
            rest.next = reversed_head
            reversed_head = rest
            rest = rest_tail
        
        return reversed_head
```
* 解答時間
  - 1回目: 1:16
  - 2回目: 1:15
  - 3回目: 1:12
