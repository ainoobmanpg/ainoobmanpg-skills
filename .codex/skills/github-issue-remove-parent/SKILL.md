---
name: github-issue-remove-parent
description: GitHub Issueから親Issueを解除するときに使う。
---

# GitHub Issue Remove Parent

GitHub Issueから親Issueを解除するためのSkillです。GitHub CLI `gh` を主経路にしますが、現行の標準 `gh issue` に親Issue解除の直結フラグがない場合があります。その場合は `gh api graphql` の `removeSubIssue` mutation を使い、現在状態とCLI仕様を確認したうえで親Issue解除を実行します。

## 使うべき状況

- 既存Issueから親Issueを外したい。
- 階層関係のうち、親Issueの解除だけを行いたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- 親Issueを解除したいIssue番号またはIssue URL。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在の親Issue状態を確認します。
4. 実行前に `gh issue edit --help`、`gh help issue` などで親Issue解除に使う現在のCLI仕様を確認します。
5. 標準 `gh issue` に直結オプションが見当たらない場合は、現在の親IssueのIssue IDと対象子IssueのIssue IDを取得し、`gh api graphql` の `removeSubIssue(input:{issueId:<parent-id>, subIssueId:<child-id>})` を使えることを確認します。
6. 利用可能な手段が確認できた場合にだけ、親Issue解除だけを実行します。確認できない場合は実行せず、未対応として報告します。
7. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` などで親Issueが外れたことを確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行した操作が親Issue解除であること。
- 実行後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillで親Issue設定、sub-issue追加削除、本文更新、Close/Reopenは行いません。
- CLIやGitHub側の仕様差分がある場合は、推測で実行しません。
- GraphQL mutation に必要なIssue IDや現在の親Issueが取得できない場合は、操作を試しません。
- 対象Issueが確定していない状態で変更しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、CLI仕様のどこが原因かを切り分けます。
