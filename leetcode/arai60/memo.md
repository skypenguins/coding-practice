# 22. Generate Parentheses
- 問題: https://leetcode.com/problems/generate-parentheses/
- 言語: Python

## Step1
- 与えられた丸カッコ `n` 組から取りうる組み合わせをすべて生成する
- 文字数は `2n` となるので `(2n)!` 通りの文字列から適切な形式のカッコの組み合わせのみを探索する方針を考えたが、事前条件 `1 <= n <= 8` から最大 $16! = 20,922,789,888,000$ 通りとなるので確実にTLEになる
- 5分経っていたので正答を見る

### 正答
- 有効な `(` の後にのみ `)`を追加する方針
  - `)` の後に `(` が来ることはない
#### 再帰DFS版
```py
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        def _gen_parenthesis(left_n, right_n, s):
            if len(s) == n * 2:
                result.append(s)
                return
            
            if left_n < n:
                _gen_parenthesis(left_n + 1, right_n, s + "(")
            
            if right_n < left_n:
                _gen_parenthesis(left_n, right_n + 1, s + ")")
        
        result = []
        _gen_parenthesis(0, 0, "")
        return result
```
#### ループDFS版
```py
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        result = []
        left_n = right_n = 0
        combinations = [(left_n, right_n, "")]

        while combinations:
            left_n, right_n, parens = combinations.pop()
            if len(parens) == 2 * n:
                result.append(parens)
                continue
            
            if left_n < n:
                combinations.append((left_n + 1, right_n, parens + "("))
            
            if right_n < left_n:
                combinations.append((left_n, right_n + 1, parens + ")"))
        
        return result
```
- `right_n < left_n` は、対応する閉カッコがない開カッコが存在している状態
- 各ステップの時点で、`対応する閉カッコがないまだ開カッコの個数` 、　`開カッコに対応する閉カッコの個数` の状態を意識すべきだと思った
- 具体的なnの値で樹形図を書き出してみるべき

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.lqk42foli42r

1. https://github.com/fhiyo/leetcode/pull/53
  - Python
  - 5パターン
    1. `n` からカッコ数だけ消費していく方法
        - `list.append()` していって最後に `join` して文字列を構築しているのだが、 `list.append()` を見ると文字列構築であると直観的に思えない感覚が個人的にはあるので理解に時間がかかった
        - `yield` を使っているのでメモリ効率が良さそう
        - `yield from` を使ったコードは初めて見た
            - Python 3.3から導入されたらしい
            - cf. https://docs.python.org/3.11/reference/expressions.html#yield-expressions
        - `map()`＋ラムダ式＋再帰（イテレータ）の組み合わせは読みづらい…再帰があるから読みづらい？なかったらそうでもない気がする
    2. 再帰で以下の2つの変数で状態管理をする方法。上の方法と違って、戻るときに**先頭に**カッコを追加していくため、**右から左に**文字列を構築していく
        - 現在までに開かれていてまだ閉じられていない左カッコ `(` の個数（＝「閉じる必要のある `(` の数」）
        - これから置ける `)` の残り回数。この値が 0 になるまで `)` を置いていく
        - ベースケースは上記2つの変数の値が一致する時
    3. 2の方法で、閉カッコを追加する条件を明示的に示した方法
    4. 1の方法でジェネレータではなくlistを返すようにした方法。 `@cache` が使える
    5. 4のジェネレータをキャッシュするようにした方法
  - 時間計算量、空間計算量で出てくる数は[カタラン数](https://en.wikipedia.org/wiki/Catalan_number)と言うらしい
    - 単語として聞いたことはある

2. https://github.com/shining-ai/leetcode/pull/53
  - Python
  - Step1と同じ方法

3. https://github.com/frinfo702/leetcode-arai60/pull/10
  - Python
  - 変数名 `num_opens` 、 `num_closes` は分かりやすいと思った
  - > 計算量は、そもそもの出力の大きさがカタラン数なので、O(4^n / (sqrt(n) * n)) ですが、  
    > さらに str でやると文字列の構築があるので n がかかるはずです。ただ、この文字列の構築は速いので現実的にはそんなに変わらないでしょう。  
    > 計算量はあくまでも極限での振る舞いなので、計算量を使って計算時間を見積もるのが大事です。  
    >   
    > また、 速度が速いかどうかは、普通コーディングにおいてそれほどプライオリティーが高くないです。  
      - ref. https://github.com/frinfo702/leetcode-arai60/pull/10/files#r1881386807. 
  - > code complexity, memory, speed あたりのバランスで選ばれることが多いです。基準としては、将来、問題を起こさない可能性が高いと思われるものが選ばれます。エンジニアリングをするということですね。
    - ref. https://github.com/frinfo702/leetcode-arai60/pull/10/files#r1881512741s

4. https://github.com/nittoco/leetcode/pull/43
5. https://github.com/wf9a5m75/leetcode3/pull/2
  - Python
  - カタラン数の再帰関係
    - `(内側)外側` の形式
    - 最初の `(` とそれに対応する `)` の間に `inside_count` 組の括弧
    - その外側（右側）に `n - 1 - inside_count` 組の括弧
      - cf. https://github.com/wf9a5m75/leetcode3/pull/2
  - ジェネレータはハマると楽しいのはなんとなくわかるかも
  - コードを読んで追っていけば理解はできたが、初手でこの発想は個人的にできないなと思った

6. https://discord.com/channels/1084280443945353267/1252267683731345438/1252591437485441024
  - > 本質的には、すべての場合の分類の仕方として、1文字目は必ず開き括弧で、それに対応する閉じ括弧を考えると、すべての場合がもれなく分類できるということかと思います。

7. https://discord.com/channels/1084280443945353267/1218823830743547914/1231546400714788864
  - 手作業でやるとしたらどうするか？
    - > 一つのやり方として、1文字ずつ開きか閉じかを追加していくというのがありますね。
      >
      > 他に、開き+閉じ*(0~ここまでの開きの数までのどれか)までを一つの仕事とみるという仕事の分担方法もあるように思います。  
      > たとえば、  
      > () + () + ()  
      > () + ( + ())  
      > (  + ()) + ()  
      > ( + () + ())  
      > ( + ( + ()))  
      > こういう風に見るのです。
       - 最初のアプローチはバックトラッキング
       - 2番目のアプローチは、「開き括弧1個を置いたあと、その直後に置ける閉じ括弧の数を 0～（ここまでに置いた開き括弧の数）までのどれかとして『ひとまとまりの仕事』とみなす」 仕事の分担の視点と解釈した
         - つまり 1 回の仕事は
           1. `(` を置く
           2. その時点で「まだ閉じていない開きの数」を `k` とすると、0個〜 `k` 個の `)` を好きに置ける

8. https://github.com/olsen-blue/Arai60/pull/54
  - 「 `(` の位置だけに注目して分類」という視点で、7. のパターンを再度見るとかなりしっくりきた
  - ```
    () + ()  + ()
    () + (   + ())
    (  + ()) + ()
    (  + ()  + ())
    (  + (   + ()))
    ```

## Step3
```py
class Solution:
    def generateParenthesis(self, n: int) -> List[str]:
        result = []
        def gen_parens_helper(parens, open_count, close_count):
            if open_count == n and close_count == n:
                result.append(parens)
                return
            if open_count < n:
                gen_parens_helper(parens + "(", open_count + 1, close_count)
            if close_count < open_count:
                gen_parens_helper(parens + ")", open_count, close_count + 1)
            
        gen_parens_helper("", 0, 0)
        return result
```
- 解答時間:
  - 1回目: 3:05
  - 2回目: 3:13
  - 3回目: 3:38
