# 3. Longest Substring Without Repeating Characters
* 問題リンク: https://leetcode.com/problems/longest-substring-without-repeating-characters/
* 使用言語: Python3

## Step1
* Set型で重複しない文字の集合を取得しようと思ったが、subsequenceになってしまうのでこれは使えない
* 方針
  1. 文字列sの先頭から1文字ずつ末尾に向かってポインタを動かしていく、1文字ずつsubstringに追加
  2. ポインタの指す1文字がsubstringに含まれていたら、ポインタを1つ戻し、substringをクリアする

### 以下は動かないコード
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        substring_length = 0
        substring = ''
        tail = 0
        while tail != len(s)-1:
            substring_length = max(substring_length, len(substring))
            substring += s[tail]
            tail += 1
            if s[tail] in substring:
                tail -= 1
                substring = s[tail]
        return substring_length
````

* 実行時間: TLE

* いつまで経っても `tail` == `len(s)-1` とならないため、TLEとなった
  - 上記原因が分からず、1時間経っていたため答えを見た

### 以下は動くコード
```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left_i  = 0
        max_substring_length = 0
        char_set = set()

        for right_i in range(len(s)):
            while s[right_i] in char_set:
                char_set.remove(s[left_i])
                left_i += 1
            
            char_set.add(s[right_i])
            max_substring_length = max(max_substring_length, right_i - left_i + 1)
        
        return max_substring_length
```

* 実行時間: 19ms
* メモリ使用量: 17.78MB
* 時間計算量: O(n)
* 空間計算量: O(1)
* 解答時間: 5:29

* 自分の考えていたアルゴリズムに近いが、`tail` を戻す考え方ではなく先頭（左側）を指すポインタを導入する考え方
* 答えでは Set型を使っていたが、和集合の目的でSet型を使っていたわけではなく単なるハッシュテーブルとして使っている
  - Set型はCPythonでは順序は保証されていないっぽいのでListが望ましい？
    - cf. https://stackoverflow.com/a/61467874

## Step2
### 参考にした方々
* https://github.com/fuga-98/arai60/pull/47/files/5bb14a8b628a0edf65223ebef947b3a87d745767
  - `s == 0`の場合は特に考えていなかった
  - keyに文字、valueにインデックスを持つDictを作成し、そのDictで重複文字が存在するか判定
* https://github.com/olsen-blue/Arai60/pull/49/files
  - 同じアルゴリズム。動的計画法の一種でSliding Window（スライディングウィンドウ、尺取法）と呼ぶらしい
  - `seen_char` の方がより実態を表していると思った

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left_i = 0
        max_substring_length = 0
        seen_char_set = set()

        for right_i in range(len(s)):
            while s[right_i] in seen_char_set:
                seen_char_set.remove(s[left_i])
                left_i += 1
            
            seen_char_set.add(s[right_i])
            max_substring_length = max(max_substring_length, right_i - left_i + 1)
        
        return max_substring_length
```
* 解答時間: 3:01

# Step3

```python
class Solution:
    def lengthOfLongestSubstring(self, s: str) -> int:
        left_i = 0
        max_substring_length = 0
        seen_char_set = set()

        for right_i in range(len(s)):
            while s[right_i] in seen_char_set:
                seen_char_set.remove(s[left_i])
                left_i += 1

            seen_char_set.add(s[right_i])
            max_substring_length = max(max_substring_length, right_i - left_i + 1)

        return max_substring_length
```
* 解答時間
  - 1回目: 3:12
  - 2回目: 3:12
  - 3回目: 4:14
