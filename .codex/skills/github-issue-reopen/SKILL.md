---
name: github-issue-reopen
description: GitHub IssueをReopenするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、確認結果は指定がない限り日本語で報告する。
---

# GitHub Issue Reopen

ClosedになっているGitHub IssueをReopenするためのSkillです。GitHub CLI `gh` を主経路にし、現在状態を確認してからReopenだけを実行します。

## 共通方針

- GitHub Issue Reopenでは、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- Reopen理由、確認結果、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- ClosedのIssueを再度Openに戻したい。
- 再調査、再対応、誤Closeの修正をしたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 必要ならReopen理由や補足。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態がClosedかどうかを確認します。
4. 必要ならReopen対象と理由を短く整理します。
5. `gh issue reopen <number-or-url> --repo OWNER/REPO` を実行します。
6. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` でOpenになったことを確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行した操作がReopenであること。
- 実行後に確認した状態。

## 禁止事項と確認が必要な操作

- このSkillでClose、本文更新、コメント追加、親子関係変更は行いません。
- 対象Issueが確定していない状態でReopenしません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象Issueを切り分けて報告します。
