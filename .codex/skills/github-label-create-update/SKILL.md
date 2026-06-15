---
name: github-label-create-update
description: "GitHub Labelを作成または更新するときに使う。ラベルを作って、色や説明を変えて、labelを整備して、という依頼で使い、Issue/PRへの付与とは切り分け、`gh` を優先する。"
---

# GitHub Label Create Update

GitHub Labelを作成または更新するためのSkillです。GitHub CLI `gh` を主経路にし、既存labelを確認してから作成または更新します。

## 共通方針

- GitHub Label作成更新では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- label名、説明、確認結果、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- 新しいGitHub Labelを作成したい。
- 既存Labelの色や説明を更新したい。
- 複数Labelの有無と設定を整えたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- label名。
- 色。例: `ffcc00`。`#` なしの6桁hexを基本にします。
- description。
- 既存labelがある場合に更新してよいか。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh label list --repo OWNER/REPO --search <label-name>` で既存labelを確認します。
4. 新規作成なら `gh label create <name> --repo OWNER/REPO --color <hex> --description <text>` を実行します。
5. 既存更新なら `gh label edit <name> --repo OWNER/REPO --color <hex> --description <text>` を実行します。
6. 実行後に `gh label list --repo OWNER/REPO --search <label-name>` で反映を確認します。

## 出力形式

- 対象リポジトリとlabel名。
- 作成または更新のどちらを行ったか。
- 色、説明、反映確認結果。

## 禁止事項と確認が必要な操作

- このSkillでlabel削除、Issue/PRへのlabel付与、milestone変更は行いません。
- 既存labelの意味を変える更新は、変更内容を確認してから実行します。
- label名、色、説明が曖昧なまま作成更新しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、label重複、色形式を切り分けて報告します。
