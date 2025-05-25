# 387. First Unique Character in a String
* 問題リンク: https://leetcode.com/problems/first-unique-character-in-a-string/
* 使用言語: Python3

# Step1
* 文字とインデックスのdictを作成
  - 重複する文字の場合、valueに`-1`を格納
* `-1`を除外してインデックスの最小値を取得

```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        first_index = -1
        indices = []
        char_dict = {}

        for idx in range(len(s)):
            if s[idx] in char_dict.keys():
                char_dict[s[idx]] = -1
            else:
                char_dict[s[idx]] = idx
        
        for c, i in char_dict.items():
            if i != -1:
                indices.append(i)
        
        if len(indices) != 0:
            first_index = min(indices)
        
        return first_index
```

* 実行時間: 75ms
* メモリ使用量: 18.14MB
* 時間計算量: $O(n)$
* 空間計算量: $O(k)$
* 解答時間: 33:13

# Step2
## 他の方のコードを読む
* https://github.com/aiueoriku/LeetCode/pull/5/files/dc9d19a8b17460f7ad03205be1f6b202fa458a39
  - スライスを使った方法
  - Collections.Counterで文字ごとの出現回数を数える方法
    - Counterは他の言語ではBagやMultiSet（多重集合）と呼ばれる
    - PythonのCounterの内部実装: https://github.com/python/cpython/blob/a66bae8bb52721ea597ade6222f83876f9e939ba/Lib/collections/__init__.py#L551
      - dictを使っている
      - Cヘルパーの`_count_elements` があればimportされる
    - defaultdictは初めて知った
      - https://docs.python.org/3/library/collections.html#collections.defaultdict
      - 初期化した時にKeyErrorの場合のデフォルトの挙動を指定できる
* https://github.com/shintaroyoshida20/leetcode/pull/21/files#diff-d41cb302e6cff5e686751a0ac9ec03c7feec65dfe1767e33bb178b7e5995596d
  - Mapオブジェクト（JavaScript）で文字ごとの出現回数を数える方法
* https://github.com/hayashi-ay/leetcode/pull/28/files#diff-5ec7c3c87171edf4d61e9eb79fd926cafa27caf068da7474222897c8e9e7ab96
  - 同じくCounterを使った方法
  - 今回はASCII範囲の英数字だけだが、UTF-8の範囲の文字を対象とする場合、考えることが増えそうだなと思った

* 出現回数を数えて、重複していない文字＝1回のみ出現する文字のインデックスを返す発想が主流
  - 他には総当たり法、キューを使った方法
* 出現回数を数える方法で書き直す
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_freq = Counter(s)
        for i, c in enumerate(s):
            if char_freq[c] == 1:
                return i
        return -1

```

* 実行時間: 56ms
* メモリ使用量: 18.24MB

# Step3
```python
class Solution:
    def firstUniqChar(self, s: str) -> int:
        char_freq = Counter(s)
        for i, c in enumerate(s):
            if char_freq[c] == 1:
                return i
        return -1
```
* 解答時間
  - 1回目: 0:59
  - 2回目: 1:01
  - 3回目: 0:55
