---
name: github-issue-close
description: GitHub IssueをCloseするときに使う。
---

# GitHub Issue Close

既存GitHub IssueをCloseするためのSkillです。GitHub CLI `gh` を主経路にし、現在状態を確認してからCloseだけを実行します。

## 使うべき状況

- GitHub IssueをCloseしたい。
- 解決済み、不要、重複などの理由でIssueを閉じたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 必要ならClose前に把握しておく理由や補足。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態がOpenかどうかを確認します。
4. 影響が大きい場合は、Close対象と理由を短く整理します。
5. `gh issue close <number-or-url> --repo OWNER/REPO` を実行します。
6. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` でClosedになったことを確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 実行した操作がCloseであること。
- 実行後に確認した状態。

## 禁止事項と確認が必要な操作

- このSkillでReopen、本文更新、コメント追加、親子関係変更は行いません。
- 対象Issueが確定していない状態でCloseしません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象Issueを切り分けて報告します。

