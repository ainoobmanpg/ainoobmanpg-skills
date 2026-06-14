---
name: github-pr-reopen
description: Close済みのGitHub Pull RequestをReopenするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、確認結果は指定がない限り日本語で報告する。
---

# GitHub Pull Request Reopen

Close済みのGitHub Pull RequestをReopenするためのSkillです。GitHub CLI `gh` を主経路にし、対象PRがReopen可能かを確認してからReopenだけを実行します。

## 共通方針

- GitHub Pull Request Reopenでは、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- Reopen理由、確認結果、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- Close済みのPRを再開したい。
- 誤ってCloseしたPRや、追加対応のため再開が必要なPRをReopenしたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 必要ならReopen理由や確認すべき背景。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments --json state,mergedAt,headRefName,baseRefName,title,url` で現在状態を確認します。
4. PRがOpenならReopenは不要として報告します。
5. PRがMerged済みならReopenできないため、Reopenせずに中止します。
6. 対象PRがClose済みでReopen可能なことを確認し、必要なら対象PRと理由を短く整理します。
7. `gh pr reopen <number-or-url> --repo OWNER/REPO` を実行します。
8. 実行後に `gh pr view <number-or-url> --repo OWNER/REPO --json state,url` でOpenになったことを確認します。

## 出力形式

- 対象リポジトリと対象PR。
- 実行した操作がReopenであること。
- 実行後に確認した状態とPR URL。

## 禁止事項と確認が必要な操作

- このSkillでPR本文更新、コメント追加、review送信、merge、close、branch pushは行いません。
- 対象PRが確定していない状態でReopenしません。
- Merged済みPRをReopenしようとしません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PR、PR状態のどこが原因かを切り分けます。
