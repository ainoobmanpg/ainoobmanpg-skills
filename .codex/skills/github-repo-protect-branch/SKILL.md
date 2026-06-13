---
name: github-repo-protect-branch
description: GitHub Repositoryのbranch protectionを設定または更新するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、対象branchと保護内容を日本語で明示確認する。
---

# GitHub Repo Protect Branch

GitHub Repositoryのbranch protectionを設定または更新するためのSkillです。GitHub CLI `gh` とGitHub APIを主経路にし、現在設定と変更内容を確認してから保護設定を更新します。

## 共通方針

- Branch protection更新では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、変更内容、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- `main` や `develop` など、具体的なbranch名のbranch protectionを設定したい。
- PR必須、review必須、status check必須、force push禁止、削除禁止などを設定したい。
- 既存branch protectionの現在値を確認し、必要な差分だけ更新したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO`。
- 対象branch名。REST branch protection APIではwildcard patternを扱えないため、具体的なbranch名だけを対象にします。
- 設定したい保護内容。例: required reviews、required status checks、enforce admins、force pushes、deletions。
- 既存保護を弱めてよいか。弱体化は明示依頼がある場合だけ扱います。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` で対象リポジトリ、visibility、default branchを確認します。
3. 対象branch名に `*` などのwildcard patternが含まれる場合は、このREST branch protection手順では扱わず、GitHub RulesetsまたはGraphQLでのbranch pattern保護に切り分けます。
4. `gh api repos/OWNER/REPO/branches/<branch>` で対象branchが存在することを確認します。
5. `gh api repos/OWNER/REPO/branches/<branch>/protection` で現在の保護設定を確認します。未設定の404は未設定として扱います。
6. 変更後の設定をJSONで組み立て、現在値との差分を整理します。
7. 保護を弱める変更、required checkの削除、admin enforcement解除、force push許可、branch deletion許可が含まれる場合は、明示依頼がない限り中止します。
8. 対象repo、branch、変更内容を明示確認してから `gh api -X PUT repos/OWNER/REPO/branches/<branch>/protection --input <json-file>` を実行します。
9. 実行後に同じprotection APIで設定が反映されたことを確認します。

## 出力形式

- 対象リポジトリと対象branch。
- 変更前後の主要設定。
- 実行したAPI操作の概要。
- 反映確認の結果。

## 禁止事項と確認が必要な操作

- 対象repo、branch、保護内容が曖昧なまま更新しません。
- wildcard branch patternをREST branch protection APIの `<branch>` pathに渡しません。pattern保護はRulesetsまたはGraphQLの別手順に切り分けます。
- 明示依頼なしに保護を弱めません。
- branch削除、repo削除、visibility変更、default branch変更は行いません。
- organizationやpersonal repoで利用できる保護項目が異なる場合は、エラー内容を確認して必要最小限に調整します。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセス、GitHub認証、対象repoの管理権限が必要です。失敗時は認証、権限、repo種別、branch存在、保護項目を切り分けて報告します。
