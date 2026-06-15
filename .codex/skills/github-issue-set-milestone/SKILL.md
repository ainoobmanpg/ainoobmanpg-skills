---
name: github-issue-set-milestone
description: "GitHub Issueのmilestone・マイルストーンを設定、変更、解除するときに使う。マイルストーンを付けて、移して、外して、という依頼で使い、`gh` を優先する。"
---

# GitHub Issue Set Milestone

既存GitHub Issueのマイルストーンを設定または更新するためのSkillです。GitHub CLI `gh` を主経路にし、現在状態と対象マイルストーンを確認してからマイルストーン操作だけを実行します。

## 共通方針

- GitHub Issueマイルストーン操作では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、変更理由、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- GitHub Issueのマイルストーンを設定、変更、解除したい。
- マイルストーンだけを独立して更新したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 設定したいマイルストーン名、または解除指示。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在マイルストーンを確認します。
4. 必要なら `gh api repos/OWNER/REPO/milestones` などで対象マイルストーンの存在を確認します。
5. `gh issue edit <number-or-url> --repo OWNER/REPO` のマイルストーン関連オプションで、指示された操作だけを実行します。
6. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` でマイルストーン状態を確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行したマイルストーン操作。例: 設定、変更、解除。
- 実行後に確認したマイルストーン状態。

## 禁止事項と確認が必要な操作

- このSkillで本文更新、ラベル変更、担当者変更、Close/Reopenは行いません。
- 存在しないマイルストーン名を推測で補完しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、マイルストーン名のどこが原因かを切り分けます。
