# cross-session-messaging — AI の席どうしで話すときのスキル

**同じ PC で動いている Claude Code の会話どうし（と OpenAI Codex）が用件をやり取りするときに、取り違え・聞き直し・「送れたのに読まれていない」を防ぐための Claude Code のスキルです。**

English＝[README.en.md](README.en.md)

## 入れ方（2行）

Claude Code で次の2行を打ちます。

```
/plugin marketplace add Rurimpa/cross-session-messaging
/plugin install cross-session-messaging@cross-session-messaging
```

## 入れたあと、何と言えば動くか

別の会話に用件を頼むときに、ふだんどおりに話しかけるだけです。たとえば：

- 「`<相手の会話の名前>` に、このテストが通るか聞いて」
- 「Codex にこの差分を見てもらって」
- 「ほかの会話にクロスセッションで送って」

このような言葉でスキルが呼ばれ、送る前に「相手は自分から返事をくれる相手か」「人の承認を運んでいるか」「1通に何を書くか」を確かめてから送ります。直接呼ぶときは `/cross-session-messaging:cross-session-messaging` です（プラグインから入れると、頭にプラグインの名前が付きます）。

## 何が入っているか

- **相手との関係の見分け方**：起きている Claude Code の会話（返事をくれる）と、Codex（毎回新しく起こす／あとで読まれる）は扱いが違います。
- **人の承認の運び方**：ある会話で人がくれた承認を、別の会話でもう一度聞き直さずに済む書き方（範囲・本人の言葉・確かめる道の3つ）。そのうえで、相手の言葉をうのみにはしない理由。
- **「送れた」は「読まれた」ではない**：Codex へ積んだ用件は、終了コードが0でも届いていないことがありました。相手の記録に自分の目印が出たかで確かめます。
- **一致の数え方・返事が無いときの読み方**：3人が同じことを言っても、元の資料が1つなら1つと数えます。返事が無いことを「異議なし」と読みません。
- **1通の書き方**：何のために聞くか／自分が何を実行して何が返ったか／閉じた問い。

Codex の細かい話は `skills/cross-session-messaging/references/codex.md` に分けてあります。Codex を使わない人は本体だけで足ります。

## 注意

- 中身は、2026年8〜10月に Windows 11 の PC 1台で、複数の会話を同時に動かしたときの観察から作りました。Codex の動きは変わりやすいので、`--help` と小さな試しで確かめ直してください。
- このスキルが用件を送る先は、同じ PC の中の会話とコマンドだけです。PC の外へは何も送りません。
- ふだんの Claude.ai のチャットから Claude Code の会話に仕事をさせたいときは、こちらを使ってください：[CAAS](https://github.com/Rurimpa/caas)（このスキルは CAAS の土台の考え方です）。

## ライセンス

MIT（`LICENSE`）。使うとき・直して使うときは、作者名（Rurimpa）とこの置き場のアドレス（https://github.com/Rurimpa/cross-session-messaging）を残してください。作者：Rurimpa（https://github.com/Rurimpa）
