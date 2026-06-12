---
name: github-pr-check-review-comments
description: GitHub Pull Requestのreviewコメントや既存review状態を確認するときに使う。
---

# GitHub Pull Request Check Review Comments

GitHub Pull Requestのreviewコメントや既存review状態を、変更せずに確認するためのSkillです。GitHub CLI `gh` を主経路にし、必要な範囲だけ `gh api` を補助的に使いながら、reviewコメント、review event、未解決の論点候補を読み取り専用で整理します。

## 使うべき状況

- 既存reviewコメントを確認したい。
- 誰がどのreview eventを送っているか確認したい。
- 返信や修正前にreview状況の前提を把握したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 確認したい項目。例: reviewコメント、review event、未解決の論点候補。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments` または `--json reviews,reviewDecision` で既存reviewの概要を読みます。
4. reviewコメントの粒度が必要なら、必要最小限で `gh api repos/OWNER/REPO/pulls/<number>/reviews` や `gh api repos/OWNER/REPO/pulls/<number>/comments` を使って追加取得します。
5. 取得結果から、review event、主要コメント、未解決の論点候補を整理します。
6. 変更操作は実行せず、必要なら次に使うべき `github-pr-comment` または `github-pr-review` を案内します。

## 出力形式

- 対象リポジトリと対象PR。
- review event ごとの現在状態。
- 確認できたreviewコメントの要点。
- 読み取れなかった項目があれば、その理由。

## 禁止事項と確認が必要な操作

- このSkillでreview返信、通常コメント追加、review送信、thread解決、merge、close は行いません。
- unresolved thread の完全管理やinline thread解決は扱わず、reviews と review comments の確認までに留めます。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- 対象リポジトリやPRが確定していない状態で推測の説明をしません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、API取得範囲のどこが原因かを切り分けます。
