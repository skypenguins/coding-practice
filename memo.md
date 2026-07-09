# 198. House Robber
- 問題: https://leetcode.com/problems/house-robber/
- 言語: Python

## Step1
### 方針
- 家の順序とmoneyを組にしたリストを作成し、リスト自体をmoneyを高い順にソートし、訪問する優先度順とする
- 上から順番に訪問していき、優先度が一つ前と一つ後の家が隣り合う場合はその家の訪問はスキップする
#### 見積
- リスト作成の時間計算量: $O(n)$ 、$n = 100$ の時、 $10^{2} \div 10^{7} = 10^{-5}$ s 
- ソートの時間計算量: $O(n \log n)$ 、$n = 100$ の時、 $10^{2} \times 6.644 \div 10^{7} = 0.0664 $ ms 
- デバッグしていたら15分経過

### WA
```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) == 1:
            return nums[0]

        if len(nums) == 2:
            return max(nums[0], nums[1])
        
        money = 0
        money_to_house = [(i, value) for i, value in enumerate(nums)]
        sorted_money_to_house = list(sorted(money_to_house, key=lambda item: item[1], reverse=True))

        for j, house in enumerate(sorted_money_to_house):
            if j == 0 and sorted_money_to_house[j + 1][0] != house[0]:
                money += house[1]
                continue
            
            if len(sorted_money_to_house) - 1 and sorted_money_to_house[j - 1][0] != house[0]:
                money += house[1]
                continue

            if j != 0 and j != len(sorted_money_to_house) - 1:
                if sorted_money_to_house[j - 1][0] != house[0] and sorted_money_to_house[j + 1][0] != house[0]:
                    money += house[1]
        
        return money
```
- 隣接判定は `sorted_money_to_house[j - 1][0] != house[0]` ではなく `abs(sorted_money_to_house[j - 1][0] - house[0]) == 1`

### 正答
#### 方針
- ある家を選ぶかどうかは、そこまでの累積最適解に依存するため、「値が大きい家から適当に拾っていく」という貪欲法では、正しい答えは保証されない
  - 「真ん中の1軒が高い」せいで、両端の合計の方が実は得、というケースを見逃す
- DPで解く
```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        prev, curr = 0, 0
        for num in nums:
            prev, curr = curr, max(curr, prev + num)
        return curr
```
##### 一時変数を使う場合
```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        prev, curr = 0, 0
        for num in nums:
            new_curr = max(curr, prev + num)
            prev = curr
            curr = new_curr
        return curr
```
- 時間計算量: $O(n)$
  - $n = 100$ の時、 $10^{2} \div 10^{7} = 10^{-5}$ s 
- 空間計算量: $O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.8mp41bsmfqeb

- https://github.com/Mike0121/LeetCode/pull/47
  - Python
  - 同じくDPだが、配列（リスト）を埋めていく方法
  - 空間計算量: $O(n)$
  - 
    ```py
    class Solution:
        def rob(self, nums: List[int]) -> int:
            if len(nums) <= 1:
                return max(nums, default = 0)
            max_values = [0] * len(nums)
            max_values[0] = nums[0]
            max_values[1] = max(nums[0], nums[1])
            for i in range(2, len(nums)):
                max_values[i] = max(max_values[i - 2] + nums[i], max_values[i - 1])
            return max_values[-1]
    ```
  - 漸化式を2変数で表現するより、こちらの方が理解しやすいと思った
  - メモ化再帰
    - フィボナッチ数列と同じ
  - 
    ```py
    class Solution:
        def rob(self, nums: List[int]) -> int:
            memo = {}
    
            def find_max_value(i: int) -> int:
                if i >= len(nums):
                    return 0
    
                if i in memo:
                    return memo[i]
                    
                max_value = max(nums[i] + find_max_value(i + 2), find_max_value(i + 1))
    
                memo[i] = max_value
                return max_value
    
            return find_max_value(0)
    ```

- https://github.com/Yoshiki-Iwasa/Arai60/pull/50
  - Rust
  - 
    > 各家の前に、泥棒の手下が一人ずつ立って、前から伝言をもらって、最後のところで求めたい数字を知りたいとします。
    > 
    > 「伝言」の内容は「ここまで最大いくら取れる、俺の眼の前の家に盗みに入らないとすると最大いくら取れる」の二つだけじゃないですか。
    src: https://github.com/Yoshiki-Iwasa/Arai60/pull/50#discussion_r1717915563

- https://github.com/hroc135/leetcode/pull/33
  - Go

## Step3
### 読みやすく書き直したコード
```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) <= 1:
            return max(nums, default = 0)
        
        max_values = [0] * len(nums)
        max_values[0] = nums[0]
        max_values[1] = max(nums[0], nums[1])
        for i in range(2, len(nums)):
            max_values[i] = max(nums[i] + max_values[i - 2], max_values[i - 1])
        
        return max_values[-1]
```
- 所要時間:
  - 1回目: 2:15
  - 2回目: 2:15
  - 3回目: 2:23
