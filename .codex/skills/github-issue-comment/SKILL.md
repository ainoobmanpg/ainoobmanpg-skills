---
name: github-issue-comment
description: "GitHub Issueに通常コメントを追加するときに使う。Issueへコメントして、進捗を書いて、確認結果を投稿して、という依頼で使い、本文は指定がない限り日本語、`gh` を優先する。"
---

# GitHub Issue Comment

既存GitHub Issueへコメントを追加するためのSkillです。GitHub CLI `gh` を主経路にし、対象Issueの現在状態を確認してから、本文更新と混ぜずにコメントだけを追加します。

## 共通方針

- GitHub Issueコメント追加では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- コメント本文と報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- GitHub Issueへコメントを追加したい。
- 進捗、確認依頼、補足情報をコメントとして残したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 追加したいコメント本文。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態と直近コメントを読みます。
4. `gh issue comment <number-or-url> --repo OWNER/REPO --body ...` または `--body-file` でコメントを追加します。
5. 追加後に `gh issue view <number-or-url> --repo OWNER/REPO --comments` で反映を確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 追加したコメントの要点。
- 追加後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillでIssue本文更新、Close/Reopen、親子関係変更は行いません。
- 既存コメントの編集や削除は扱いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象Issueを切り分けて報告します。
