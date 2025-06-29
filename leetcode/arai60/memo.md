# 141. Linked List Cycle
* 問題リンク: https://leetcode.com/problems/linked-list-cycle/
* 言語: Python3

# Step1
## 方針
* 循環しているnodeのポインタ `pos` は引数として与えられない
* 循環を「既に訪れたnodeの値があるかどうか」で検出する
* 後述の問題は出ると思ったがとりあえず実装してみた

### すべてのテストケースを通過しないコード
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        if not hasattr(head, "next"):
            return False
        
        current_node = head
        visited_node_val = []

        while True:
            if current_node.val in visited_node_val:
                return True
            if current_node.next is not None:
                visited_node_val.append(current_node.val)
                current_node = current_node.next
            else:
                return False
```

* 案の定、循環していなくても同じ値があれば循環と誤検出する
* 答えを考えて5分過ぎていたので正答を見る

### 正答
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:        
        current_node = head
        visited_node = set()

        while current_node:
            if current_node in visited_node:
                return True
            visited_node.add(current_node)
            current_node = current_node.next
        
        return False
```

* そもそもnodeの値（`current_node.val`）ではなく、nodeオブジェクトの参照の値（メモリ上のアドレス）を追加すれば（一意となるので）良い
* `while True` としない場合、以下は無くても動く
```python
        if not hasattr(head, "next"):
            return False
```
* 時間計算量: $O(n)$
* 空間計算量: $O(n)$

# Step2
## 別解を読む
* https://github.com/ryosuketc/leetcode_arai60/pull/1
  - 一回の走査で2つ先を読む「速いポインタ」と1つ先を読む「遅いポインタ」の2種類のポインタを用意して、その2つのポインタが指す位置が同じになるかどうかで循環検出を行う方法
    - フロイドの循環検出法と呼ぶらしい
    - アルゴリズム自体は単純だが、すぐに思いつかないアルゴリズムだと思った
    - このアルゴリズムは「常識」の範囲内なのだろうか？
  - 変数の初期化順序
    - https://docs.python.org/3/reference/simple_stmts.html#assignment-statements
  - `!=` と `is not` の違い（値の比較かid checkか）
    - https://docs.python.org/3/library/operator.html#mapping-operators-to-functions
    - https://peps.python.org/pep-0008/#programming-recommendations

* https://github.com/mptommy/coding-practice/pull/1
  - node数の上限が決まっているので、ループ回数がその上限を超えたら循環とみなす方法
    - 問題として答えは一応出るが、正規表現のReDoSの検出っぽい方法だと思った

## フロイドの循環検出法で解く
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        fast_ptr = head
        slow_ptr = head

        while fast_ptr and fast_ptr.next:
            fast_ptr = fast_ptr.next.next
            slow_ptr = slow_ptr.next

            if fast_ptr == slow_ptr:
                return True
        
        return False
```

# Step3
```python
class Solution:
    def hasCycle(self, head: Optional[ListNode]) -> bool:
        fast_ptr = head
        slow_ptr = head

        while fast_ptr and fast_ptr.next:
            fast_ptr = fast_ptr.next.next
            slow_ptr = slow_ptr.next

            if fast_ptr == slow_ptr:
                return True
        
        return False
```
* 解答時間
  - 1回目: 1:10
  - 2回目: 1:10
  - 3回目: 1:06
