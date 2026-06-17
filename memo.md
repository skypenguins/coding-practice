# 49. Group Anagrams
- 問題: https://leetcode.com/problems/group-anagrams/
- 言語: Python

- おおよそ15分以内に解答する

## Step1
- 文字列の配列 `strs` が与えられたとき、アナグラム同士をグループ化する。結果は任意の順序。
- 方針:
  - キーに単語の文字列、値に「単語の `strs` でのインデックス」と「単語の文字ごとの出現回数」のタプルを持つ辞書を作成
  - 単語をクエリとして、上記辞書を検索し文字ごとの出現回数が全て同じ値なら同じグループに追加
- 下のコードを書いた時点で30分経っていたので、正答を見る

### 途中まで書いたコード
```py
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        from collections import Counter
        occurrence_by_str = defaultdict()
        for i, word in enumerate(strs):
            counter = Counter(word)
            occurrence_by_str[word] = (i, counter)
        
        for i, word in enumerate(strs):
            c_i = Counter(word)
            for j, c_j in occurrence_by_str[word]:
                if i == j:
                    continue
                for k, v in c_i:
                    if c_i[k] != v:
                        break
```

### 正答
- 2つの文字列がアナグラムであるかどうかは、文字の出現頻度が完全に一致している場合に限る

#### 0. 総当たり
  - 最もシンプルなアプローチ
  - すべての文字列ペア `(i, j)` について調べ、それらが互いにアナグラムかどうかを確認。2つの文字列がアナグラムであるとは、両方の文字列をソートした結果が一致する場合を指す。一致した場合、それらを同じグループにまとめる。
  - 全体の時間計算量は $O(n² × k \log k)$
    - 文字列の個数 $n$ 、 ソートの時間計算量 $O(k \log k)$

#### 1. 文字列をキーとしてソート
  - 文字列の文字をアルファベット順にソートすると、その文字列のすべてのアナグラムは同じソート済み文字列を生成する。このソート済み文字列をハッシュマップのキーとして使う。

```py
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        sorted_strs = defaultdict(list)
        for s in strs:
            key = "".join(sorted(s))
            sorted_strs[key].append(s)
        return list(sorted_strs.values())
```
- 時間計算量: $O(n × k \log k)$
  - 長さ $k$ の $n$ 個の文字列それぞれをソート
- 空間計算量: $O(n × k)$
  - マップにすべての文字列を格納

#### 2. 頻度エンコーディング（最適解）
- ソートの代わりに、各文字列に対して長さ26の頻度配列を1回の線形探索 $O(k)$ で構築。2つの文字列がアナグラムであるかどうかは、それらの頻度配列が同一である場合。
```py
class Solution:
    def encode(self, s: str) -> str:
        freq = [0] * 26
        for ch in s:
            freq[ord(ch) - ord('a')] += 1
        return ''.join(chr(ord('a') + i) + str(freq[i]) for i in range(26))

    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        mp = defaultdict(list)
        for s in strs:
            mp[self.encode(s)].append(s)
        return list(mp.values())
```
- 時間計算量: $O(n × k)$
  - 各文字列に対して1回の線形探索
- 空間計算量: $O(n × k)$
  - すべての文字列をマップに格納

---

- 出典: https://leetcode.com/problems/group-anagrams/solutions/8332248/maang-interview-approach-brute-force-sor-iokl
  - どう見てもClaudeの出力

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.e5dwa7yj3tv0

- https://github.com/kazukiii/leetcode/pull/13
  - Python
  - 上記2通りの解法
  - `frozenset` で文字と数値の対応表を作成している
    - cf. https://docs.python.org/3/library/stdtypes.html#set-types-set-frozenset
    - `set` はミュータブルで、`frozenset` はその名の通りイミュータブル

- https://github.com/Fuminiton/LeetCode/pull/12
  - Python
  - 上記2通りの解法
  - 出現頻度の配列を度数分布であるから `histgram` というのはなるほどと思った
  - 頻度配列に小文字アルファベット以外が来ると何が起きるか？
    - > また Python では some_list[-1] のように負の index でも要素にアクセスできるので、このケースだと -26 ~ -1 の範囲であれば想定外の入力でもエラーが出ずに動いてしまい、思いがけないバグが起きそうです。（たとえば ^ が入った場合は ord('^') - ord('a') = -3 となるのであたかも 'x' かのように動いてしまう）
    - cf. https://github.com/quinn-sasha/leetcode/pull/15#discussion_r1969949923

## Step3
- 文字列をキーとしてソート
```py
class Solution:
    def groupAnagrams(self, strs: List[str]) -> List[List[str]]:
        sorted_strs = defaultdict(list)
        for s in strs:
            key = "".join(sorted(s))
            sorted_strs[key].append(s)
        return list(sorted_strs.values())
```
- 所要時間:
  - 1回目: 1:32
  - 2回目: 1:22
  - 3回目: 1:24

- 書いた後だけど `sorted_strs` は `strs_by_sorted_strs` とかの方がわかりやすいな
