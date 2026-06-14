---
name: github-pr-review
description: GitHub Pull Requestへreview eventを送信するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、review本文は指定がない限り日本語で書く。
---

# GitHub Pull Request Review

既存GitHub Pull Requestへreviewを送信するためのSkillです。GitHub CLI `gh` を主経路にし、対象PRの現在状態と意図するreview eventを確認してから、`COMMENT`、`APPROVE`、`REQUEST_CHANGES` のいずれかを送信します。

## 共通方針

- GitHub Pull Request review送信では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- review本文、補足コメント、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- PRへreviewを送りたい。
- `COMMENT`、`APPROVE`、`REQUEST_CHANGES` を明示してレビュー結果を残したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- PR番号またはPR URL。
- review event。`COMMENT`、`APPROVE`、`REQUEST_CHANGES` のいずれか。
- review本文。少なくとも `COMMENT` と `REQUEST_CHANGES` では必須として扱います。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh pr view <number-or-url> --repo OWNER/REPO --comments` または `--json reviewDecision,reviews` で現在のreview状況を確認します。
4. 対象PRと送信するreview eventを確認し、`COMMENT`、`APPROVE`、`REQUEST_CHANGES` のどれを送るかを明示します。
5. 本文が必要な場合は内容を整え、長文では `--body-file` を優先します。
6. `gh pr review <number-or-url> --repo OWNER/REPO --comment --body ...`、`--approve --body ...`、`--request-changes --body ...` のいずれかを実行します。
7. 実行後に `gh pr view <number-or-url> --repo OWNER/REPO --comments` または `--json reviews,reviewDecision` で反映を確認します。

## 出力形式

- 対象リポジトリと対象PR。
- 送信したreview event。
- review本文の要点。
- 送信後に確認したreview状態。

## 禁止事項と確認が必要な操作

- このSkillで通常コメント追加、merge、close、inline threadの解決は行いません。
- `COMMENT` と `REQUEST_CHANGES` で本文なしの送信を前提に進めません。`APPROVE` でも本文がある場合はその内容を確認してから送信します。
- 対象PRや送信するreview event が曖昧なまま実行しません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象PR、review event のどこが原因かを切り分けます。
