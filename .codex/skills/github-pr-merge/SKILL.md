---
name: github-pr-merge
description: GitHub Pull Requestをmergeするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、対象PR、merge方式、CI/review状態を日本語で明示確認する。
---

# GitHub Pull Request Merge

GitHub Pull RequestをmergeするためのSkillです。GitHub CLI `gh` を主経路にし、対象PR、CI、review、merge方式、branch削除有無を確認してからmergeします。

## 共通方針

- GitHub Pull Request mergeでは、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- merge確認、結果報告、関連Issueの扱いは、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- OpenなPRをmergeしたい。
- squash、merge commit、rebase mergeのどれかを指定してmergeしたい。
- merge前にCI、review、conflict、draft状態を確認したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- merge方式。例: `--merge`、`--squash`、`--rebase`。
- merge commit title/body、squash commit title/bodyの指定。
- head branch削除の要否。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --json state,isDraft,mergeStateStatus,reviewDecision,headRefName,baseRefName,title,url,statusCheckRollup` でPR状態を確認します。
4. `gh pr checks <number-or-url> --repo OWNER/REPO` でCI状態を確認します。
5. Draft、conflict、required check失敗、必要review未完了がある場合は、明示的なoverride依頼がない限りmergeを中止します。
6. merge方式、commit title/body、head branch削除有無を確認します。
7. 対象repo、PR、base/head branch、merge方式、CI/review状態を明示確認してから `gh pr merge <number-or-url> --repo OWNER/REPO --merge|--squash|--rebase` を実行します。
8. head branch削除が明示されている場合だけ `--delete-branch` を付けます。
9. 実行後に `gh pr view <number-or-url> --repo OWNER/REPO --json state,mergedAt,url` でMergedになったことを確認します。

## 出力形式

- 対象リポジトリと対象PR。
- merge方式、head branch削除有無。
- merge前に確認したCI/review状態。
- merge後に確認した状態とPR URL。

## 禁止事項と確認が必要な操作

- 対象PR、merge方式、branch削除有無が曖昧なままmergeしません。
- CI失敗、review未完了、conflict、draft状態を無視しません。overrideが必要な場合は明示依頼を確認します。
- 明示依頼なしにhead branchを削除しません。
- このSkillでPR本文更新、review送信、Issue close、release作成は行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセス、GitHub認証、対象repoへのmerge権限が必要です。失敗時は認証、権限、branch protection、CI、review、conflictを切り分けて報告します。
