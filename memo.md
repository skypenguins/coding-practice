# 50. Pow(x, n)
- 問題: https://leetcode.com/problems/powx-n/
- 言語: Python

- おおよそ15分以内に解答する

## Step1
- 冪乗を計算する
- $-2^{31} <= n <= 2^{31}-1$ であるため、単純な実装 $O(n)$ だとTLE（想定通り）
- たしかビット演算で定数時間で冪乗を計算できたはずだが、改善策が思い浮かばず正答を見る

### TLEとなるコード
```py
class Solution:
    def myPow(self, x: float, n: int) -> float:
        result = x
        if n == 0:
            return 1

        if n > 0:
            for i in range(n-1):
                result *= x
        
        if n < 0:
            for i in range(n*(-1)-1):
                result *= x
            result = 1 / result
        
        return result
```

### 正答
- 方針: 指数の性質を利用して計算を分解していく（「繰り返し二乗法」）
  - $n$ が偶数の時、 $x^{n}=(x^{2})^{n/2}$
  - $n$ が奇数の時、 $x^{n}=x \times x^{n-1}$
  - 指数を半分にしていく
    - 具体例: $2^{10}$ の場合
      - $2^{10} = 2^{5} \times 2^{5}$
      - $2^{5}  = 2 \times 2^{2} \times 2^{2}$
      - $2^{2}  = 2^{1} \times 2^{1}$
- 時間計算量: $O(\log n)$
- 空間計算量: $O(1)$

- $x = 0$, $n < 0$ の場合では数学的には $0$ の負の累乗、つまり $1 / 0$ になるので未定義であるから、エラーを出しても良い

#### 再帰版
```py
class Solution:
    def myPow(self, x: float, n: int) -> float:
        def power(x: float, n: int) -> float:
            if n == 0:
                return 1
            
            half = power(x, n // 2)

            if n % 2 == 0:
                return half * half
            
            return x * half * half

        if n < 0:
            return 1 / power(x, (-1)*n)
        
        return power(x, n)
```

#### whileループ版
```py
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            x = 1 / x
            n = (-1)*n

        result = 1

        while n > 0:
            if n % 2 == 1:
                result *= x

            x *= x
            n //= 2

        return result
```

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.d9ky6ipkmw98

- https://github.com/hroc135/leetcode/pull/43
  - Go
  - 今回は入力が限られているが、実用的な実装にするとエッジケースとしてかなり考慮すべきことが多いなと思った
    - そもそも数字を受け付けないようにするか？Infの扱い、など
    - $0^{0}$ は $1$ のほうが自然らしい
      - cf. https://github.com/hroc135/leetcode/pull/43#discussion_r2651608999

- https://github.com/TORUS0818/leetcode/pull/47
  - Python
  - 指数 `n` を2進数として最上位から読む。毎回 `result` を2乗する。今見ているビットが`1`なら、さらに `x` を掛ける。
  - 元の `n`, `x`を破壊していない方がスッキリしていてわかりやすいと思った

- 処理系CPythonの実装: https://github.com/python/cpython/blob/bdab67e1c795443a0d8f8a5bbeb3a91ac4fd5a19/Objects/longobject.c#L4894
    - メインの処理
      ```c
      for (--i, bit >>= 1;;) {
          for (; bit != 0; bit >>= 1) {
              MULT(z, z, z);
              if (bi & bit) {
                  MULT(z, a, z);
              }
          }
          if (--i < 0) {
              break;
          }
          bi = b->long_value.ob_digit[i];
          bit = (digit)1 << (PyLong_SHIFT-1);
      }
      ```
- 関連知識: 浮動小数点数の仕様 IEEE 754

## Step3
- whileループ版
```py
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            x = 1 / x
            n = (-1)*n

        result = 1

        while n > 0:
            if n % 2 == 1:
                result *= x

            x *= x
            n //= 2

        return result
```
- 所要時間: 
  - 1回目: 2:39
  - 2回目: 1:41
  - 3回目: 1:12
