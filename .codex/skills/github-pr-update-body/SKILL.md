---
name: github-pr-update-body
description: GitHub Pull Requestの本文だけを更新するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、PR本文と報告文は指定がない限り日本語にする。
---

# GitHub Pull Request Update Body

既存GitHub Pull Requestの本文だけを更新するためのSkillです。GitHub CLI `gh` を主経路にし、現在本文を確認してから本文更新だけを実行します。

## 共通方針

- GitHub Pull Request本文更新では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- PR本文、確認結果、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- 既存PRの本文だけを差し替えたい。
- PR説明、検証結果、関連Issue一覧などを更新したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 新しいPR本文。
- 必要なら既存本文から引き継ぐべき項目。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --json number,title,body,state,url` で現在本文と状態を確認します。
4. 新しい本文の内容を整え、長文では `--body-file` 用の一時ファイルを使います。
5. `gh pr edit <number-or-url> --repo OWNER/REPO --body-file <file>` を実行します。
6. 実行後に `gh pr view <number-or-url> --repo OWNER/REPO --json body,url` で本文が反映されたことを確認します。

## 出力形式

- 対象リポジトリと対象PR。
- 本文更新を実行したこと。
- 更新後に確認したPR URLと、必要なら本文の要約。

## 禁止事項と確認が必要な操作

- このSkillでPRタイトル、base/head branch、label、assignee、reviewer、merge状態は変更しません。
- 新しい本文が空、または対象PRが曖昧なまま更新しません。
- 既存本文の重要情報を消す可能性がある場合は、差し替え内容を確認してから実行します。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PRを切り分けて報告します。
