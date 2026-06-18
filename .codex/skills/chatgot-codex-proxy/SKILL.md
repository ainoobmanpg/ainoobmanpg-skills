---
name: chatgot-codex-proxy
description: "ChatGotから自作Codex proxyを使う設定や、OpenAI Chat Completions互換APIとして `chatgot-codex` を叩く依頼で使う。Base URL、API key、session key、stream、Private network内限定の接続確認で使う。"
---

# ChatGot Codex Proxy

ChatGotやcurlから自作Codex proxyをOpenAI Chat Completions互換APIとして使うためのSkillです。proxyはPrivate network内からだけ到達する前提で、外部ネットからは叩けません。実際のBase URLはローカルのChatGot設定や環境変数から確認し、公開Skill本文に固定しません。

## 使うべき状況

- ChatGotにCodex proxyを登録したい。
- OpenAI Chat Completions互換APIとしてCodex proxyをcurlや既存クライアントから叩きたい。
- `chatgot-codex`、Base URL、API key、stream、session keyの指定方法を確認したい。
- 同じCodex threadを継続するための `user` 値を決めたい。
- Private network内限定の到達性や接続失敗を切り分けたい。

## 期待する入力

- 使うクライアント。例: ChatGot、curl、OpenAI互換SDK。
- 継続したいsession key。未指定なら、用途が分かるASCIIの短い値を `user` に入れます。
- streamが必要かどうか。
- 接続エラーがある場合は、実行したコマンド、HTTPステータス、エラーメッセージ。

## 接続設定

- Base URL: ローカルのChatGot設定、または `CHATGOT_CODEX_BASE_URL` で管理します。例: `http://<private-proxy-host>:8788/v1`
- Chat Completions endpoint: `POST ${CHATGOT_CODEX_BASE_URL}/chat/completions`
- API key / Authorization: 任意の文字列でよい。例: `dummy`
- model: 任意。ChatGotでは `chatgot-codex` を使う。server側に `CODEX_MODEL` があればそちらが優先されます。
- user: session key。同じ `user` ならCodex threadを継続します。

ChatGotでは、Base URLにローカルで管理しているPrivate network内のproxy URL、API keyに適当な文字列、modelに `chatgot-codex` を指定します。

## 最小リクエスト

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

- ChatGot設定値、curl例、SDK設定例のうち、依頼に必要なものだけを出します。
- session継続が重要な場合は、使う `user` 値を明記します。
- 接続失敗の相談では、Private network到達性、URL、endpoint、`user`、stream有無を分けて判定します。

## 禁止事項と確認が必要な操作

- 実在するAPIキーや秘密情報を出力しません。
- 外部公開URLとして案内しません。
- ユーザーが求めていない限り、proxyサーバーの設定変更、再起動、デプロイ、永続化作業へ進みません。
- 疎通テストでネットワーク実行が必要な場合は、実行前に対象URLと送信内容を明示します。
