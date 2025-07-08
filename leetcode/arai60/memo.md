# 20. Valid Parentheses
問題リンク: https://leetcode.com/problems/valid-parentheses/
言語: Python

# Step1
* 訪れた文字をスタックに積む
* 訪れた文字が閉カッコの場合かつ、ひとつ前の文字が対応する開カッコの場合、スタックから2回popする

```python
class Solution:
    def isValid(self, s: str) -> bool:
        if len(s) <= 1:
            return False
        
        visited_chars = deque()
        
        for c in s:
            visited_chars.append(c)
            if len(visited_chars) != 1:
                if c == ")" and visited_chars[-2] == "(":
                    visited_chars.pop()
                    visited_chars.pop()
                if c == "]" and visited_chars[-2] == "[":
                    visited_chars.pop()
                    visited_chars.pop()
                if c == "}" and visited_chars[-2] == "{":
                    visited_chars.pop()
                    visited_chars.pop()
        
        if len(visited_chars) == 0:
            return True
        else:
            return False
```
* 解答時間: 1:03:46
  - 途中でスタックを使った方法に気づいてからは15分くらい
  - 後ろから2番目を参照する方法は `visited_chars` のサイズを事前に検証しないといけないのであまり綺麗ではないと思った

# Step2
## 他人のコードを読む
* https://github.com/maeken4/Arai60/pull/6
  - C++
  - スタックを使ったアルゴリズム
    - 事前にカッコの対応関係（キー:開カッコ、値:閉カッコ）を辞書で持っておく
      - 今後のカッコの種類の変更に対応しやすくなる意味でも良いと思った
    - 自分の場合は両方のカッコを同時にスタックに積んでいたが、これは開カッコに対応する**閉カッコ**のみをスタックに積む方法
  - 文脈自由文法（プッシュダウンオートマトン）で文法を定義できる
    - `S → (S) | SS | ε`
    - コンパイラの字句解析そのものか…

* https://github.com/ryosuketc/leetcode_arai60/pull/6
  - Python
  - カッコの対応関係（キー:閉カッコ、値:開カッコ）を辞書で持っておく
    - アルゴリズムは同じ
  - 変数名がわかりやすい

## 読みやすく直したコード
```python
class Solution:
    def isValid(self, s: str) -> bool:
        closing_to_opening_brackets = {")": "(", "]": "[", "}": "{"}
        unmatched_open_brackets = deque()

        for bracket in s:
            if bracket in closing_to_opening_brackets:
                if not unmatched_open_brackets:
                    return False
                actual_bracket = unmatched_open_brackets.pop()
                expected_bracket = closing_to_opening_brackets[bracket]
                if actual_bracket != expected_bracket:
                    return False
            else:
                unmatched_open_brackets.append(bracket)
        
        return not unmatched_open_brackets
```

# Step3
```python
class Solution:
    def isValid(self, s: str) -> bool:
        closing_to_opening_bracket = {")": "(", "]": "[", "}": "{"}
        unmatched_brackets = deque()

        for bracket in s:
            if bracket in closing_to_opening_bracket:
                if not unmatched_brackets:
                    return False
                actual_bracket = unmatched_brackets.pop()
                expected_bracket = closing_to_opening_bracket[bracket]
                if actual_bracket != expected_bracket:
                    return False
            else:
                unmatched_brackets.append(bracket)
            
        return not unmatched_brackets
```
* 解答時間
  - 1回目: 3:44
  - 2回目: 4:27
  - 3回目: 3:51
