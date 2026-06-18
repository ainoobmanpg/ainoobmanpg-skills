---
name: chatgot-codex-proxy
description: "ChatGotから自作Codex proxyを使う設定や、OpenAI Chat Completions互換APIとして `chatgot-codex` を叩く依頼で使う。個人利用限定、Base URL、API key、session key、stream、Private network内限定、SaaS・チーム共有・URL共有・利用制限/課金/認証迂回に見える用途の判定で使う。"
---

# ChatGot Codex Proxy

ChatGotやcurlから自作Codex proxyをOpenAI Chat Completions互換APIとして使うためのSkillです。proxyは個人の利便性ツールとして扱い、Private network内からだけ到達する前提で、外部ネットからは叩けません。実際のBase URLはローカルのChatGot設定や環境変数から確認し、公開Skill本文に固定しません。

## 使うべき状況

- ChatGotにCodex proxyを登録したい。
- OpenAI Chat Completions互換APIとしてCodex proxyをcurlや既存クライアントから叩きたい。
- `chatgot-codex`、Base URL、API key、stream、session keyの指定方法を確認したい。
- 同じCodex threadを継続するための `user` 値を決めたい。
- Private network内限定の到達性や接続失敗を切り分けたい。
- 用途が個人利用の範囲に収まるか、SaaS、チーム共有、他人へのURL共有、利用制限・課金・認証の迂回に見えないかを判定したい。

## 期待する入力

- 使うクライアント。例: ChatGot、curl、OpenAI互換SDK。
- 用途。自分だけが使うのか、他人、チーム、顧客、SaaS、業務サービスから使うのか。
- 認証、課金、監査、利用制限、ログ管理が必要な文脈かどうか。
- 継続したいsession key。未指定なら、用途が分かるASCIIの短い値を `user` に入れます。
- streamが必要かどうか。
- 接続エラーがある場合は、実行したコマンド、HTTPステータス、エラーメッセージ。

## 利用可否の判断ライン

接続設定やcurl例を出す前に、用途をこの順で判定します。

- OK: 自分だけがローカルで使う。ChatGotから自分のCodexを呼ぶ。
- 条件付きOK: 自分専用のPrivate network内運用。URLや利用権を他人に渡さず、個人利用に閉じる。
- NG: 他人にURLを渡す。
- NG: チーム共有で使う。
- NG: SaaSや業務サービスの裏側で使う。
- NG: 利用制限、課金、認証を迂回しているように見える使い方。

NGに見える場合は、接続先URL、curl例、ChatGot設定手順を出しません。OpenAI APIなど契約、課金、監査できる経路、または `codexappserver` のような正規の実行基盤へ寄せるよう案内します。

## 接続設定

このセクションは、用途がOKまたは条件付きOKと判断できる場合だけ使います。

- Base URL: ローカルのChatGot設定、または `CHATGOT_CODEX_BASE_URL` で管理します。例: `http://<private-proxy-host>:8788/v1`
- Chat Completions endpoint: `POST ${CHATGOT_CODEX_BASE_URL}/chat/completions`
- API key / Authorization: 任意の文字列でよい。例: `dummy`
- model: 任意。ChatGotでは `chatgot-codex` を使う。server側に `CODEX_MODEL` があればそちらが優先されます。
- user: session key。同じ `user` ならCodex threadを継続します。

ChatGotでは、Base URLにローカルで管理しているPrivate network内のproxy URL、API keyに適当な文字列、modelに `chatgot-codex` を指定します。

## 最小リクエスト

用途がOKまたは条件付きOKと判断できる場合だけ、この例を出します。

```bash
curl -sS "${CHATGOT_CODEX_BASE_URL}/chat/completions" \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer dummy' \
  -d '{
    "model": "chatgot-codex",
    "user": "my-chatgot-session",
    "messages": [
      { "role": "user", "content": "こんにちは。一言で返して" }
    ]
  }'
```

## streamリクエスト

用途がOKまたは条件付きOKと判断できる場合だけ、この例を出します。

```bash
curl -N "${CHATGOT_CODEX_BASE_URL}/chat/completions" \
  -H 'Content-Type: application/json' \
  -H 'Authorization: Bearer dummy' \
  -d '{
    "model": "chatgot-codex",
    "stream": true,
    "user": "my-chatgot-session",
    "messages": [
      { "role": "user", "content": "3つ箇条書きで返して" }
    ]
  }'
```

## 失敗時の切り分け

1. Private network内から実行しているか確認します。外部ネットからの到達不可は想定動作です。
2. Base URLに `/v1` まで含めているか確認します。
3. endpointが `/v1/chat/completions` になっているか確認します。
4. `Authorization` は `Bearer dummy` など任意値でよく、proxy側では無視される前提で扱います。
5. 会話が継続しない場合は、同じ `user` を送っているか確認します。
6. streamが途切れる場合は、まず `stream: false` または未指定の最小リクエストで疎通確認します。

## 出力形式

- まず用途判定を出します。例: `OK`, `条件付きOK`, `NG`。
- OKまたは条件付きOKの場合だけ、ChatGot設定値、curl例、SDK設定例のうち依頼に必要なものを出します。
- NGの場合は接続設定を出さず、正規API、事業者向け契約、監査可能な実行基盤への代替案を出します。
- session継続が重要な場合は、使う `user` 値を明記します。
- 接続失敗の相談では、Private network到達性、URL、endpoint、`user`、stream有無を分けて判定します。

## 禁止事項と確認が必要な操作

- 実在するAPIキーや秘密情報を出力しません。
- 外部公開URLとして案内しません。
- 他人へのURL共有、チーム共有、SaaSや業務サービスの裏側、利用制限・課金・認証の迂回に見える用途では、接続設定やcurl例を案内しません。
- ユーザーが求めていない限り、proxyサーバーの設定変更、再起動、デプロイ、永続化作業へ進みません。
- 疎通テストでネットワーク実行が必要な場合は、実行前に対象URLと送信内容を明示します。
