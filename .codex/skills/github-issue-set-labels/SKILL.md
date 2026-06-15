---
name: github-issue-set-labels
description: "GitHub Issueのlabel・ラベルを設定、追加、削除、更新するときに使う。ラベルを付けて、外して、分類して、という依頼で使い、`gh` を優先する。"
---

# GitHub Issue Set Labels

既存GitHub Issueのラベルを設定または更新するためのSkillです。GitHub CLI `gh` を主経路にし、現在状態と利用可能ラベルを確認してからラベル操作だけを実行します。

## 共通方針

- GitHub Issueラベル操作では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、変更理由、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- GitHub Issueのラベルを追加、削除、置換したい。
- ラベルだけを独立して調整したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 設定したいラベル、追加するラベル、削除するラベルの指示。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在ラベルを確認します。
4. 必要なら `gh label list --repo OWNER/REPO` などで対象ラベル名の存在を確認します。
5. `gh issue edit <number-or-url> --repo OWNER/REPO` のラベル関連オプションで、指示されたラベル操作だけを実行します。
6. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` でラベル状態を確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行したラベル操作。例: 追加、削除、置換。
- 実行後に確認したラベル状態。

## 禁止事項と確認が必要な操作

- このSkillで本文更新、担当者変更、マイルストーン変更、Close/Reopenは行いません。
- 存在しないラベル名を推測で補完しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、ラベル名のどこが原因かを切り分けます。
