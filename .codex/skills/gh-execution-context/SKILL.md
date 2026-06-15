---
name: gh-execution-context
description: "Codexサンドボックス内でGitHub CLI `gh` の認証やAPI接続が壊れて見えるときに使う。`gh auth status` 失敗、`token is invalid`、権限付き実行、再ログイン要否、`gh auth refresh` の切り分け依頼で使う。"
---

# Gh Execution Context

Codexサンドボックス内でGitHub CLI `gh` が失敗したとき、認証切れ、ネットワーク制限、実行環境差分を切り分けるためのSkillです。`gh auth status` の1行だけで認証不備と判断せず、実API疎通と実行権限を確認します。

## 使うべき状況

- `gh auth status` が `token is invalid` や認証失敗を返す。
- `gh` が `error connecting to api.github.com` やAPI接続失敗を返す。
- Codex内では `gh` が失敗するが、ユーザーの通常シェルではGitHub操作できそうに見える。
- GitHub操作のために、対象の `gh` コマンドを通常ネットワーク権限付きで再実行すべきか判断したい。
- 不要な `gh auth login` や認証更新を避けたい。

## 期待する入力

- 失敗した `gh` コマンド全文。
- エラーメッセージ全文。ただしトークンや秘密情報は共有しない。
- 対象の `OWNER/REPO`、Issue番号、PR番号、run IDなど。
- その操作が読み取りか書き込みか。

## 判断基準

- `gh auth status` の `token is invalid` を単独で認証切れ扱いしません。
- `error connecting to api.github.com` は、まずCodexサンドボックスのネットワーク制限として扱います。
- 認証状態は `gh auth status` だけでなく、`gh api user`、`gh repo view OWNER/REPO`、`gh pr view <number> --repo OWNER/REPO` など、目的に近い実API疎通で確認します。
- 権限付き実行で `gh api user` が通り、ユーザーが `ainoobmanpg` と確認できる場合は、`gh auth login` を実行しません。
- GitHub操作が必要で通常実行だけが失敗した場合は、失敗した対象の `gh` コマンドだけを通常ネットワーク権限付きで再実行します。
- 権限付き実行でも実APIが認証エラーを返す場合だけ、本当の認証不備として扱います。

## 実行手順

1. 失敗したコマンドとエラー文を確認し、`token is invalid`、`api.github.com` 接続失敗、403/404/権限不足を分けます。
2. `error connecting to api.github.com` の場合は、認証更新ではなくネットワーク権限の問題として扱います。
3. 読み取り確認なら `gh api user` と `gh repo view OWNER/REPO` を、PR関連なら `gh pr view <number> --repo OWNER/REPO` を実API疎通として使います。
4. 通常実行で接続に失敗し、GitHub操作が必要な場合は、対象の `gh` コマンドだけを通常ネットワーク権限付きで再実行します。
5. 権限付き実行で `gh api user` が `ainoobmanpg` を返したら、認証は使える状態として扱い、`gh auth login` は実行しません。
6. 権限付き実行でも認証エラーが続く場合だけ、ユーザーに見えるターミナルで次を案内します。

```bash
gh auth refresh -h github.com -s repo -s workflow -s read:org
```

## 出力形式

- 判定。例: `Codexサンドボックスのネットワーク制限`, `権限付き実行で疎通確認済み`, `本当の認証更新が必要`。
- 確認した実APIコマンドと結果の要約。
- 次に実行すべき対象 `gh` コマンド。書き込み操作なら対象repo、Issue/PR、変更内容も明記します。
- 認証更新が必要な場合だけ、ユーザーに見えるターミナルで実行する `gh auth refresh` コマンド。

## 禁止事項と確認が必要な操作

- `gh auth status` の失敗だけで `gh auth login` を実行しません。
- 権限付き実行で `gh api user` が通っている状態で、再ログインや認証更新を案内しません。
- トークン、認証済みセッション、keyringの中身を表示しません。
- 対象repoや操作内容が曖昧なまま、書き込み系の `gh` コマンドを権限付きで再実行しません。
- `gh auth refresh` は本当に認証更新が必要な場合だけ案内し、Codex内で不可視に実行しません。
