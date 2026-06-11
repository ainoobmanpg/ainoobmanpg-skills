---
name: github-issue-manager
description: GitHub Issueの作成、更新、親子関係、チェックボックス、コメント、Close/Reopen、Issueテンプレートを管理するときに使う。
---

# GitHub Issue Manager

GitHub Issueを作成、確認、更新、コメント、Close/Reopen、親子関係の設定、チェックボックス更新、Issueテンプレート作成まで安全に扱うためのSkillです。GitHub CLI `gh` を主経路にし、認証状態、対象リポジトリ、対象Issue、現在本文を確認してから変更します。

## 使うべき状況

- GitHub Issueを新規登録する。
- 既存Issueのタイトル、本文、ラベル、担当者、マイルストーンを更新する。
- Issue本文内のチェックボックスをON/OFFする。
- Issueへコメントを追加する。
- IssueをCloseまたはReopenする。
- 親Issue、子Issue、sub-issueの関係を作成、追加、削除する。
- `.github/ISSUE_TEMPLATE/` 配下にIssueテンプレートまたはIssue Formsを作成、更新する。

## 期待する入力

作業前に、分かる範囲で次の情報を確認します。不足していても、`gh` やリポジトリ状態から安全に特定できる場合は補完してよいです。

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 操作種別。例: 新規登録、本文更新、チェックON/OFF、コメント、Close、Reopen、親子関係更新、Issueテンプレート作成。
- 新規作成や更新に使うタイトル、本文、コメント。
- ラベル、担当者、マイルストーン。
- 親Issue、子Issue、sub-issueとして扱うIssue番号またはURL。
- チェックボックス更新対象の文言と、ON/OFFのどちらにするか。
- Issueテンプレートの場合は、テンプレート名、用途、必須項目、既存テンプレートとの関係。

## 安全手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。認証が必要な場合はユーザーに明示し、トークン、認証URL内の秘密情報、個人アクセストークンをチャットやログに出力しません。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で、対象リポジトリが正しいことを確認します。
3. 既存Issueを変更する前に、必ず `gh issue view <number-or-url> --repo OWNER/REPO --comments` で現在状態、本文、コメント、Close状態、ラベル、担当者を読みます。
4. 親子関係やIssueテンプレートを扱う場合は、実行前に `gh issue <command> --help`、`gh help issue`、または公式ドキュメントで現在のCLI仕様を確認します。CLIやGitHub側の仕様差分がある場合は、推測で実行しません。
5. 変更内容がIssue本文、Close/Reopen、親子関係、テンプレート構造のように影響が大きい場合は、実行前に差分または実行予定コマンドを短く説明します。
6. 実行後は `gh issue view` やファイル内容の確認で、意図した状態になったことを検証します。

## 実行方針

- Issueの新規作成は `gh issue create --repo OWNER/REPO --title ... --body ...` または `--body-file` を優先します。
- 既存Issueの更新は `gh issue edit <number-or-url> --repo OWNER/REPO` を使います。本文が長い場合やチェックボックス更新では `--body-file` を使います。
- コメント追加は `gh issue comment <number-or-url> --repo OWNER/REPO --body ...` または `--body-file` を使います。
- Closeは `gh issue close <number-or-url> --repo OWNER/REPO`、Reopenは `gh issue reopen <number-or-url> --repo OWNER/REPO` を使います。
- 現在状態の確認は `gh issue view <number-or-url> --repo OWNER/REPO --json ...` または通常表示を使います。
- 親子関係は、利用できるCLI仕様を確認したうえで、`gh issue create --parent`、`gh issue edit --parent`、`--add-sub-issue`、`--remove-sub-issue`、`--remove-parent` を使う前提で進めます。使えない環境では代替手段を確認し、ユーザーに明示します。
- ラベル、担当者、マイルストーンの変更は `gh issue edit` の対応オプションを使い、対象名の誤りがないか事前に確認します。

## チェックボックス更新

Issue本文のチェックボックスをON/OFFするときは、本文全体を雑に置換しません。

1. `gh issue view <number-or-url> --repo OWNER/REPO --json body --jq .body` などで本文を取得します。
2. 対象文言に一致する `- [ ]` または `- [x]` の行だけを探します。
3. 一致が0件、複数件、または文言が曖昧な場合はユーザーに確認します。
4. 対象行だけを `- [ ]` から `- [x]`、または `- [x]` から `- [ ]` に差し替えます。大文字の `- [X]` がある場合は既存の意味を確認してから統一します。
5. 更新本文を一時ファイルに保存し、`gh issue edit <number-or-url> --repo OWNER/REPO --body-file <file>` で反映します。
6. 反映後に `gh issue view` で対象行だけが変わったことを確認します。

## Issueテンプレート

Issueテンプレートを作る場合は、対象リポジトリ内の `.github/ISSUE_TEMPLATE/` を確認してから作業します。

- 既存テンプレートがある場合は、命名、フロントマター、項目構成、言語、ラベル指定、assignees指定のスタイルを合わせます。
- Markdownテンプレートは `.github/ISSUE_TEMPLATE/<name>.md` に作成します。
- Issue Formsは `.github/ISSUE_TEMPLATE/<name>.yml` または `.yaml` に作成します。
- テンプレート名はASCIIのkebab-caseを基本にし、用途が分かる名前にします。
- 必須項目、説明文、プレースホルダーには秘密情報や認証情報を入力させないよう明記します。
- 既存のテンプレート設定や公開設定を変える場合は、事前にユーザーの明示確認を取ります。

## 出力形式

作業完了時は、次を簡潔に報告します。

- 対象リポジトリと対象Issueまたは作成したテンプレートファイル。
- 実行した操作。例: 新規登録、本文更新、チェックON、コメント追加、Close、Reopen、親子関係追加。
- 確認した現在状態。
- 実行できなかったこと、認証や権限の不足、CLI仕様差分があればその理由。

## 禁止事項と確認が必要な操作

- Issue削除、Issueロック、Issue transfer、project変更、リポジトリ公開設定変更、リモート作成は、ユーザーの明示なしに行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- 対象リポジトリやIssueが確定していない状態で変更操作を実行しません。
- チェックボックスの曖昧一致や複数候補を、推測で更新しません。
- 既存Issue本文やテンプレートの無関係な整形、言い換え、項目削除を勝手に行いません。
- ネットワークアクセスとGitHub認証が必要な操作では、失敗時に認証、権限、対象repo、CLI仕様のどれが原因かを切り分けて報告します。
