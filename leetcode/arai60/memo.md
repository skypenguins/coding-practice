# 929. Unique Email Addresses
- 問題: https://leetcode.com/problems/unique-email-addresses/
- 言語: Python

## Step1
- メールアドレスのローカルパートとドメインパートを問題の条件通りに1文字ずつ走査し正規化する
- 事前条件として入力文字列の文字種はASCII範囲のアルファベット、数字、`+`、`.`、`@` を保証している
  - `@` は1つだけ
  - 事前条件のバリデーションは書かない方針
- 今回はメールアドレス文字列長が最大100文字なので、文字列追記 `+=` でも実行時間としては特に問題ないと判断
- ifの順番がセンシティブ

### 解答（AC）
```py
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        normalized_emails = set()

        for email in emails:
            normalized_email = ""
            has_plus_sign = False
            for i, s in enumerate(email):
                if s == "@":
                    normalized_email += email[i:]
                    break
                if has_plus_sign:
                    continue
                if s != "." and s != "+":
                    normalized_email += s
                    continue
                if s == ".":
                    continue
                if s == "+":
                    has_plus_sign = True
                    continue

            normalized_emails.add(normalized_email)
        
        print(normalized_emails)
        return len(normalized_emails)
```
- 解答時間: 20:14
- 時間計算量: $O(n^2)$

## Step2
- 典型コメント集: https://docs.google.com/document/d/11HV35ADPo9QxJOpJQ24FcZvtvioli770WWdZZDaLOfg/edit?tab=t.0#heading=h.h73uwlfq793n
- https://github.com/hayashi-ay/leetcode/pull/25
  - Python
  - ローカルパートとドメインパートをフラグで管理
  - 自分のコードでは `normalize` としたが `canonicalize` のほうが正確だと思った
  - 1文字ずつ走査していくのはステートマシン（状態機械）を作っていることと同じ
  - 文字列の追記は文字列の再構築が走る
    - > あ、あと、文字列の追記は文字列の再構築が走るので、(CPython は最適化されるみたいですが、)指摘されたらリストに append して join ですね。
      - cf. https://discord.com/channels/1084280443945353267/1200089668901937312/1210258224087961650
  - > https://github.com/python/cpython/blob/bb3e0c240bc60fe08d332ff5955d54197f79751c/Objects/unicodeobject.c#L11768
    > 文字列の追加 += が最適化される条件はここにあります。
    > 
    > 背景として、Python の文字列がイミュータブルであることは常識なので、このコードを見たら誰もが不安にかられるので、その不安感を共有できていますか、という質問です。
    > 
    > で、それに対して、「そう思っていたんですが実験した範囲では最適化が効くみたいです。」という返答は、なかなかに困って、というのも、次の疑問がわいてきます。「いつでもその最適化は行われるのか。インタープリターのバージョンに依存しないのか、たとえば、バージョンアップで最適化がなくなることはないのか。もしも、最適化がされることが保証されていないならば、そのように書いておいたとして、どういった場合に最適化されないのか。そのような仕様が仮にあるのだとしたら、そのドキュメントへのリンクをコメントで書いておいて欲しい。また、Python のバージョンが変わったときには、その最適化がされることが保証されていないならば、そのドキュメントをもう一回見て、バージョンによって仕様が変わっていないかどうかを確認するプロセスが必ず走るようにして欲しい。」
    > 
    > で、ここまでの疑問にその場で答えられるならば、面接官は、なるほど、そうなんですね、といって、Python に詳しい人だと思うでしょう。
    > 
    > で、仕様レベルで最適化が保証されているのだとしても、最低限コメントとして、「このような場合には最適化されることが保証される。どこどこ参照。」と書いておかないと、今後そのコードを読んでデバッグする人が、「むむ、もしかして、今回のタイムアウトの原因はここではないかな?」といって余計な実験をすることになるわけです。
    > 
    > つまり、「あ、Python の文字列はイミュータブルなので、こうしたほうがいいですね。」は減点なしの評価で、まあ、思わずやっちゃうことあるよね、くらいの感覚です。
「この場合は最適化されることが仕様レベルで保証されているのでその旨のコメントを書き足しましょうか。」も減点はしにくいけれども、それだったら join で書き直しませんか、という気分ですね。
    > つまり、気にしているのは、オーダーが正しく動くか、ではなくて、半年後に読んだ別の同僚が不安にならないか、環境の変化に対して頑健か、なのです。
    - cf. https://discord.com/channels/1084280443945353267/1200089668901937312/1210619083385479258
    - `join` なら $O(n)$ で済むので、`+=` を使うconsが思いつかず特にこだわりがないなら、こちらを使うべきだなと思った 
  - メールアドレスに関連するRFC
    - [RFC 1034](https://www.ietf.org/rfc/rfc1034.txt#:~:text=The%20labels%20must%20follow%20the%20rules%20for%20ARPANET%20host%20names.%20%20They%20must%0Astart%20with%20a%20letter%2C%20end%20with%20a%20letter%20or%20digit%2C%20and%20have%20as%20interior%0Acharacters%20only%20letters%2C%20digits%2C%20and%20hyphen.%20%20There%20are%20also%20some%0Arestrictions%20on%20the%20length.%20%20Labels%20must%20be%2063%20characters%20or%20less)
    - [RFC 5322](https://datatracker.ietf.org/doc/html/rfc5322#:~:text=local-part%20%20%20%20%20%20%3D%20%20%20dot-atom%20/%20quoted-string%20/%20obs-local-part)
      - メールアドレス文字列の仕様は 3.4.1. Addr-Spec Specification のあたり
  - > maxsplit rsplit はご存知ですか?
    - `rsplit` は知っていたが、`maxsplit` は知らなかった
    - cf. https://docs.python.org/3.12/library/stdtypes.html#str.split
  - > RFCを読むとlocal-partsに@マークが使える（ダブルクオートで書こうと）ので、今のコードだと有効なメールアドレスでも誤判定してしまいます（他にももっと対応できてないケースがありますが）
    > 
    > email.rsplit("@", maxsplit=1)で分割してあげれば@マークが最低1つ存在する場合については確実に有効なドメインが取得できそうです。
      - cf. https://discord.com/channels/1084280443945353267/1200089668901937312/1209416153982697492
  - 他には正規表現を使った方法
    - cf. https://docs.python.org/ja/3/library/re.html#re.sub

- https://github.com/colorbox/leetcode/pull/28
  - C++
  - コピーするか？しないか？の選択肢の判断

- https://github.com/seal-azarashi/leetcode/pull/14
  - Java
  - > いや、全部正規表現にしなくてもよいですよね。元のループを回すコードは要するにステートマシンを書いています。
    > しかし、そうではなくて、いくつかの関数の組み合わせで書けるはずです。
    >
    > 1. @ の前後で分ける
    > 2. 前半に対して、+ の前までを取る
    > 3. 前半から . を取り除く
    > 4. 結合する
      - cf. https://github.com/seal-azarashi/leetcode/pull/14/files#r1677225649

- https://github.com/t0hsumi/leetcode/pull/14
  - Python
  - setの内包表記
    - cf. https://github.com/t0hsumi/leetcode/pull/14/files#r1929803127

- https://github.com/plushn/SWE-Arai60/pull/14
  - Python
  - RFCでのメールアドレス最大文字列帳は254文字
    - cf. https://github.com/plushn/SWE-Arai60/pull/14/files#r2052171339

### 読みやすく書き直したコード
```py
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def canonicalize(email: str) -> str:
            local_part, domain_part = email.split("@")
            local_part = local_part.split("+")[0]
            local_part = local_part.replace(".", "")
            return local_part + "@" + domain_part
        
        unique_emails = set()
        for email in emails:
            unique_emails.add(canonicalize(email))
        
        return len(unique_emails)
```

## Step3
```py
class Solution:
    def numUniqueEmails(self, emails: List[str]) -> int:
        def canonicalize(email: str) -> str:
            local_part, domain_part = email.split("@")
            local_part = local_part.split("+")[0]
            local_part = local_part.replace(".", "")
            return local_part + "@" + domain_part
        
        unique_emails = set()
        for email in emails:
            unique_emails.add(canonicalize(email))
        
        return len(unique_emails)
```
- 解答時間
  - 1回目: 2:46
  - 2回目: 2:42
  - 3回目: 2:48
