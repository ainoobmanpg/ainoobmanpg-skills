---
name: github-release-create
description: GitHub Releaseを新規作成するときに使う。CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先し、Releaseタイトル、本文、報告文は指定がない限り日本語にする。
---

# GitHub Release Create

GitHub Releaseを新規作成するためのSkillです。GitHub CLI `gh` を主経路にし、対象repo、tag、target、公開状態、release notesを確認してから作成します。

## 共通方針

- GitHub Release作成では、CodexのGitHub Plugin/connectorよりこのSkillとGitHub CLI `gh` を優先します。
- Releaseタイトル、本文、報告文は、ユーザーまたは対象repoの規約で別言語指定がない限り日本語で書きます。
- Plugin/connectorは、`gh` で不足する読み取りや構造化情報取得の補助に留めます。
- `gh auth status` がCodexサンドボックス内で失敗する場合は、即座に認証切れと判断せず `gh-execution-context` で実行環境を切り分けます。

## 使うべき状況

- 新しいtagに対してGitHub Releaseを作成したい。
- 既存tagまたは指定commitからdraft、prerelease、正式releaseを作成したい。

## 期待する入力

- 対象リポジトリの `OWNER/REPO` またはローカルリポジトリ。
- Release tag名。
- Release target。例: branch名、commit SHA。未指定なら対象repoの既定branchを確認します。
- Releaseタイトルと本文、または `--generate-notes` を使う指定。
- `draft`、`prerelease`、添付assetの有無。

## 実行手順

1. `gh auth status` でGitHub CLIの認証状態を確認します。
2. `gh repo view OWNER/REPO` またはローカルの `git remote -v` と `gh repo view` で対象リポジトリを確認します。
3. `gh release view <tag> --repo OWNER/REPO` や `git ls-remote --tags origin <tag>` で同名Releaseやtagの有無を確認します。
4. target、タイトル、本文、draft/prerelease指定、asset指定が揃っていることを確認します。
5. 公開Releaseを作る場合は、対象repo、tag、公開状態、release notesの内容を明示確認します。
6. 長文本文では一時ファイルを使い、`gh release create <tag> --repo OWNER/REPO --target <target> --title ... --notes-file ...` を実行します。自動生成が明示されている場合だけ `--generate-notes` を使います。
7. 必要な場合だけ `--draft`、`--prerelease`、指定済みassetパスを付けます。
8. 作成後に `gh release view <tag> --repo OWNER/REPO` で結果を確認します。

## 出力形式

- 対象リポジトリ、Release tag、Release URL。
- target、draft/prerelease有無、asset有無。
- 作成後に確認した状態。

## 禁止事項と確認が必要な操作

- 既存Releaseの上書き、削除、unpublishは行いません。
- tag名、target、公開状態、本文が曖昧なまま作成しません。
- 明示指定のないassetを添付しません。
- 公開Release、既存tag利用、tag新規作成を伴うため、対象と公開状態を明示確認してから実行します。
- 認証情報、APIキー、個人アクセストークン、認証済みセッションの内容を出力しません。
- ネットワークアクセスとGitHub認証が必要です。失敗時は認証、権限、tag、release状態、assetパスを切り分けて報告します。
