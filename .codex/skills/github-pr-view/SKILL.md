---
name: github-pr-view
description: GitHub Pull Requestの現在状態を読み取り専用で確認するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、確認結果は指定がない限り日本語で報告する。
---

# GitHub Pull Request View

GitHub Pull Requestの本文、状態、review状況、checks、コメントを変更せずに確認するためのSkillです。GitHub CLI `gh` を主経路にし、認証状態と対象リポジトリを確認したうえで、読み取り専用で状況を把握します。

## 共通方針

- GitHub Pull Request確認では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 読み取った内容の要約、確認結果、次アクション案は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- PRのタイトル、本文、state、review状況、checks、comments を確認したい。
- 更新前の現在状態を安全に読みたい。
- どのreviewやコメントが付いているかを俯瞰したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 確認したい項目。例: 本文、state、review状況、checks、comments。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments` または `--json` を使って、タイトル、本文、state、reviewDecision、reviews、statusCheckRollup、comments など必要な情報だけを読みます。
4. 取得結果から、依頼された確認項目だけを整理します。
5. 変更操作は実行せず、必要なら次に使うべき更新系Skillを案内します。

## 出力形式

- 対象リポジトリと対象PR。
- 確認した項目ごとの現在状態。
- 読み取れなかった項目があれば、その理由。例: 権限不足、認証不足、CLI仕様差分。

## 禁止事項と確認が必要な操作

- このSkillでPR作成、通常コメント追加、review送信、merge、close は行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- 対象リポジトリやPRが確定していない状態で推測の説明をしません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repoのどこが原因かを切り分けます。
