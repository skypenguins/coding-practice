# 703. Kth Largest Element in a Stream
* 問題リンク: https://leetcode.com/problems/kth-largest-element-in-a-stream/
* 言語: Python

## Step1
* 問題文通りに素直に実装

### 解答（AC）
```py
class KthLargest:
    k = 0
    nums = []
    def __init__(self, k: int, nums: List[int]):
        self.k = k - 1
        self.nums = nums

    def add(self, val: int) -> int:
        self.nums.append(val)
        self.nums = sorted(self.nums, reverse=True)
        return self.nums[self.k]
```
* 解答時間: 7:02
* 時間計算量:  $O(m × n \log n)$
  - `add()` 呼出: m回
  - `sorted()` はTimsort $O(n \log n)$
* `add()` を呼び出すたびに毎回ソートし、 `nums` のリストを生成しているため、 $10^4$ 回呼び出すと実行時間2000ms超となっていると想像
* 間違えてクラス変数にしてしまった

## Step2
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.t2w1qof72ib2
* https://github.com/fhiyo/leetcode/pull/10
  - C++
  - 優先度付きキューを使った方法
  - > k_が1以上であることはコンストラクタでチェックすべきだった気がする。
    - LeetCodeでは入力の数値範囲が保証されているので気にしなくていいが、実務ではどう呼び出されるかに応じてチェックすべきだと思った
    - C++の `priority_quere` について
      - cf. https://cpprefjp.github.io/reference/queue/priority_queue.html
    - Pythonのheqpq実装
      - cf. https://github.com/python/cpython/blob/31a28cbae0989f57ad01b428c007dade24d9593a/Lib/heapq.py#L260
* https://github.com/shining-ai/leetcode/pull/8
  - Python
  - heqpq、優先度付きキューを自前実装


### 読みやすく書き直したコード
#### 優先度付きキュー
* Pythonでは標準で `heapq` を使う
  - cf. https://docs.python.org/3/library/heapq.html
```py
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = []

        for n in nums:
            self.add(n)

    def add(self, val: int) -> int:
        heapq.heappush(self.nums, val)
        if len(self.nums) > self.k:
            heapq.heappop(self.nums)
        
        return self.nums[0]
```
#### 優先度付きキューを自前実装
- CPythonを参考
  -  `_sift_up` と `_sift_down` はそれぞれのメソッド名と中身の処理が直観的に一致していないように思えたので、入れ替えた
```py
class MinHeap:
    def __init__(self):
        self.heap = []

    def _parent(self, i):
        """親ノードの位置を返す"""
        return (i - 1) // 2
    
    def _left_child(self, i):
        """左の子ノードの位置を返す"""
        return 2 * i + 1
    
    def _right_child(self, i):
        """右の子ノードの位置を返す"""
        return 2 * i + 2

    def _sift_up(self, pos):
        """要素を適切な位置まで上に移動させる"""
        new_item = self.heap[pos]

        while pos > 0:
            parent_pos = self._parent(pos)
            parent_val = self.heap[parent_pos]
            if new_item < parent_val:
                self.heap[pos] = parent_val
                pos = parent_pos
            else:
                break
        
        self.heap[pos] = new_item

    def _sift_down(self, pos):
        """要素を適切な位置まで下に移動させる"""
        end_pos = len(self.heap)
        start_pos = pos
        new_item = self.heap[pos]

        child_pos = self._left_child(pos)

        while child_pos < end_pos:
            right_pos = self._right_child(pos)
            if right_pos < end_pos and self.heap[right_pos] < self.heap[child_pos]:
                child_pos = right_pos
            
            self.heap[pos] = self.heap[child_pos]
            pos = child_pos
            child_pos = self._left_child(pos)
        
        self.heap[pos] = new_item
        self._sift_up(pos)
        
    def size(self):
        return len(self.heap)
    
    def push(self, val):
        """ヒープに要素を追加"""
        self.heap.append(val)
        self._sift_up(len(self.heap) - 1)
    
    def pop(self):
        """ヒープから最小値を取り出す"""
        if not self.heap:
            raise IndexError("pop from empty heap")
        
        min_val = self.heap[0]
        last_val = self.heap.pop()

        if self.heap:
            self.heap[0] = last_val
            self._sift_down(0)
        
        return min_val
    
    def peek(self):
        """最小要素を確認（取り出さない）"""
        if not self.heap:
            raise IndexError("peek from empty heap")
        return self.heap[0]

class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = MinHeap()

        for n in nums:
            self.add(n)

    def add(self, val: int) -> int:
        self.nums.push(val)
        if self.nums.size() > self.k:
            self.nums.pop()
        
        return self.nums.peek()
```

## Step3

```py
class KthLargest:
    def __init__(self, k: int, nums: List[int]):
        self.k = k
        self.nums = []

        for n in nums:
            self.add(n)

    def add(self, val: int) -> int:
        heapq.heappush(self.nums, val)
        if len(self.nums) > self.k:
            heapq.heappop(self.nums)
        
        return self.nums[0]
```
* 解答時間:
  - 1回目: 1:57
  - 2回目: 1:24
  - 3機目: 1:53
