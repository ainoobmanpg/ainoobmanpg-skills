---
name: github-issue-set-assignees
description: "GitHub Issueのassignee・担当者を設定、追加、削除、更新するときに使う。担当者を付けて、assignして、外して、という依頼で使い、`gh` を優先する。"
---

# GitHub Issue Set Assignees

既存GitHub Issueの担当者を設定または更新するためのSkillです。GitHub CLI `gh` を主経路にし、現在状態と対象ユーザーを確認してから担当者操作だけを実行します。

## 共通方針

- GitHub Issue担当者操作では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、変更理由、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- GitHub Issueの担当者を追加、削除、置換したい。
- 担当者だけを独立して更新したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 設定したい担当者、追加する担当者、削除する担当者の指示。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在担当者を確認します。
4. 対象ユーザー名が誤っていないか、リポジトリで使える担当者かを確認します。
5. `gh issue edit <number-or-url> --repo OWNER/REPO` の担当者関連オプションで、指示された担当者操作だけを実行します。
6. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` で担当者状態を確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行した担当者操作。例: 追加、削除、置換。
- 実行後に確認した担当者状態。

## 禁止事項と確認が必要な操作

- このSkillで本文更新、ラベル変更、マイルストーン変更、Close/Reopenは行いません。
- 対象ユーザー名を推測で決めません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象ユーザーのどこが原因かを切り分けます。
