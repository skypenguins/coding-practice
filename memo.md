# 779. K-th Symbol in Grammar
- 問題: https://leetcode.com/problems/k-th-symbol-in-grammar/
- 言語: Python

## Step1
### 方針
- $2^{30} ≈ 10^{8}$ 
- `0 → 0|1 → 01|10 , 0|1|1|0 → 01|10|10|01 ...` と長さが毎回2倍（$n^2$）になる、すべてのビットパターンを素直に列挙すると指数時間の計算量であり、Python だと 1.07 × 10^{9} / 10^{7} ​≈ 107 秒
  - 実際にはこれより前に空間（ $2^{29}$ 文字 ≈ 5億文字）でMLEの可能性
- 前の状態に依存するからDPか？
- 15分ほど経過したので正答を調べる

### 正答
- 1-indexed
#### 方針1：親子関係をたどる再帰
- n 行目の k 番目の記号は、n-1 行目（前の行）の $⌈k/2⌉$ 番目の記号（＝親）から生成できる
  - 親が 0 のとき → "01" に展開される → 奇数番目は 0、偶数番目は 1
    - k が奇数 → 親と同じ値
  - 親が 1 のとき → "10" に展開される → 奇数番目は 1、偶数番目は 0
    - k が偶数 → 親を反転した値

##### コード
```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0
        parent = self.kthGrammar(n - 1, (k + 1) // 2)
        return parent if k % 2 == 1 else 1 - parent
```
- `(k + 1) // 2` は切り上げ計算
- 時間計算量: $O(n)$
  - 再帰の深さ・呼び出し回数
- 空間計算量: $O(n)$
  - 再帰スタック

#### 方針2: ビット演算（Thue–Morse数列）
- この数列はThue–Morse数列と呼ばれる（らしい）
> `n` 行目 `k` 番目の記号 = `(k-1)` を2進数で表したときの 1の個数（`popcount`）の偶奇

##### コード
```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        return bin(k - 1).count("1") % 2
```
- 時間計算量: $O(\log k)$
- 空間計算量: $O(1)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.adit16u7jkla

- https://github.com/hayashi-ay/leetcode/pull/46
  - Python
  - 愚直にやるとやはりMLEとなる
  - `previous_value` は分かりやすい

- https://github.com/hroc135/leetcode/pull/44
  - Go
  - $2^{29}$ 文字 ≈ 5億文字は、だいたい1GB
    - cf. https://github.com/hroc135/leetcode/pull/44/changes#r2006672241

## Step3
### 方針1: 親子関係をたどる再帰
```py
class Solution:
    def kthGrammar(self, n: int, k: int) -> int:
        if n == 1:
            return 0
        parent = self.kthGrammar(n - 1, (k + 1) // 2)
        return parent if k % 2 == 1 else 1 - parent
```
- 所要時間:
  - 1回目: 1:26
  - 2回目: 1:14
  - 3回目: 1:20
