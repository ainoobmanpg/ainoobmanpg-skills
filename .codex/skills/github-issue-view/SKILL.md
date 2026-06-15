---
name: github-issue-view
description: "GitHub Issueの現在状態を読み取り専用で確認するときに使う。Issueを見て、状態確認、本文やコメントやラベルを確認して、という依頼で使い、`gh` を優先する。"
---

# GitHub Issue View

GitHub Issueの本文、コメント、状態、ラベル、担当者、親子関係などを、変更せずに確認するためのSkillです。GitHub CLI `gh` を主経路にし、認証状態と対象リポジトリを確認したうえで、読み取り専用で状況を把握します。

## 共通方針

- GitHub Issue確認では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 読み取った内容の要約、確認結果、次アクション案は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- Issue本文、コメント、Close状態、ラベル、担当者を確認したい。
- 更新前の現在状態を安全に読みたい。
- 親Issue、sub-issue、関連URLなどの現状把握だけをしたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 確認したい項目。例: 本文、コメント、状態、ラベル、担当者、親子関係。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` または `--json` を使って、必要な情報だけを読みます。
4. 取得結果から、依頼された確認項目だけを整理します。
5. 変更操作は実行せず、必要なら次に使うべき更新系Skillを案内します。

## 出力形式

- 対象リポジトリと対象Issue。
- 確認した項目ごとの現在状態。
- 読み取れなかった項目があれば、その理由。例: 権限不足、認証不足、CLI仕様差分。

## 禁止事項と確認が必要な操作

- このSkillでIssueの更新、コメント追加、Close/Reopen、親子関係変更を行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- 対象リポジトリやIssueが確定していない状態で推測の説明をしません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repoのどこが原因かを切り分けます。
