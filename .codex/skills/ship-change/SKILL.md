---
name: ship-change
description: "ローカル変更を、No-Goがなければコミット、branch push、PR作成、PR確認、merge、remote/local branch削除、main同期まで一気通貫で進めるときに使う。commitからpushまで、PR作って問題なければマージ、ブランチをmainだけに、段階確認を省いて進めたい、という依頼で使う。"
---

# Ship Change

ローカル変更をレビューし、問題がなければ commit から PR merge とブランチ整理まで進めるための薄いオーケストレーターSkillです。既存の `git-commit`、`git-push-branch`、`github-pr-create`、`github-pr-view`、`github-pr-merge` を置き換えず、停止条件と進行順だけを定めます。

## 基本方針

- repoの `AGENTS.md`、README、既存Skill、branch protection、default branch を先に確認します。
- No-Go条件が見つからない限り、ユーザー確認を挟まず一気通貫で進めます。
- No-Go条件が見つかったら、そこで止めて理由と次の一手だけを報告します。
- default branch へ直接 push しません。保護状況が不明でも作業branchからPRを作ります。
- PRが安全条件を満たす場合だけ merge します。
- merge後は head branch を remote/local とも削除し、default branch へ戻して同期します。

## 期待する入力

- 作業目的またはコミットに含めたい範囲。
- 必要ならコミット件名、PRタイトル、merge方式。
- 既存IssueやPRがあれば番号またはURL。

不足分は `git status`、差分、README、`AGENTS.md`、既存Skill、GitHub PR状態から補完します。危険な推測になる場合だけ確認します。

## 実行手順

1. `git status --short --branch`、`git remote -v`、`git branch --show-current` で現在状態を確認します。
2. `AGENTS.md`、README、関連Skillを確認し、repo固有ルールを拾います。
3. 差分を確認し、秘密情報、無関係差分、生成物、未完了作業、検証不足があれば No-Go として止めます。
4. 必要最小限のファイルだけを stage し、`git diff --cached` を確認して commit します。
5. default branch上でcommitした場合は、すぐ作業branchを作ってそのbranchをpushします。branch名は変更内容から短いASCII kebab-caseにします。
6. pushed branch から default branch へのPRを作ります。PR本文には概要と確認結果を短く書きます。
7. PR状態を確認します。
8. 次の安全条件をすべて満たす場合だけ merge します。
   - PRがOpenでdraftではない。
   - `mergeStateStatus` が `CLEAN`。
   - required checks の失敗やpendingがない。
   - review必須未満ではない。
   - conflictがない。
9. merge方式はユーザー指定があれば従い、未指定ならrepo慣例を優先します。慣例が読めなければ `--merge` を使います。
10. merge時に `--delete-branch` を付け、remote head branch を削除します。
11. `git fetch origin --prune`、default branchへ `git switch`、`git merge --ff-only origin/<default>` を実行します。
12. local head branch が default branch ではなく、merge済みで、`git branch -d <branch>` が通る場合だけ削除します。
13. 最後に `git status --short --branch`、local/remote branch一覧、直近logを確認します。

## No-Go条件

- 秘密情報、APIキー、個人情報、認証済みセッション内容の混入疑いがある。
- コミット範囲が曖昧、または無関係差分が混ざっている。
- 生成物、キャッシュ、ログ、デバッグ痕跡が混ざっている。
- 必要な検証が未実行で、repo内の情報から安全に補えない。
- push先、default branch、remote、PR head/base が確定できない。
- PRがdraft、conflictあり、required check失敗またはpending、必要review未完了。
- merge方式やbranch削除がrepo規約と衝突する。
- force push、履歴改変、破壊的削除、本番操作、外部公開設定変更が必要になる。

## 出力形式

- No-Goで止めた場合: 理由、止めた段階、次の一手。
- 完了した場合: commit hash、PR URL、merge commit、削除したbranch、最終status。

## 禁止事項

- No-Go条件を無視して進めません。
- default branchを削除しません。
- `git add .`、force push、rebase、amend、`git reset --hard`、`git clean` を暗黙に使いません。
- CI失敗、review未完了、conflict、draft状態をoverrideしません。
- 既存の専門Skillを不要に拡張せず、このSkillは進行制御だけに留めます。
