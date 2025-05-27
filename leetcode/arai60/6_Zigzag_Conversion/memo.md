# 6. Zigzag Conversion
* 問題リンク: https://leetcode.com/problems/zigzag-conversion/
* 使用言語: Python3

# Step1
* `numRows` と行ごとに対応する `s` のインデックスに何らかの関係性を見出そうとしたが、その時点で5分経過したため答えを見る
## 見た解答
* `s` のそれぞれの文字と行番号には以下のような対応関係がある
```
"abcdefghi"
 123432123 (= それぞれが行番号)
```
  - 文字列の先頭から順番に数値を`1`からインクリメントしていき`numRows`に到達したら、今度は`1`までデクリメントする
    - これを最後の文字に到達するまで繰り返す
    - `direction` でインクリメントするかデクリメントするかを制御
* `numRows` の行数の2次元配列を作成し、行番号に対応する文字を追加していく
* 2次元配列を平滑化（flatten）して文字列に変換する

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s
        
        row_i = 0
        direction = 1
        zigzag_rows = [[] for _ in range(numRows)]

        for char in s:
            zigzag_rows[row_i].append(char)
            if row_i == 0:
                direction = 1
            elif row_i == numRows - 1:
                direction = -1
            row_i += direction

        for i in range(numRows):
            zigzag_rows[i] = ''.join(zigzag_rows[i])
        
        flattened_rows = ''.join(zigzag_rows)
        return flattened_rows
```

* 実行時間: 7ms
* メモリ使用量: 18.19MB
* 時間計算量: $O(n)$
* 空間計算量: $O(n)$
* 解答時間: 4:45

# Step2
## 他の方のコードを読む
* https://github.com/Miyamoto-tryk/leetcode-arai60/pull/5/commits/805d50034516672fbbc262c940f5d8d33f268f96
  - 同じく2次元配列を作成して、行ごとに文字を追加する方法
  - 行番号の生成を以下の部分で算出しているが、初見だと何をやっているのかピンと来なかった
```cpp
const int MOD = max(numRows + (numRows - 2), 1);  // 一往復の文字数
int row = i % MOD;
if (row >= numRows) {
    row = MOD - row;
}
```
* https://github.com/hroc135/leetcode/pull/55/commits/561c6eab4394d6d64e953349a35f16e6a3b4a18a
  - 2次元配列を作成し、文字列を先頭から走査し文字ごとに2次元配列の座標 `(row, col)` で対応づける素直な方法
    - 最初にこれを思いつくべきだった
  - 時間計算量 $O(n^2)$
* https://github.com/saagchicken/coding_practice/pull/22/files#diff-641171eaccfc6c3f147cc8650b9302e2abf6fcaf35f2c3c35c8c6e0a4a83d31eR73
  - 行番号を生成するアルゴリズムを関数として切り出す

```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s

        row_i = 0
        direction = 1
        zigzag_rows = [[] for _ in range(numRows)]

        for char in s:
            zigzag_rows[row_i].append(char)
            if row_i == 0:
                direction = 1
            elif row_i == numRows - 1:
                direction = -1
            row_i += direction
        
        for i in range(numRows):
            zigzag_rows[i] = ''.join(zigzag_rows[i])
        
        flattened_rows = ''.join(zigzag_rows)
        return flattened_rows
```
* 特に変更なし
  - 行番号のインクリメントを `row_i += 1` 、デクリメントを `row_i -= 1` で表現しようとしたが条件分岐が増えそうなのでやめた

# Step3
```python
class Solution:
    def convert(self, s: str, numRows: int) -> str:
        if numRows == 1 or numRows >= len(s):
            return s
        
        row_i = 0
        direction = 1
        zigzag_rows = [[] for _ in range(numRows)]

        for char in s:
            zigzag_rows[row_i].append(char)
            if row_i == 0:
                direction = 1
            elif row_i == numRows - 1:
                direction = -1
            row_i += direction
        
        for i in range(numRows):
            zigzag_rows[i] = ''.join(zigzag_rows[i])
        flattened_rows = ''.join(zigzag_rows)
        
        return flattened_rows
```
* 解答時間
  - 1回目: 3:47
  - 2回目: 3:55
  - 3回目: 5:07
