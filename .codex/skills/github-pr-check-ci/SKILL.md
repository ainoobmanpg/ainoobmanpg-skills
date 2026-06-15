---
name: github-pr-check-ci
description: "GitHub Pull RequestのCI/check statusを読み取り専用で確認するときに使う。PRのCI見て、checks確認、失敗jobを調べて、という依頼で使い、rerunは別Skillへ切り分け、`gh` を優先する。"
---

# GitHub Pull Request Check CI

GitHub Pull RequestのCI/check statusを読み取り専用で確認するためのSkillです。GitHub CLI `gh` を主経路にし、失敗check、pending check、workflow runの詳細を調べます。

## 共通方針

- GitHub Pull Request CI確認では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、失敗要約、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- PRのCIが通っているか確認したい。
- 失敗しているcheckやworkflow runを読み取り専用で特定したい。
- rerun前に対象runやjobを確認したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号、PR URL、またはbranch名。
- 必要なら詳しく見るworkflow名、run ID、job名。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --json number,headRefName,baseRefName,state,isDraft,statusCheckRollup,url` でPRとcheck概要を確認します。
4. `gh pr checks <number-or-url> --repo OWNER/REPO` でcheck一覧を確認します。
5. 必要なら `gh run list --repo OWNER/REPO --branch <head-branch>` と `gh run view <run-id> --repo OWNER/REPO --log-failed` で失敗runや失敗logを確認します。
6. 失敗、pending、skipped、successを分け、rerunが必要な場合は `github-actions-rerun` の対象情報としてrun IDやjob IDを整理します。

## 出力形式

- 対象リポジトリ、PR番号、head/base branch。
- check全体の状態。
- 失敗またはpendingのcheck名、workflow名、run ID、job ID、URL。
- rerunが必要な場合の候補。ただしこのSkillではrerunしません。

## 禁止事項と確認が必要な操作

- このSkillは読み取り専用です。workflow rerun、cancel、dispatch、merge、pushは行いません。
- 失敗logに秘密情報らしい値が含まれる場合、その値をそのまま出力しません。
- 対象PRやrunが曖昧なまま結論を出しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PR、Actions権限を切り分けて報告します。
