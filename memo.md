# 1011. Capacity To Ship Packages Within D Days
- 問題: https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/
- 言語: Python

## Step1
### 方針
- `days` 日以内に重量 `weights` を持つ荷物を輸送できる船の最小積載容量を返す、順序は変えていはいけない
- 軽い荷物はなるべくまとめた方がいい、逆に重い荷物はなるべく単独で運ぶべき
- 少なくとも `weights` の最大値以上の積載容量は必要
- 初日に輸送する個数を、荷物をそれぞれの日に等しく分散させた場合の個数から1個づつ増やしていく
- 1個増やしたらそれぞれの日の個数で重量の合計値をとり、最大の合計値＞他の日のぞれぞれの合計値を満たすか調べる
- これを満たすまで1個づつ増やすのを繰り返す
- 満たさなかったら、1個づつ減らしつつ2日目の個数を1個づつ増やす
- これを最終日まで繰り返す
- 方針を考えていたら15分経過

### 正答
#### 方針: 積載容量を二分探索
- 以下の単調性がある
  - 積載容量が大きいほど、必要な日数は単調減少する
  - 積載容量が小さいほど、必要な日数は単調増加する
- 「積載容量 → 必要日数」は単調関数なので、「D日以内に運べる最小の積載容量」を二分探索で求める
- 探索範囲:
  - 下限 (`lo`): `max(weights)`
    - 一番重い荷物（最大値）以上の積載容量は必要
  - 上限 (`hi`): `sum(weights)`
    - 1日で全部積めば必ずD日以内（$D≥1$ だから）に収まる
- ある積載容量を仮定したとき何日で運びきれるかを貪欲にシミュレーションする（`feasible()`）
  - 順番はそのままであるため「積めるだけ積んでから次の日に回す」のが常に最適になるから

##### コード
```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def feasible(capacity: int) -> bool:
            day_count = 1
            cur_load = 0
            for w in weights:
                if cur_load + w > capacity:
                    day_count += 1
                    cur_load = 0
                cur_load += w
            return day_count <= days

        lo, hi = max(weights), sum(weights)
        while lo < hi:
            mid = (lo + hi) // 2
            if feasible(mid):
                hi = mid  # midで積載できるなら、もっと小さくできるか探索
            else:
                lo = mid + 1  # midで積載できないなら、もっと大きくする
        return lo
```
- 時間計算量: $O(n \log(n))$, `n`は `weights` の長さ
  - 見積: 
- 空間計算量: $O(1)$

### （参考）TLEになる解答: DP
- 最初の i 個の荷物を d 日で運ぶときの、最小の「1日あたり最大積載量」を状態としてもつ
- 「i個をd日に分割する全パターンのうち、各パターンの最大値をとり、そのパターン間での最小値」を求める
- i個の荷物をd日に分けるとき、「最後の1日(d日目)に何個目から何個目までを積むか」で場合分け

```py
from typing import List
from itertools import accumulate


class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        n = len(weights)
        # prefix_sum[i] = sum(weights[0:i])
        prefix_sum = [0] + list(accumulate(weights))

        INF = float("inf")
        # max_capacity_per_day[d][i] : 最初のi個をd日で運ぶときの最小の最大積載量
        max_capacity_per_day = [[INF] * (n + 1) for _ in range(days + 1)]
        max_capacity_per_day[0][0] = 0

        for d in range(1, days + 1):
            for i in range(n + 1):
                for j in range(i):  # 最後の日に weights[j:i] を積む
                    if max_capacity_per_day[d - 1][j] == INF:
                        continue
                    last_day_load = prefix_sum[i] - prefix_sum[j]
                    candidate = max(max_capacity_per_day[d - 1][j], last_day_load)
                    max_capacity_per_day[d][i] = min(
                        max_capacity_per_day[d][i], candidate
                    )

        return max_capacity_per_day[days][n]
```
- 時間計算量: $O(n^{2} · days)$
  - 見積: $n = 5 × 10^{4}$, Python: $10^{7}$ ステップ/秒 として
    - ステップ数: $ \rm{days} × n(n+1)/2 = 約 6.25 × 10^{13} 回$
    - 実行時間: $6.25 × 10^{13} / 10^7 = 6.25 × 10^6 秒 ≈ 1,736 時間 ≈ 72.3 日$
- 空間計算量: $O(1)$
  - 見積: 
    - 要素数: $(days + 1)(n + 1) = 50,001^2 ≈ 2.5 × 10^9$
    - サイズ: $2.5 × 10^9 × 8 ≈ 20 \rm{GB}$

## Step2
- 典型コメント集: なし

- https://github.com/naoto-iwase/leetcode/pull/27
  - Python
  - ある積載容量で運べる運べないの判定にも二分探索を使用
    - ここの判定には貪欲法が使えるが、daysがnに比べて小さい場合は速い？
    - 判定で「現在の合計重量が積載容量を超過したら、日付を進めてリセット」の方が直観的だと思った
  - 二分探索に `bisect_left` を使用
  - weightが0や負の値の時の現実世界での対応付けはなるほどと思った

- https://github.com/mamo3gr/arai60/pull/42
  - Python
  - `lo`、`hi` より `min_capacity` 、 `max_capacity` の方がわかりやすい

- https://github.com/garunitule/coding_practice/pull/44
  - Python
  - 不変条件は「capacity < left_capacityの場合、daysより大きい」、「right < capacityの場合、days以下」
  - 二分探索の不変条件として、上記を採用したため、二分探索では bool ではなく days を返すようにしている
    - 他の用途で使ったり保守性が高まるかも？

## Step3
- 変数名、関数名の改善
- 二分探索に `bisect_left` を使用

```py
class Solution:
    def shipWithinDays(self, weights: List[int], days: int) -> int:
        def is_shipped_within_days(capacity: int) -> bool:
            day_count = 1
            loaded = 0
            for weight in weights:
                if loaded + weight > capacity:
                    day_count += 1
                    loaded = 0
                loaded += weight
            return day_count <= days

        min_capacity = max(weights)
        max_capacity = sum(weights)
        return bisect_left(
            range(max_capacity + 1), True, key=is_shipped_within_days, lo=min_capacity
        )
```
- 所要時間:
  - 1回目: 3:06
  - 2回目: 3:18
  - 3回目: 4:06
