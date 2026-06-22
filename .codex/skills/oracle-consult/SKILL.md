---
name: oracle-consult
description: "Oracle CLI (`@steipete/oracle`) で外部モデルへ相談するときに使う。送信前の render/dry-run、匿名化サマリ作成、第三者npm・ブラウザ操作・秘密情報リスク評価、Oracle session の回収まで扱う。"
---

# Oracle Consult

Oracle CLIで外部モデルへ相談し、送信内容の確認から結果回収まで進めるためのSkillです。便利さより漏洩防止を優先します。

## 基本方針

- 原則として、生ファイルを直接送らず `/private/tmp/<slug>.md` に匿名化サマリを作ります。
- 送信前に必ず `--render --render-plain` または `--dry-run summary` で送信内容を確認します。
- 秘密情報、個人情報、APIキー、トークン、秘密鍵、認証済みセッション、広すぎるglob、不要なローカル絶対パスが混じる場合は送信しません。
- リスクが低いと判断できる場合だけ、Oracleで送信して結果を回収します。
- 少しでも心配があれば、何が危ないかを短く示してユーザー確認を取ります。

## 期待する入力

- Oracleに聞きたい質問。
- 相談対象のファイル、ディレクトリ、または要約元。
- 外部送信してよい範囲。
- 必要なら使いたいslug。未指定なら内容から3-5語のASCII kebab-caseで作ります。

## 実行手順

1. 相談目的と外部送信してよい範囲を確認します。
2. 対象に秘密情報や広すぎる範囲がないか、`rg --files` や目視で確認します。
3. 原則として `/private/tmp/<slug>.md` に匿名化サマリを作ります。サマリには具体的なホスト名、鍵名、アカウント名、token、secret、cookie、認証済みURLを入れません。
4. 送信内容を確認します。

```bash
npx -y @steipete/oracle --render --render-plain \
  --prompt "<質問>" \
  --file /private/tmp/<slug>.md
```

5. render結果に危険な内容があれば止め、問題箇所を伏せたうえでユーザーに確認します。
6. 問題なければ送信します。

```bash
npx -y @steipete/oracle \
  --prompt "<質問>" \
  --file /private/tmp/<slug>.md \
  --slug <slug>
```

7. ChatGPTのモデルセレクタ検出で失敗した場合は、現在選択中モデルを使って再試行します。

```bash
npx -y @steipete/oracle \
  --prompt "<質問>" \
  --file /private/tmp/<slug>.md \
  --slug <slug> \
  --browser-model-strategy current \
  --force
```

8. Chrome切断、タイムアウト、長時間実行、途中終了に見える場合は、同じslugのsessionを回収します。

```bash
npx -y @steipete/oracle session <slug> --harvest
```

## リスク判定

送信してよい例:
- `/private/tmp` に作った短い匿名化サマリ。
- 公開済みREADMEや公開前提の仕様メモ。
- ユーザーが明示的に外部送信OKと言った小さなファイル。

送信前に確認が必要な例:
- `Agents.md`、運用台帳、サーバー構成メモ、バックアップ一覧。
- `~/.ssh`、`.env`、config、auth、cookie、session、token、secretを含み得るファイル。
- `--file .`、`--file "**/*"`、リポジトリ全体、`backups/`、`private/`。
- Chrome/ChatGPT/Geminiなど認証済みブラウザセッションに依存する実行。

## 出力形式

- 実行したOracleコマンドの要約。
- 送信したファイルの範囲。
- セッションslug。
- Oracle回答の要点。
- 送信しなかったもの、または確認で止めた理由。

## 禁止事項

- render確認なしに外部送信しません。
- 秘密情報候補をそのままプロンプト、ログ、報告文に貼りません。
- ユーザー確認なしに生の運用台帳やバックアップ配下を送信しません。
- `npx -y @steipete/oracle` は第三者npmコード実行なので、必要に応じてネットワーク・ブラウザ操作・権限付き実行の承認を取ります。
