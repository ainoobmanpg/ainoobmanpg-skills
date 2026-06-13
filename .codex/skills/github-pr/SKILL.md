---
name: github-pr
description: GitHub Pull Request関連の依頼を適切な子Skillへ振り分けるときに使う。CodexのGitHub Plugin/connectorよりこのSkill群とGitHub CLI `gh` を優先し、PRタイトル、本文、コメント、review本文は指定がない限り日本語にする。
---

# GitHub Pull Request

GitHub Pull Request関連の依頼を整理し、適切な子Skillへ振り分けるための入口Skillです。このSkill自身でPR操作を全部まとめて実行するのではなく、依頼内容を切り分けて主担当のSkillを選びます。主経路はGitHub CLI `gh` を前提にします。

## 共通方針

- GitHub Pull Request関連依頼では、CodexのGitHub Plugin/connectorよりこのSkill群とGitHub CLI `gh` を優先します。
- PRタイトル、本文、コメント、review本文、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。

## 使うべき状況

- GitHub Pull Requestに関する依頼を受けたが、作成、閲覧、コメント、review送信、reviewコメント確認、本文更新、CI確認、close、reopen、mergeのどれかを切り分けたい。
- PR系Skill群のどれを使うべきか最初に判断したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO`、PR番号、PR URL、またはローカルリポジトリ。
- やりたい操作の種類。例: 新規作成、状態確認、通常コメント追加、review送信、reviewコメント確認、本文更新、CI確認、close、reopen、merge。
- 必要なら対象branch、タイトル、本文、review event。

## 実行手順

1. 依頼がPRの新規作成、閲覧、通常コメント、review送信、reviewコメント確認、本文更新、CI確認、close、reopen、mergeのどれに当たるかを切り分けます。
2. 次の基準で子Skillを選びます。
3. PRを新規作成したい場合は `github-pr-create` を使います。
4. PRの現在状態を確認したい場合は `github-pr-view` を使います。
5. PRへ通常コメントを追加したい場合は `github-pr-comment` を使います。
6. `COMMENT`、`APPROVE`、`REQUEST_CHANGES` のreview eventを送りたい場合は `github-pr-review` を使います。
7. 既存reviewコメントやreview状態を確認したい場合は `github-pr-check-review-comments` を使います。
8. PR本文だけを更新したい場合は `github-pr-update-body` を使います。
9. PRのCI/check statusを読み取り専用で確認したい場合は `github-pr-check-ci` を使います。
10. PRをmergeせずにCloseしたい場合は `github-pr-close` を使います。
11. Close済みPRをReopenしたい場合は `github-pr-reopen` を使います。
12. PRをmergeしたい場合は `github-pr-merge` を使います。
13. PR作成前にbranch pushが必要な場合は `git-push-branch` を使ってから `github-pr-create` に戻ります。
14. CI/checkの再実行が必要な場合は、読み取り確認後に `github-actions-rerun` を使います。
15. local commit を含む場合はPR系Skillの対象外として切り分け、適切なGit作業として扱います。

## 出力形式

- 選ぶべき子Skill名。
- そのSkillを選んだ理由。
- 足りない入力があれば、その不足内容。

## 禁止事項と確認が必要な操作

- このSkillでPR作成、コメント追加、review送信をまとめて直接実行しません。
- 対象repo、PR、branch、review event が曖昧なまま書き込み系Skillへ進めません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要な子Skillを前提にします。merge、close、reopen、branch push、CI rerunなどの書き込み操作は、対象と操作内容が明確な場合だけ該当Skillへ進めます。
