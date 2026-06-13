---
name: github-pr-close
description: GitHub Pull RequestをmergeせずにCloseするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、Close理由と確認結果は指定がない限り日本語で報告する。
---

# GitHub Pull Request Close

既存GitHub Pull RequestをmergeせずにCloseするためのSkillです。GitHub CLI `gh` を主経路にし、現在状態を確認してからCloseだけを実行します。

## 共通方針

- GitHub Pull Request Closeでは、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- Close理由、確認結果、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- PRをmergeせずにCloseしたい。
- 作業中止、重複、不要、別PRへ移行などの理由でPRを閉じたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- 必要ならClose理由やClose時コメント。
- head branch削除の要否。削除は明示指定がある場合だけ扱います。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments --json state,mergedAt,headRefName,baseRefName,title,url` で現在状態を確認します。
4. PRが既にClosedなら追加操作せず報告します。
5. PRがMerged済みならClose対象ではないため中止します。
6. Close対象と理由、コメント有無、branch削除有無を確認します。
7. `gh pr close <number-or-url> --repo OWNER/REPO` を実行します。コメントが明示されている場合だけ `--comment` を使います。
8. head branch削除が明示されている場合だけ `--delete-branch` を付けます。
9. 実行後に `gh pr view <number-or-url> --repo OWNER/REPO --json state,url` でClosedになったことを確認します。

## 出力形式

- 対象リポジトリと対象PR。
- 実行した操作がCloseであること。
- Close時コメントやbranch削除の有無。
- 実行後に確認した状態。

## 禁止事項と確認が必要な操作

- このSkillでPR merge、reopen、本文更新、review送信、branch pushは行いません。
- 明示依頼なしにhead branchを削除しません。
- 対象PRが確定していない状態でCloseしません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PR、PR状態を切り分けて報告します。
