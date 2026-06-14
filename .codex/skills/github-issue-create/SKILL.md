---
name: github-issue-create
description: GitHub Issueを新規作成するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、タイトルや本文は指定がない限り日本語で作成する。
---

# GitHub Issue Create

GitHub Issueを新規作成するためのSkillです。GitHub CLI `gh` を主経路にし、認証状態、対象リポジトリ、タイトルと本文の内容を確認してから作成します。

## 共通方針

- GitHub Issue作成では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- タイトル、本文、コメント、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- GitHub Issueを新規登録したい。
- タイトル、本文、必要に応じてラベルや担当者を指定してIssueを作りたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- 新規Issueのタイトル。
- 新規Issueの本文。
- 必要なら初期ラベル、担当者、マイルストーン。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. タイトルと本文が揃っていることを確認し、長文本文では `--body-file` を使う前提で整えます。
4. `gh issue create --repo OWNER/REPO --title ... --body ...` または `--body-file` を実行します。
5. 作成後に `gh issue view <created-number> --repo OWNER/REPO` で結果を確認します。

## 出力形式

- 対象リポジトリと作成したIssue番号またはURL。
- 実行した作成内容。例: タイトル、主要本文、追加したラベル。
- 作成後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillで既存Issue本文の更新、コメント追加、Close/Reopen、親子関係変更をまとめて扱いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- 対象リポジトリが確定していない状態で作成しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repoを切り分けて報告します。
