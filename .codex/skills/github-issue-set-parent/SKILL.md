---
name: github-issue-set-parent
description: GitHub Issueに親Issueを設定するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、確認結果は指定がない限り日本語で報告する。
---

# GitHub Issue Set Parent

GitHub Issueに親Issueを設定するためのSkillです。GitHub CLI `gh` を主経路にしますが、現行の標準 `gh issue` に親Issue設定の直結フラグがない場合があります。その場合は `gh api graphql` の `addSubIssue` mutation を使い、現在状態とCLI仕様を確認したうえで親Issue設定を実行します。

## 共通方針

- GitHub Issue親子関係操作では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、変更理由、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- 既存Issueに親Issueを設定したい。
- 階層関係のうち、親Issueの追加だけを行いたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- 子として扱うIssue番号またはIssue URL。
- 親として設定したいIssue番号またはIssue URL。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態を確認します。
4. 実行前に `gh issue edit --help`、`gh issue create --help`、`gh help issue` などで親Issue設定に使う現在のCLI仕様を確認します。
5. 標準 `gh issue` に直結オプションが見当たらない場合は、親Issue側のIssue IDと子Issue側のIssue IDを取得し、`gh api graphql` の `addSubIssue(input:{issueId:<parent-id>, subIssueId:<child-id>})` を使えることを確認します。
6. 利用可能な手段が確認できた場合にだけ、親Issueを設定する操作だけを実行します。確認できない場合は実行せず、未対応として報告します。
7. 実行後に `gh issue view <number-or-url> --repo OWNER/REPO` などで親Issueが意図どおり設定されたことを確認します。

## 出力形式

- 対象リポジトリ、対象Issue、設定した親Issue。
- 実行した親Issue設定の方法。
- 実行後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillで親Issueの解除、sub-issue追加削除、本文更新、Close/Reopenは行いません。
- CLIやGitHub側の仕様差分がある場合は、推測で実行しません。
- GraphQL mutation に必要なIssue IDが取得できない場合は、操作を試しません。
- 対象Issueや親Issueが確定していない状態で変更しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、CLI仕様のどこが原因かを切り分けます。
