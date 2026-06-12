---
name: github-issue-update-body
description: GitHub Issueの本文だけを更新するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、本文は指定がない限り日本語で更新する。
---

# GitHub Issue Update Body

既存GitHub Issueの本文だけを安全に更新するためのSkillです。GitHub CLI `gh` を主経路にし、現在本文を読み取ってから、無関係な変更を混ぜずに反映します。

## 共通方針

- GitHub Issue本文更新では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 更新本文、コメント、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- 既存Issueの本文を更新したい。
- タイトルやラベルではなく、本文だけを差し替えたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 新しい本文、または現在本文に対する明確な更新内容。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態と本文を読みます。
4. 影響が大きい変更なら、反映前に差分または更新方針を短く整理します。
5. `gh issue edit <number-or-url> --repo OWNER/REPO --body ...` または `--body-file` で本文だけを更新します。
6. 更新後に `gh issue view <number-or-url> --repo OWNER/REPO` で本文が意図どおり変わったことを確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 変更した本文の要点。
- 更新後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillでタイトル、ラベル、担当者、マイルストーン、Close/Reopen、親子関係は更新しません。
- 既存Issue本文の無関係な整形、言い換え、項目削除を勝手に行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象Issueを切り分けて報告します。
