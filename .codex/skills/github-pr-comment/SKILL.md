---
name: github-pr-comment
description: GitHub Pull Requestに通常コメントを追加するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、コメント本文は指定がない限り日本語で書く。
---

# GitHub Pull Request Comment

既存GitHub Pull Requestへ通常コメントを追加するためのSkillです。GitHub CLI `gh` を主経路にし、対象PRの現在状態を確認してから、review送信と混ぜずに通常コメントだけを追加します。

## 共通方針

- GitHub Pull Requestコメント追加では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- コメント本文と報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- PRへ通常コメントを追加したい。
- 補足情報、進捗、確認依頼をコメントとして残したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 追加したいコメント本文。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments` で現在状態と直近コメントを読みます。
4. `gh pr comment <number-or-url> --repo OWNER/REPO --body ...` または `--body-file` で通常コメントを追加します。
5. 追加後に `gh pr view <number-or-url> --repo OWNER/REPO --comments` で反映を確認します。

## 出力形式

- 対象リポジトリと対象PR。
- 追加したコメントの要点。
- 追加後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillでreview event送信、merge、close、既存コメントの編集や削除は行いません。
- 対象repoやPRが曖昧なまま書き込みをしません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PRを切り分けて報告します。
