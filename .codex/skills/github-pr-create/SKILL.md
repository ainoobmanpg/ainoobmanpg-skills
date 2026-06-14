---
name: github-pr-create
description: push済みのブランチからGitHub Pull Requestを新規作成するときに使う。未push branchの場合は先に `git-push-branch` を使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、PRタイトルと本文は指定がない限り日本語で作成する。
---

# GitHub Pull Request Create

push済みのbranchからGitHub Pull Requestを新規作成するためのSkillです。GitHub CLI `gh` を主経路にし、認証状態、対象リポジトリ、head branch、base branch、タイトル、本文、draft指定を確認してから作成します。branchが未pushの場合は、このSkillでpushせず `git-push-branch` を使ってから戻ります。

## 共通方針

- GitHub Pull Request作成では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- PRタイトル、本文、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- すでにpush済みのbranchから新しいPRを作りたい。
- draft PRを含め、タイトルと本文を指定してPRを登録したい。
- 未push branchからPRを作りたい依頼では、先に `git-push-branch` へ切り分けたい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- head branch。
- base branch。
- PRタイトル。
- PR本文。
- 必要なら `draft` 指定。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `git branch --show-current` や `git branch --list` などでhead branchを確認し、必要なら `git rev-parse --abbrev-ref <head>@{upstream}` や `git ls-remote --heads origin <head>` で対象branchがすでにpush済みであることを確認します。
4. 対象branchが未pushの場合はPR作成を止め、`git-push-branch` でpushしてからこのSkillに戻ります。
5. base branch、タイトル、本文、必要ならdraft指定が揃っていることを確認し、長文本文では `--body-file` を使う前提で整えます。
6. `gh pr create --repo OWNER/REPO --base <base> --head <head> --title ... --body ...` または `--body-file` を実行し、必要なら `--draft` を付けます。
7. 作成後に `gh pr view <created-number-or-url> --repo OWNER/REPO` で結果を確認します。

## 出力形式

- 対象リポジトリと作成したPR番号またはURL。
- 実行した作成内容。例: head/base branch、タイトル、draft有無。
- 作成後に確認した現在状態。

## 禁止事項と確認が必要な操作

- このSkillでlocal commit、branch push、merge、close、reopenは行いません。
- pushされていないbranchからの作成を前提に進めません。未pushの場合は `git-push-branch` に切り分けます。
- 対象repo、head branch、base branch、タイトル、本文が曖昧なまま作成しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、branch状態のどこが原因かを切り分けます。
