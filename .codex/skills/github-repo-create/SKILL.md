---
name: github-repo-create
description: "GitHub Repositoryを新規作成するときに使う。repoを作って、private/public repo作成、origin作成、初回push前のrepository作成、という依頼で使い、`gh` を優先する。"
---

# GitHub Repo Create

GitHub Repositoryを新規作成するためのSkillです。GitHub CLI `gh` を主経路にし、owner、repo名、公開範囲、初期化方法を確認してから作成します。

## 共通方針

- GitHub Repository作成では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 確認結果、作成内容、報告文は、ユーザーまたは対象組織の規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- 新しいGitHub Repositoryを作成したい。
- ローカルrepoを紐づけるためのremote repoを作りたい。
- private、public、internalなどの公開範囲を指定してrepoを作りたい。

## 期待する入力

- owner。例: ユーザー名またはorganization名。
- repo名。
- 公開範囲。例: private、public、internal。
- description、homepage、README初期化、`.gitignore`、license、team指定の有無。
- ローカルrepoと紐づけるか、空repoとして作るか。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` で同名repoが既に存在しないか確認します。存在する場合は新規作成を中止します。
3. owner、repo名、公開範囲、初期化方法、ローカルremote追加やpush有無を確認します。
4. public repoを作る場合、または組織配下に作る場合は、対象ownerと公開範囲を明示確認します。
5. `gh repo create OWNER/REPO --private|--public|--internal ...` を実行します。ローカルrepo連携が明示されている場合だけ `--source`、`--remote`、`--push` を使います。
6. 作成後に `gh repo view OWNER/REPO` でURL、visibility、default branchを確認します。
7. ローカルremoteを追加した場合は `git remote -v` で向き先を確認します。

## 出力形式

- 作成した `OWNER/REPO` とURL。
- visibility、default branch、descriptionなど主要設定。
- ローカルremote追加やpushを行った場合は、その結果。

## 禁止事項と確認が必要な操作

- 明示依頼なしにpublic repoを作成しません。
- 明示依頼なしに既存repoのvisibility変更、削除、移管、archive、branch保護設定変更は行いません。
- ローカルrepoのcommit、push、remote上書きはこのSkillだけでは行いません。必要な場合は別操作として確認します。
- owner、repo名、公開範囲が曖昧なまま作成しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、owner、repo名重複、組織ポリシーを切り分けて報告します。
