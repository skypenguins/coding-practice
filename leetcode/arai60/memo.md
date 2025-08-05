# 121. Best Time to Buy and Sell Stock
* 問題: https://leetcode.com/problems/best-time-to-buy-and-sell-stock/
* 言語: Python

## Step1
* 最初は単純に `prices` の最小値と最大値を取得して、 `profit` を算出したが、それだと時系列で過去の最大値を取得するのでWA
* 手作業でやるならどうやるか？をシミュレーション
  - 先頭から順に値（価格）を取得
    - その値が以前に取得した値より小さければ、その値に持ち替える
    - その値が以前に取得した値より大きければ、その値と自分の持つ値（その位置での最小値）の差を算出
      -  その差が以前に算出した `profit` と比較して大きければ、その差で `profit` を更新

### 解答（AC）
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if prices is None:
            return 0
        
        cheapest_price = float("inf")
        profit = 0
        for price in prices:
            if price < cheapest_price:
                cheapest_price = price
            elif price > cheapest_price:
                if profit < price - cheapest_price:
                    profit = price - cheapest_price
        
        return profit
```
* 解答時間: 25:00
* 時間計算量: $O(n)$
* 空間計算量: $O(1)$

## Step2
### 他の人のコードを読む
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.8qw2um7il4s5
- https://github.com/colorbox/leetcode/pull/6
    - C++
    - `prices` を先頭から順に訪れ、そのインデックスまでの最小値を要素に持つリストと末尾から順に訪れ、そのインデックスまでの最大値を要素に持つリストをの2つのリストを作る
    - それを使って `maxProfit` を算出 
    - 関数型っぽい感覚
- https://github.com/goto-untrapped/Arai60/pull/58
   - Java
   - 同じ方法、先頭から訪れてその位置での最小値の更新と同時に最大利益も出す方法、末尾から訪れてその位置での最大値の更新と同時に最大利益も出す方法、i日目以前の最小の値とi日目以後の最大の値を持ち回って日毎に比較する方法
     - 最後の方法は関数型っぽい感覚
       - cf. https://github.com/goto-untrapped/Arai60/pull/58/files#r1782742318
       - ```haskell
         prices = [2,4,6,1,3,5]
         minPrices = scanl min (prices!!0) prices
         profit = zipWith (-) prices minPrices
         maxProfit = foldl max 0 profit
         ```
- https://github.com/Kitaken0107/GrindEasy/pull/7
  - Python
  - 総当たり法


### 読みやすく書き直したコード
* `prices.length >= 1` なので最初の2行は要らなかった
* `elif price > cheapest_price:` の処理は `max()` で書き直せる

```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        cheapest_price = float("inf")
        profit = 0

        for price in prices:
            if price < cheapest_price:
                cheapest_price = price
            elif price > cheapest_price:
                profit = max(profit, price - cheapest_price)
        
        return profit
```

# Step3
```py
class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        cheapest_price = float("inf")
        profit = 0

        for price in prices:
            if price < cheapest_price:
                cheapest_price = price
            elif price > cheapest_price:
                profit = max(profit, price - cheapest_price)
        
        return profit
```
* 解答時間
  - 1回目: 1:52
  - 2回目: 1:34
  - 3回目: 1:27
