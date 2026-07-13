# 213. House Robber II
- 問題: https://leetcode.com/problems/house-robber-ii/
- 言語: Python

## Step1
### 方針
- 見積: 最大 $n = 100$ 、 計算量: $O(n)$ 、 Pythonの実行時間: $10^{7}$ ステップ/秒 とすると、$10^{-5}$ 秒
- `198. House Robber` と条件はほぼ同じだが、家が環状（最初の家と最後の家が隣接）に配置されている
  - 環状をなんとかしてただの配列にしようと考えて10分経過

### 正答
#### 方針
- 以下をそれぞれ実行する
  - 最後の家を除いた `nums[:-1]` で線形DPを実行
  - 最初の家を除いた `nums[1:]` で線形DPを実行

```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) <= 1:
            return max(nums, default = 0)
        if len(nums) == 2:
            return max(nums)

        def rob_linear(houses: List[int]) -> int:
            if len(houses) <= 1:
                return max(houses, default = 0)

            max_values = [0] * len(houses)
            max_values[0] = houses[0]
            max_values[1] = max(houses[0], houses[1])

            for i in range(2, len(houses)):
                max_values[i] = max(max_values[i - 2] + houses[i], max_values[i - 1])

            return max_values[-1]

        # 最後を除くケース と 最初を除くケース の大きい方
        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```
- 時間計算量: $O(n)$
- 空間計算量: $O(n)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.8gnsrornagjc

- https://github.com/TakayaShirai/leetcode_practice/pull/35
  - Dart
  - 考え方
    - > 極端な話、まず、2^n 通り列挙して、そこから条件を満たしているものだけ取り出して、それぞれ和を取って最大値を取ればいけますよね。この 2^n 枚の紙をバインダーに挟んで、このパターンで盗んだときに、いくらになるかを計算し、最大にならない紙を捨てて、一番大きい紙を見つける方法でやってみましょう。
      >  
      > 各家の前に、泥棒が立って、バインダーが回ってきます。2^n 枚の紙の自分の担当の情報を埋めていくわけです。
      > 
      > 泥棒文句言うと思うんですね。ほとんどの紙は条件を満たしていません。先に捨てておけよ。
      > あと、最大になりようがないのも回ってきますね。自分は10人目なのにまだ1件も入っていない。先に捨てておけよ。
      > 
      > え、でも全部捨てたら怒られますよね。じゃあ、何は捨てたらだめですか。
      > 
      > これすると同じようなことが書かれている紙だけが残ります。
      > これ、1件目の家で盗んだか盗んでいないか、一つ前の家で盗んだか盗んでいないか、それぞれの最大の4種類だけですね。
        - src: https://github.com/TakayaShirai/leetcode_practice/pull/35#discussion_r2929595782

## Step3
```py
class Solution:
    def rob(self, nums: List[int]) -> int:
        if len(nums) <= 1:
            return max(nums, default = 0)
        if len(nums) == 2:
            return max(nums)

        def rob_linear(houses: List[int]) -> int:
            if len(houses) <= 1:
                return max(houses, default = 0)

            max_values = [0] * len(houses)
            max_values[0] = houses[0]
            max_values[1] = max(houses[0], houses[1])

            for i in range(2, len(houses)):
                max_values[i] = max(max_values[i - 2] + houses[i], max_values[i - 1])

            return max_values[-1]

        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```
- 所要時間:
  - 1回目: 4:03
  - 2回目: 3:37
  - 3回目: 4:11
