# 39. Combination Sum
問題: https://leetcode.com/problems/combination-sum/
言語: Python

## Step1
* 与えられた配列の要素の数値を使って和を作り、 `target` の数値になる数値の組み合わせの配列を返す。数値は何度でも使ってよい。
* [31. Next Permutation](https://leetcode.com/problems/next-permutation/) と考え方は似ている
* 数値が1度だけしか使えないなら総当たりで組み合わせを調べれば（ `target` の数値から最大値から順に引いていく）よいので比較的簡単だと思ったが、重複を許すので良いバックトラッキングの方法がパッと思いつかないため正答を見た。

### 正答
```py
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        result = []
        candidates.sort()

        def backtrack(start, target, path):
            if target == 0:
                result.append(path)
                return
            
            if target < 0:
                return
            
            for i in range(start, len(candidates)):
                backtrack(i, target - candidates[i], path + [candidates[i]])
        
        backtrack(0, target, [])
        return result
```
- `backtrack()` が再帰するときに `i` をインクリメントしないことで重複を許す
- 深さ優先で、 `path` に1つずつ選択を積み重ねる
- ```
  [] → [2] → [2,2] → [2,2,2]
          → [2,3]
     → [3] → [3,3]
  ```
- 時間計算量: O(N^(T/M))
  - N: `candidates` の長さ
  - T: `target` の値
  - M: `candidates` の最小値

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.vo5b5dbpqng

1. https://github.com/fhiyo/leetcode/pull/52
    - Python
        1. バックトラッキング + yieldを使った方法、引いていくのではなく足していく
        2. 全部スキップして最深部へ到達してから戻る方法（深さ優先で、各数値の使用回数を一気に試す）
        - ```py
            class Solution:
                def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
                    def generate_combinations(combination: list[int], total: int, index: int) -> Iterator[list[int]]:
                        if index == len(candidates):
                            return
                        yield from generate_combinations(combination, total, index + 1)
                        if total + candidates[index] == target:
                            yield combination + [candidates[index]]
                            return
                        if total + candidates[index] < target:
                            yield from generate_combinations(combination + [candidates[index]], total + candidates[index], index)

                    return list(generate_combinations([], 0, 0))
            ```
        3. 2. のスタック版
        - ```py
            class Solution:
                def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
                    candidates.sort()
                    all_combinations = []
                    stack = [([], 0, 0)]
                    while stack:
                        combination, total, start = stack.pop()
                        if total == target:
                            all_combinations.append(combination)
                        for i in range(start, len(candidates)):
                            if total + candidates[i] > target:
                                break
                            stack.append((combination + [candidates[i]], total + candidates[i], i))
                    return all_combinations
            ```
    - 今回のバックトラッキング戦略の考え方
        - > この問題ですが、基本的にはバックトラックをするわけですが、
          > 分類してみると、いくつかあるように思います。
          > 
          > [A, A] まで使うことが確定していて B 以降しか使ってはいけないという状況下で、
          > 
          > B を一つ使うか、C 以降しか使ってはいけないか、に分岐する。
          > B の使う数を列挙して分岐し、C 以降しか使ってはいけないに遷移する。
          > 次の1個が、B, C, D, E, F... である場合に分岐する。
          > 
          > あたりのように思っています。
          > とにかく、抜け漏れなく分類ができれば、いいということでしょうか。
            - cf. https://github.com/fhiyo/leetcode/pull/52/files#r1690161771
        - パターン1: 
            - ```
              [A,A]
              ├─ [A,A,B] ← Bを1個使う
              │   ├─ [A,A,B,B] ← さらにBを1個
              │   └─ [A,A,B,C] ← Bをスキップ
              └─ [A,A,C] ← Bをスキップ
              ```
        - パターン2: 
            - ```
              [A,A]
              ├─ [A,A] → C以降へ（B×0）
              ├─ [A,A,B] → C以降へ（B×1）
              ├─ [A,A,B,B] → C以降へ（B×2）
              └─ [A,A,B,B,B] → C以降へ（B×3）
              ```
        - パターン3: 
            - ```
              [A,A]
              ├─ [A,A,B] ← Bを選ぶ
              │   ├─ [A,A,B,B] ← 再びBを選ぶ
              │   ├─ [A,A,B,C] ← Cを選ぶ
              │   └─ [A,A,B,D] ← Dを選ぶ
              ├─ [A,A,C] ← Cを選ぶ
              └─ [A,A,D] ← Dを選ぶ
              ```

2. https://github.com/hayashi-ay/leetcode/pull/65
    - Python
    - 上記のパターン1、パターン2、パターン3の方針
    - パターン1、パターン2について
      - 先にスキップ＝先に `index + 1` で再帰して、何もせずに配列の最後まで到達したら戻りながら試すパターンは認知負荷が高い
      - 後でスキップ（先に使う）＝ヘルパー関数の最後で `index + 1` で再帰して、構築しながら進むパターンのほうが直観的でわかりやすい
    - パターン3が一番直観的でコード量が少ない

### 読みやすく書き直したコード
```py
class Solution:
    def combinationSum(self, candidates: List[int], target: int) -> List[List[int]]:
        candidates.sort()
        all_combinations = []
        stack = [([], 0, 0)]
        while stack:
            combination, total, start = stack.pop()
            if total == target:
                all_combinations.append(combination)
            for i in range(start, len(candidates)):
                if total + candidates[i] > target:
                    break
                stack.append((combination + [candidates[i]], total + candidates[i], i))
        return all_combinations
```
- 所要時間
  - 1回目: 3:21
  - 2回目: 3:21
  - 3回目: 3:20
