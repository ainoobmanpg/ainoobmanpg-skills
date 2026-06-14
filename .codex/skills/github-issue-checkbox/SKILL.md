---
name: github-issue-checkbox
description: GitHub Issue本文内のチェックボックスをONまたはOFFにするときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、報告は指定がない限り日本語で行う。
---

# GitHub Issue Checkbox

GitHub Issue本文内のチェックボックスを安全にON/OFFするためのSkillです。GitHub CLI `gh` を主経路にし、現在本文を読み取ってから、対象行だけを更新します。

## 共通方針

- GitHub Issue本文内チェックボックス更新では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- 既存本文の言語を尊重し、報告文はユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- Issue本文内のチェックボックスをONにしたい。
- Issue本文内のチェックボックスをOFFにしたい。
- 本文全体ではなく、特定のチェック項目だけを更新したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Issue番号またはIssue URL。
- 更新対象のチェックボックス文言。
- ON/OFFのどちらにするか。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh issue view <number-or-url> --repo OWNER/REPO --json body --jq .body` などで現在本文を取得します。
4. 対象文言に一致する `- [ ]` または `- [x]` の行だけを探します。
5. 一致が0件、複数件、または文言が曖昧な場合はユーザーに確認します。
6. 対象行だけを `- [ ]` から `- [x]`、または `- [x]` から `- [ ]` に差し替えます。
7. 更新本文を一時ファイルに保存し、`gh issue edit <number-or-url> --repo OWNER/REPO --body-file <file>` で反映します。
8. 反映後に `gh issue view <number-or-url> --repo OWNER/REPO` で対象行だけが変わったことを確認します。

## 出力形式

- 対象リポジトリと対象Issue。
- 変更したチェックボックス文言とON/OFFの結果。
- 更新後に確認した現在状態。

## 禁止事項と確認が必要な操作

- 本文全体を雑に置換しません。
- チェックボックスの曖昧一致や複数候補を、推測で更新しません。
- このSkillでコメント追加、Close/Reopen、親子関係変更は行いません。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、対象repo、対象Issueを切り分けて報告します。
