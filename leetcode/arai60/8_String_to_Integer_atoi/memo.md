# 8. String to Integer (atoi)
* 問題リンク: https://leetcode.com/problems/string-to-integer-atoi/
* 使用言語: Python3

# Step1
* 問題のアルゴリズムを素直に実装しようとしたつもり
* 途中まで「`0`が1つの場合と連続する場合で処理を分岐する」の方針でアルゴリズムを考えていたので、なかなかテストケースを通過しなかった
* 「ゼロ埋めした数字のみの文字列」を整数型にキャストする方針に変更した
  - `0`が1つの場合でも、連続していても適切に整数型に変換される
* それでも条件分岐が多く、きたないコードな気がする

```python
class Solution: 
    def myAtoi(self, s: str) -> int:
        sign = ""
        str_digits = [str(d) for d in range(0, 10)]
        no_whitespace_s = s.lstrip()
        only_digits_s = ""

        if no_whitespace_s == "":
            return 0

        if len(no_whitespace_s) == 1 and no_whitespace_s in str_digits:
            return int(s)
        if len(no_whitespace_s) == 1 and no_whitespace_s not in str_digits:
            return 0
        
        if no_whitespace_s[0] == "-":
            sign = "-"
            no_sign_s = no_whitespace_s[1:]
        elif no_whitespace_s[0] == "+":
            sign = ""
            no_sign_s = no_whitespace_s[1:]
        else:
            no_sign_s = no_whitespace_s
        
        if no_sign_s[0] not in str_digits:
            return 0
        
        for char in no_sign_s:
            if char in str_digits:
                only_digits_s += char
            else:
                break
        
        int_digits = int(sign + str(int(only_digits_s)))
        if int_digits <= -2147483648:
            return -2147483648
        elif int_digits >= 2147483647:
            return 2147483647
        else:
            return int_digits
```

* CPythonの`int`型は、32ビット符号付き整数型ではなく**任意長（640〜4300桁まで）の整数型**
  - > CPython の int 型は、任意の長さの数をバイナリ形式で保存したものです (一般に "bignum" または多倍長整数として知られています)。基数が2のべき乗でない限り、線形の時間で文字列をバイナリ整数に、あるいはバイナリ整数を文字列に変換できるアルゴリズムは存在しません。10進数に対するアルゴリズムでは、最もよく知られているものでさえ、2次に近い (sub-quadratic) 複雑さになります。高速な CPU でも、 int('1' * 500_000) のような大きな数の変換は1秒以上かかる可能性があります。
  - cf. https://docs.python.org/ja/3.13/library/stdtypes.html#integer-string-conversion-length-limitation

* 書いてから思ったが、`int(sign + str(int(only_digits_s)))` は単に `sign * int(only_digits_s)` で良かったかも

* 実行時間: 7ms
* メモリ使用量: 17.15MB
* 時間計算量: $O(n^2)$
* 空間計算量: $O(1)$
* 解答時間: 1:13:00

# Step2
## 他の方のコードを読む
* https://github.com/hroc135/leetcode/blob/b232e8d2b313d15d2b480e896a2b8be37498b84c/8StringToInteger(atoi).md
  - 状態機械（ステートマシン、State Machine）を使う方法
  - Python 3.10以降でGoの`switch`文に相当する`match`文が使えるので同じような書き方ができそう
  - `unicode.IsDigit()`関数で文字列が数字のみかどうか判定している
    - 自分のコードは自前で実装したので、関数化するかPython組み込み型`str`の関数`str.isdigit()`を使うべきだった
      - cf. https://docs.python.org/3/library/stdtypes.html#str.isdigit
  - Goでは`s[index]`で文字列の特定位置にアクセスすると、その位置のUnicodeコードポイントが取得される
    - 後述するようなオーソドックスな方法
    - cf. https://go.dev/blog/strings#code-points-characters-and-runes

* https://github.com/olsen-blue/Arai60/blob/d2841f0e2c491ccb1e130f9413c00a102b85fb37/8.%20String%20to%20Integer%20(atoi).md
  - 後述するようなオーソドックスな方法
  - 自分が使った`int()`は万能すぎるので、今回の問題では使わない方が良かったかも

## 雑感
* Cやそれに影響を受けた言語だと、文字列リテラルをコードポイントの配列のように操作できるので数値の判定が楽だが、Pythonだと単にその位置の文字を取得するので `ord()` 関数で文字をコードポイントに変換してやる必要がある
* むかし、HTTPサーバをCで実装したときにポート番号を整数型に変換するときに標準Cライブラリ関数 `atoi()` を使ったことを思い出した
  - 内部実装まで気にしたことがなかったが、調べてみると `str[i] - '0'` のようにASCII範囲の数字が1ずつ並んでいることを利用し、`num = num * 10 + 新しい桁 * sign` で既存の結果を10倍して新しい桁を加算することで、左から右へ数値を構築していくアルゴリズムはオーソドックスな方法らしいことがわかった
  - しかしライブラリの実装[glibc](https://github.com/bminor/glibc/blob/master/stdlib/atoi.c)、[BSD libc](https://github.com/freebsd/freebsd/blob/master/lib/libc/stdlib/atoi.c)、[Newlib](https://github.com/bminor/newlib/blob/master/newlib/libc/stdlib/atoi.c)では、単純に `strtol()` を呼び出しているだけ
    - cf. https://yohhoy.hatenadiary.jp/entry/20200604/p1

## 読みやすく整え直したコード
```python
class Solution: 
    def myAtoi(self, s: str) -> int:
        idx = 0

        while idx < len(s) and s[idx] == " ":
            idx += 1
        if idx == len(s):
            return 0
        
        sign = 1
        if s[idx] == "+":
            idx += 1
        elif s[idx] == "-":
            sign = -1
            idx += 1
        
        num = 0
        MAX_INT = 2 ** 31 - 1
        MIN_INT = -2 ** 31
        while idx < len(s) and ord("0") <= ord(s[idx]) <= ord("9"):
            digit = ord(s[idx]) - ord("0")
            num = num * 10 + sign * digit

            if num > MAX_INT:
                return MAX_INT
            if num < MIN_INT:
                return MIN_INT
            idx += 1
        
        return num
```
* オーソドックスな方法で解き直した
  - `lstrip()`を自前実装に変更
    - これを使うと、インデックスの情報を取得するのが面倒
* 実行時間: 4ms
* メモリ使用量: 18.06MB
* 時間計算量: $O(n)$
* 空間計算量: $O(1)$

## Step3

```python
class Solution: 
    def myAtoi(self, s: str) -> int:
        idx = 0

        while idx < len(s) and s[idx] == " ":
            idx += 1
        if idx == len(s):
            return 0
        
        sign = 1
        if s[idx] == "+":
            idx += 1
        elif s[idx] == "-":
            sign = -1
            idx += 1
        
        num = 0
        MAX_INT = 2 ** 31 - 1
        MIN_INT = -2 ** 31
        while idx < len(s) and ord("0") <= ord(s[idx]) <= ord("9"):
            digit = ord(s[idx]) - ord("0")
            num = num * 10 + sign * digit

            if num < MIN_INT:
                return MIN_INT
            if num > MAX_INT:
                return MAX_INT
            idx += 1
        
        return num
```
* 解答時間
  - 1回目: 4:40
  - 2回目: 5:38
  - 3回目: 4:34
