---
name: git-push-branch
description: ローカルGit branchをremoteへpushするときに使う。push対象、remote、upstream、force有無を確認し、GitHub PR作成前のbranch公開にも使う。
---

# Git Push Branch

ローカルGit branchをremoteへpushするためのSkillです。現在branch、remote、upstream、未commit差分、push方法を確認してからpushします。

## 共通方針

- Git branch pushでは、対象branchとremoteを先に確認します。
- GitHub PR作成前にbranchが未pushの場合は、このSkillでpushしてから `github-pr-create` に戻ります。
- 報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。

## 使うべき状況

- 現在branchをoriginなどのremoteへpushしたい。
- upstream未設定のbranchを初回pushしたい。
- PR作成前にhead branchをGitHubへ公開したい。

## 期待する入力

- 対象ローカルリポジトリ。
- pushするbranch名。未指定なら現在branchを確認します。
- remote名。未指定なら `origin` を基本に確認します。
- upstream設定の要否。
- force pushが必要か。通常は使いません。

## 実行手順

1. `git status --short --branch` で現在branch、upstream、作業ツリー状態を確認します。
2. `git remote -v` でremoteの向き先を確認します。
3. `git branch --show-current` でpush対象branchを確認します。
4. `git log --oneline --decorate -n 5` で直近commitを確認し、push対象が意図したcommitか確認します。
5. upstreamが未設定なら `git push -u <remote> <branch>` を実行します。既にupstreamがある場合は `git push` または `git push <remote> <branch>` を実行します。
6. 実行後に `git status --short --branch` や `git rev-parse --abbrev-ref <branch>@{upstream}` でupstreamと同期状態を確認します。

## 出力形式

- pushしたbranch、remote、upstream。
- push前後の同期状態。
- pushしたcommitの短い要約。

## 禁止事項と確認が必要な操作

- このSkillでcommit、merge、rebase、tag作成、PR作成は行いません。
- 対象branchやremoteが曖昧なままpushしません。
- 未commit差分がある場合は、push対象commitと作業ツリーの差分を区別して報告します。
- `--force` は原則使いません。明示依頼がある場合でも `--force-with-lease` を優先し、対象branch、remote、理由を確認してから実行します。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとremoteへの書き込み権限が必要です。失敗時は認証、権限、remote、branch、upstream、保護設定を切り分けて報告します。
