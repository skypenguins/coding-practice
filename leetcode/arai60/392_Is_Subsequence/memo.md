# 392. Is Subsequence
* 問題リンク: https://leetcode.com/problems/is-subsequence/
* 言語: Python3

# Step1
## 解答の方針
* `t` の文字とインデックスの位置を保持するdict `key_to_index` を作成し、 `s` の先頭の文字から走査してインデックスがすべて小＜大の順序を満たすかどうかを調べる方針

## すべてのテストケースを通過しないコード
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        key_to_index = collections.OrderedDict()
        for i, char_t in enumerate(t):
            if key_to_index.get(char_t) is not None:
                key_to_index.get(char_t).append(i)
            else:
                key_to_index[char_t] = [i]
        
        print(key_to_index)
        
        exists_char_i = []
        for char_s in s:
            if key_to_index.get(char_s) is None:
                return False
            elif len(key_to_index[char_s]) == 0:
                return False
            exists_char_i.append(key_to_index[char_s].pop(0))
        
        for i in range(len(exists_char_i) - 1):
            if exists_char_i[i] >= exists_char_i[i + 1]:
                return False
        
        return True
```

* しかし、`s` = `"ab"` 、 `t` = `"baab"` の場合、`OrderedDict([('b', [0, 3]), ('a', [1, 2])])` となって、誤って `False` を返す
* `s` のすべてのインデックスの順序を取得するアルゴリズムが思い浮かばず、正答を見た

## 正答
* `s` を基準に先頭の文字から調べていくのではなく、`t` を基準にして先頭から文字を取得し、取得した文字が `s` に存在すれば、検索中のSubsequenceの長さをインクリメントし、最終的に検索中のSubsequenceの長さと `s` の長さが一致するか判定

```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        if len(s) > len(t):
            return False
        if len(s) == 0:
            return True
        
        subseq_i = 0
        for t_i in range(0, len(t)):
            if subseq_i < len(s):
                if t[t_i] == s[subseq_i]:
                    subseq_i += 1
        
        return subseq_i == len(s)
```

* 実行時間: 0ms
* メモリ使用量: 18.04MB
* 時間計算量: $O(n)$
* 空間計算量: $O(1)$

# Step2
## 他の方のコードを読む
* https://github.com/fuga-98/arai60/blob/27af9937ef75a7565d13d27e79853dd0f93fa070/392.%20Is%20Subsequence.md#step1
  - ループの途中でreturnして、なるべく早く制御を戻している
* https://github.com/olsen-blue/Arai60/blob/0aac26e68fa6b8f516ff494da34578b8cc1cefc2/392.%20Is%20Subsequence.md#%E8%A7%A3%E6%B3%952%E4%BB%95%E4%BA%8B%E3%81%AE%E5%BC%95%E3%81%8D%E7%B6%99%E3%81%8E%E3%83%88%E3%83%83%E3%83%97%E3%83%80%E3%82%A6%E3%83%B3%E5%86%8D%E5%B8%B0dpac
  - 解法2は再帰を使った方法。視線誘導がすっきりしていて読みやすい。スタックオーバーフローにならないか気になった。
  - > while に掲げるものは主役であることが多いですね。
    - これはもっともだと思った
* https://github.com/fhiyo/leetcode/blob/3bdbb38c39f00eba0213b5fb82d1cddd75e55537/392_is-subsequence.md
  - ①はエッジケースがループに自然に組み込まれていていて読みやすい
  - ②の正規表現を使う方法は面白いと思った
  - ③は自分が当初やろうとしていた方針に近いかも。defaultdictを使ってリスト型で初期化し、リストを二分探索すればよかったのか…パズルに近いかも？
  - ④は `find()` を使っているが、時間計算量が①と同じなのが意外だった
* https://github.com/nittoco/leetcode/pull/16/files#diff-4213dd9e9bb2e47ed3241ed4250c36f5193149962dd86c6163c7d6b52b8b2668R71-R82
  - `while True`で無限ループで解く方法

## 読みやすく整え直したコード
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        idx = 0
        for char_t in t:
            if idx == len(s):
                return True
            if s[idx] == char_t:
                idx += 1
        return idx == len(s)
```
* 実行時間: 3ms
* メモリ使用量: 17.92MB
* 時間計算量: $O(n+m)$
* 空間計算量: $O(1)$

# Step3
```python
class Solution:
    def isSubsequence(self, s: str, t: str) -> bool:
        idx = 0
        for char_t in t:
            if idx == len(s):
                return True
            if s[idx] == char_t:
                idx += 1
        return idx == len(s)
```

* 解答時間
  - 1回目: 1:05
  - 2回目: 1:00
  - 3回目: 1:03
