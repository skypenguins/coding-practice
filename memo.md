# 122. Best Time to Buy and Sell Stock II
- 問題: https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/
- 言語: Python

## Step1
### 方針
- `121. Best Time to Buy and Sell Stock` と異なり、複数回株を売買することで `profit` を最大化する。一度に保持できる株は1個だけ
- 1回しか売買しない場合は、121の方法で良い
- 手作業でやるならどうやるかを考えていたら15分経過
  - 売却したタイミングでそれまでの最安値をリセットするのは思いついた

## WA
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        cheapest_price = float("inf")
        total_profit = 0
        profit = 0

        for i, price in enumerate(prices):
            cheapest_price = min(cheapest_price, price)
            profit = max(profit, price - cheapest_price)
            if profit > 0:
                total_profit += profit
                cheapest_price = float("inf")
                profit = 0
                
        return total_profit
```
- `profit` は必ず正の値になるので、 `if profit > 0` は無意味
- 利益が少しでもプラスになった瞬間に確定させてリセットしてしまう

## 正答
- 以下はすべて同じ「上昇区間の差分をすべて拾う」というロジックを異なる形で表現している

### 方針1
- 利益がプラスになってもすぐには確定せず、「次の日の価格が下がる（＝ピークを迎えた）」か「配列の最後に到達した」タイミングで初めて利益を確定
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        cheapest_price = float("inf")
        total_profit = 0
        profit = 0

        for i, price in enumerate(prices):
            cheapest_price = min(cheapest_price, price)
            profit = max(profit, price - cheapest_price)
            # ピーク（次が値下がり）か最終日でだけ利益を確定させる
            if i == len(prices) - 1 or prices[i + 1] < price:
                total_profit += profit
                cheapest_price = float("inf")
                profit = 0

        return total_profit
```
### 方針2
- 連続する日の値上がり分をすべて足す
  - 貪欲法（Greedy）
  - 「安く買って高く売る」を細かく繰り返しても、一気に買って一気に売っても、利益の合計は数学的に同じになる、という発想
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        return sum(max(0, prices[i] - prices[i - 1]) for i in range(1, len(prices)))
```
- 時間計算量：$O(n)$
- 空間計算量：$O(1)$

### 方針3
- 谷と山（Valley-Peak）を明示的に探す
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        i, n = 0, len(prices)
        total_profit = 0

        while i < n - 1:
            # 谷を探す
            while i < n - 1 and prices[i] >= prices[i + 1]:
                i += 1
            valley = prices[i]

            # 山を探す
            while i < n - 1 and prices[i] <= prices[i + 1]:
                i += 1
            peak = prices[i]

            total_profit += peak - valley

        return total_profit
```
- 時間計算量：$O(n)$
  - 外側・内側のループを合計しても i の移動回数は高々 n 回
- 空間計算量：$O(1)$

### 方針4
- DPで考える
  - 「株を持っている状態」と「持っていない状態」の2つを管理する方法
  - あり得る売買履歴の世界線を大量に走らせて、各状態ごとに一番利益の出る世界線だけ残す
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        hold = -prices[0]      # 株を保有している場合の最大利益
        not_hold = 0           # 株を保有していない場合の最大利益

        for price in prices[1:]:
            hold, not_hold = max(hold, not_hold - price), max(not_hold, hold + price)

        return not_hold
```
- タプルで代入し、右側から計算する
- 以下のイメージ？
```
今日の hold =
  max(
    昨日すでに持っていた世界線,
    昨日持っていなくて、今日買う世界線
  )

今日の not_hold =
  max(
    昨日すでに持っていなかった世界線,
    昨日持っていて、今日売る世界線
  )
```
- 最後は必ず `not_hold`（株を売り切った状態）が答え。株を持ったまま終わっても得はしないから。
- 「取引回数がk回まで」「売却に手数料がかかる」といった拡張がしやすい
- 時間計算量：$O(n)$
- 空間計算量：$O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.h4s44httjcso

- https://github.com/goto-untrapped/Arai60/pull/59
  - > 毎日できることは、株を持っているか、お金を持っているかの2択なので、未来が見える人になったとして、どちらがいいかを考えればいいのです。
    - src: https://github.com/goto-untrapped/Arai60/pull/59/changes#r1782748689

- https://github.com/nittoco/leetcode/pull/44
  - 過去と現在の価格を比較し、過去の株を買ったことにしておいて、安い場合は利益、高い場合は損失とする

## Step3
### 読みやすく書き直したコード
#### 方針4
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        hold = -prices[0]
        not_hold = 0

        for price in prices[1:]:
            hold, not_hold = max(hold, not_hold - price), max(not_hold, hold + price)

        return not_hold
```
- 所要時間: 
  - 1回目: 1:52
  - 2回目: 1:12
  - 3回目: 1:39
