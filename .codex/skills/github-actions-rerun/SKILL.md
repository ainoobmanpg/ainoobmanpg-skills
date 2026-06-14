---
name: github-actions-rerun
description: GitHub Actionsのworkflow runまたはjobをrerunするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、対象run/jobとrerun範囲を日本語で明示確認する。
---

# GitHub Actions Rerun

GitHub Actionsのworkflow runまたはjobをrerunするためのSkillです。GitHub CLI `gh` を主経路にし、対象run/jobとrerun範囲を確認してから再実行します。

## 共通方針

- GitHub Actions rerunでは、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、rerun理由、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。
- CI/check状態の読み取り確認だけなら `github-pr-check-ci` を使い、このSkillではrerun操作だけを扱います。

## 使うべき状況

- 失敗したGitHub Actions workflow runを再実行したい。
- failed jobsだけをrerunしたい。
- 特定jobだけをrerunしたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- run ID、run URL、PR番号、branch名、workflow名のいずれか。
- rerun範囲。例: failed jobsのみ、全job、特定job。特定jobの場合はjob名、または `databaseId` を特定できる情報。
- rerun理由。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. run IDがない場合は、`gh run list --repo OWNER/REPO --branch <branch>` や `gh run list --repo OWNER/REPO --workflow <workflow>` で候補を絞ります。
4. `gh run view <run-id> --repo OWNER/REPO --json status,conclusion,displayTitle,workflowName,event,headBranch,headSha,url,jobs` で対象runとjobを確認します。
5. 特定job rerunの場合は、UIやURLに見える番号をそのまま使わず、`gh run view <run-id> --repo OWNER/REPO --json jobs --jq '.jobs[] | {name, databaseId}'` で対象jobの `databaseId` を特定します。
6. 対象run/job、rerun範囲、理由を明示確認します。
7. failed jobsのみなら `gh run rerun <run-id> --repo OWNER/REPO --failed` を実行します。
8. 全jobなら `gh run rerun <run-id> --repo OWNER/REPO` を実行します。
9. 特定jobなら `gh run rerun <run-id> --repo OWNER/REPO --job <databaseId>` を実行します。
10. 実行後に `gh run view <run-id> --repo OWNER/REPO` または新しいrunの表示で状態を確認します。

## 出力形式

- 対象リポジトリ、workflow名、run ID、run URL。
- rerun範囲。例: failed jobsのみ、全job、特定job。
- rerun実行後の状態。

## 禁止事項と確認が必要な操作

- このSkillでworkflow dispatch、cancel、disable、secret更新、branch push、PR mergeは行いません。
- 対象run/jobが曖昧なままrerunしません。
- 明示依頼なしに全job rerunを選びません。失敗分だけで足りる場合は `--failed` を優先します。
- 特定job rerunで、Actions UIやURLに見える番号を `--job` に直接渡しません。必ず `databaseId` に対応付けてから実行します。
- 失敗logに秘密情報らしい値が含まれる場合、その値をそのまま出力しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセス、GitHub認証、Actions rerun権限が必要です。失敗時は認証、権限、対象run/job、Actions設定を切り分けて報告します。
