# 2. Add Two Numbers
* 問題リンク: https://leetcode.com/problems/add-two-numbers/
* 使用言語: Python3

## Step1
* ListNode（連結リストのノード）のデータ構造を忘れていた
* 数値に変換してから総和を計算して、総和の数値からListNodeに戻す方針を立てたが、最後までアルゴリズムを思いつかず
* 5分考えてもわからないので答えを見た

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(0)
        tail = dummy_head
        carry = 0

        while l1 or l2 or carry:
            digit_1 = l1.val if l1 else 0
            digit_2 = l2.val if l2 else 0

            summation = digit_1 + digit_2 + carry
            new_digit = summation % 10
            carry = summation // 10

            new_node = ListNode(new_digit)
            tail.next = new_node
            tail = tail.next

            l1 = l1.next if l1 else None
            l2 = l2.next if l2 else None
        
        summation_digits = dummy_head.next
        return summation_digits
```

* 実行時間: 1ms
* メモリ使用量: 17.95MB
* 解答時間: 11:13
* 時間計算量: O(n)

* 合計の一の位の算出方法は剰余（`%`演算子）で、合計の十の位は整数の商（`//`演算子）だが、どっちがどっちか混乱した
* l1とl2で要素数が異なるので、短い方をゼロ埋めしないといけない

## Step2
### 参考にした方々
* https://github.com/rinost081/LeetCode/pull/7/commits/689615b8b080dfe1347e58034f9aad395c2d1eca
    - `sentinel` という変数名は思いつかなかった
* https://github.com/fuga-98/arai60/blob/88212ec138b616e257984cd19c47c63b12bfa078/2-Add-Two-Numbers.md
    - 自分が当初考えていた手法
    - 再帰を使った方法
        - 再帰を使う場合、スタックの深さに気をつける
        - Pythonのスタックの最大数は `sys.getrecursionlimit()` で取得（cf. [公式Docs](https://docs.python.org/ja/3/library/sys.html#sys.getrecursionlimit)）
            - 1000くらい？
* 問題の制約はあまり重要視していなかった
    - ノード数が最大100個なのでそこまで大きくない認識
* Pythonの三項演算子を使っている人があまりいない？

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(0)
        tail = dummy_head
        carry = 0

        while l1 or l2 or carry:
            digit_1 = 0
            digit_2 = 0

            if l1:
                digit_1 = l1.val
            if l2:
                digit_2 = l2.val

            summation = digit_1 + digit_2 + carry
            new_digit = summation % 10
            carry = summation // 10

            new_node = ListNode(new_digit)
            tail.next = new_node
            tail = tail.next

            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
        
        summation_digits = dummy_head.next
        return summation_digits
```
* 実行時間: 3ms
* メモリ使用量: 17.82MB

* 三項演算子をやめて、最初に `digit_1`、`digit_2`を初期化するようにした
* よく考えたら、`l1`、`l2`を `None`にしなくてもよかった

## Step3

```python
class Solution:
    def addTwoNumbers(self, l1: Optional[ListNode], l2: Optional[ListNode]) -> Optional[ListNode]:
        dummy_head = ListNode(0)
        tail = dummy_head
        carry = 0

        while l1 or l2 or carry:
            digit_1 = 0
            digit_2 = 0

            if l1:
                digit_1 = l1.val
            if l2:
                digit_2 = l2.val
            
            summation = digit_1 + digit_2 + carry
            new_digit = summation % 10
            carry = summation // 10

            if l1:
                l1 = l1.next
            if l2:
                l2 = l2.next
            
            new_node = ListNode(new_digit)
            tail.next = new_node
            tail = tail.next

        summation_digits = dummy_head.next
        return summation_digits
```
* 1回目: 5:11
* 2回目: 3:41
* 3回目: 3:11
