---
name: github-issue-template-create
description: "GitHub IssueテンプレートやIssue Formsを新規作成するときに使う。`.github/ISSUE_TEMPLATE` を追加して、テンプレ作って、フォームを作成して、という依頼で使い、`gh` を優先する。"
---

# GitHub Issue Template Create

`.github/ISSUE_TEMPLATE/` 配下にGitHub IssueテンプレートやIssue Formsを新規作成するためのSkillです。対象リポジトリの既存テンプレート構成を確認してから、用途に合うファイルを追加します。ローカル編集が中心ですが、必要に応じて GitHub CLI `gh` で対象repoや既存運用を確認します。

## 共通方針

- GitHub Issueテンプレート作成では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- テンプレート名、説明、フォーム項目、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- 新しいIssueテンプレートを追加したい。
- 新しいIssue Formを追加したい。

## 期待する入力

- 対象リポジトリのパス。
- テンプレート名。
- 用途、想定読者、必須項目。
- MarkdownテンプレートかIssue Formかの指定。

## 実行手順

1. 対象リポジトリ内の `.github/ISSUE_TEMPLATE/` と関連設定を確認します。
2. 必要なら `gh auth status` と `gh repo view OWNER/REPO` で、対象repoと認証状態を確認します。
3. 既存テンプレートがある場合は、命名、言語、フロントマター、項目構成のスタイルを確認します。
4. テンプレート名はASCIIのkebab-caseを基本にし、用途が分かる名前にします。
5. Markdownテンプレートなら `.md`、Issue Formなら `.yml` または `.yaml` で新規作成します。
6. 必須項目、説明文、プレースホルダーには秘密情報や認証情報を入力させないよう明記します。
7. 作成後に対象ファイル内容を確認し、既存構成と矛盾しないことを検証します。

## 出力形式

- 対象リポジトリと作成したテンプレートファイル。
- テンプレート種別。例: Markdownテンプレート、Issue Form。
- 反映した主要項目と確認結果。

## 禁止事項と確認が必要な操作

- このSkillで既存テンプレートの上書き更新は行いません。
- 既存のテンプレート設定や公開設定を変える場合は、事前にユーザーの明示確認を取ります。
- 秘密情報、APIキー、個人情報、認証済みセッションの内容をテンプレートへ含めません。
- GitHub上の既存運用確認や反映確認には、ネットワークアクセスとGitHub認証が必要になる場合があります。
- リポジトリのGit操作、push、公開設定変更は、ユーザーの明示なしに行いません。
