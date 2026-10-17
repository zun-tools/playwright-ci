# playwright-ci

YouTube「ずんだもんの実験道具箱」の動画「Playwright 再入門 3」（Playwright と GitHub Actions）で使ったサンプルです。動画と同じ中身を保つため、公開後は書き換えません。

買い物メモの 1 画面（`index.html`）と、Playwright のテスト 1 本（`tests/shopping.spec.js`）。履歴は動画と同じ 4 コミットです（買い物メモとテスト1本 → workflow を足す → ボタン名を変える → ボタン名を戻す）。

## 自分の GitHub で、push のたびにテストを走らせる

```bash
git clone https://github.com/zun-tools/playwright-ci.git
cd playwright-ci
npm ci
npx playwright install chromium
npx playwright test                       # 手元で緑になる
```

自分の GitHub に空の公開リポを作って、そこへ push します（ブラウザで空のリポを作ってもかまいません）。

```bash
gh repo create <自分の名前>/playwright-ci --public
git remote set-url origin https://github.com/<自分の名前>/playwright-ci.git
git push
```

自分のリポの Actions のタブを開くと、実行が 1 つ走って緑になります（4 コミットをまとめて送るので、実行は一番新しいコミットの 1 回です）。

## ボタン名を変えて、何も打たずに赤くする

`index.html` のボタン名「追加」を「入れる」に変えて push します。

```bash
git commit -am "ボタン名を変える"
git push
```

テストのコマンドは打っていませんが、Actions のタブで約 2 分後に ✗ が付きます。

## レポートを落としてトレースを開く

```bash
gh run list                                              # 実行の番号を見る
gh run view <実行の番号> --log-failed                    # 失敗した段のログ
gh run download <実行の番号> -n playwright-report -D ci-report
npx playwright show-report ci-report
```

レポートの失敗したテストを開き、再試行 1 回目（Retry #1）のタブからトレースを開きます。`gh` を入れない場合は、実行ページの Artifacts の欄の `playwright-report` からも落とせます（GitHub にログインした状態で）。`gh` をはじめて使うときは先に `gh auth login`（[公式の手順](https://cli.github.com/manual/gh_auth_login)）が要ります。

## つまずいたら

- push が workflow のファイルを理由に拒まれたら、GitHub のトークンに `workflow` の権限が要ります（`gh auth refresh -s workflow` など。うちでは試していません）。
- clone したあとの `git commit` が `Author identity unknown` で拒まれたら、そのフォルダで `git config user.name "<名前>"` と `git config user.email "<メール>"` を設定します。
- `gh run download` が `would result in path traversal` で失敗したら、`-D ci-report` のように落とし先のフォルダを付けます。

## 試した環境

2026 年 10 月・Linux（arm64）・Node.js 22・Playwright 1.64.0・GitHub の `ubuntu-latest`。Mac と Windows の手元は確かめていません。

## ライセンス

MIT
