# 349. Intersection of Two Arrays
* 問題: https://leetcode.com/problems/intersection-of-two-arrays/
* Python

## Step1
* 入力の長さが最大1000なので、素直な二重ループ $O(n^2)$ でACすると判断
  - $約10^8 回/秒$ として、

$$
\frac{(10^3)^2}{10^8} = 10^{-2}
$$

* 約0.01秒
* 集合は（おなじみ） `set` を使う

### 解答（AC）
```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        result = set()
        
        for num1 in nums1:
            for num2 in nums2:
                if num1 == num2:
                    result.add(num1)
        
        return list(result)
```
* 解答時間: 1:51
* 時間計算量: $O(n^2 + n)$
  - 二重ループ＋listへの変換
* 空間計算量: $O(2n)$

## Step2
* 典型コメント: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.o0jquy48e6cy
* https://discord.com/channels/1084280443945353267/1183683738635346001/1188897668827730010
  - C++
  - 2つの配列をソートし、2つのポインタが指す要素が同じならそれを別の配列に追加していく方法
  - 共通部分（intersection）以外はポインタをインクリメントして読み飛ばしていく
    - > 両方の頭にポインターを持って、小さい方のポインターをインクリメントして、値が同じになったら、それを出力、そして、その数字の間は捨てる。
      - cf. https://discord.com/channels/1084280443945353267/1183683738635346001/1188159345792393397
  - ```cpp
    class Solution {
        public:
            vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
                sort(nums1.begin(), nums1.end());
                sort(nums2.begin(), nums2.end());

                auto nums1Pointer = nums1.begin();
                auto nums2Pointer = nums2.begin();
                vector<int> ans;
                while (nums1Pointer != nums1.end() && nums2Pointer != nums2.end()) {
                    if (*nums1Pointer < *nums2Pointer) {
                        ++nums1Pointer;
                        continue;
                    }
                    if (*nums2Pointer < *nums1Pointer) {
                        ++nums2Pointer;
                        continue;
                    }
                    int common = *nums1Pointer;
                    ans.push_back(common);
                    while (nums1Pointer != nums1.end() && common == *nums1Pointer) {
                        ++nums1Pointer;
                    }
                    while (nums2Pointer != nums2.end() && common == *nums2Pointer) {
                        ++nums2Pointer;
                    }
                }            
                return ans;
            }
    };
    ```
* https://github.com/shining-ai/leetcode/pull/13
  - Python
  - set型同士で共通部分を取る方法
    - cf. https://docs.python.org/ja/3/library/stdtypes.html#set-types-set-frozenset
  - ```py
    class Solution:
        def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
            nums1_set = set(nums1)
            nums2_set = set(nums2)
            return list(nums1_set & nums2_set)
    ```
    - CPythonの実装では `&` 演算子をオーバーロードしている
        - cf. https://github.com/python/cpython/blob/bd4be5e67de5f31e9336ba0fdcd545e88d70b954/Lib/_collections_abc.py#L606
  - ソートを使った方法
    - > ```py
      > if nums1_sorted[i] == nums2_sorted[j]:
      > ```
      > これは書きません。
      - なぜ？と思ったが、こちらのパターンだとelse ifの嵐となり、continueによる早期returnができなくなることで読みにくくなる（脳内のワーキングメモリを長く占有する）から？
      - あとは、ふつうは共通部分でない要素が圧倒的に多いので、プロセッサの分岐予測が効きやすくなる？
    -  cf. https://discord.com/channels/1084280443945353267/1201211204547383386/1208701087264280596
    - 2つのソート済み配列を処理するイディオムは標準的なマージアルゴリズムのイディオム
* https://github.com/tarinaihitori/leetcode/pull/13
  - Python
  - Pythonには標準でメソッド `intersection()` が用意されている
    - cf. https://docs.python.org/ja/3/library/stdtypes.html#frozenset.intersection

### 読みやすく書き直したコード
```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```

## Step3
```py
class Solution:
    def intersection(self, nums1: List[int], nums2: List[int]) -> List[int]:
        return list(set(nums1) & set(nums2))
```
* 解答時間:
  - 1回目: 0:18
  - 2回目: 0:22
  - 3回目: 0:20
