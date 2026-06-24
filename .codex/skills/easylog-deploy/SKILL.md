---
name: easylog-deploy
description: "EasyLog private版をMinafos/prod-nas本番へデプロイ、再起動、検証、ロールバックするときに使う。EasyLog、prod-nas、prodnas、Minafos、Tailscale、9443、本番反映、deploy-private.shが出たら使う。"
---

# EasyLog Deploy

EasyLog private版を本番 `prod-nas` へ安全に反映するための手順です。repoだけで判断せず、実ホスト、実サービス、実URLを確認します。

## 使うべき状況

- EasyLogの変更を本番へデプロイ、再起動、反映、検証、ロールバックする。
- ユーザーが `prod-nas`、`prodnas`、Minafos、Tailscale、`9443`、EasyLog本番を話題にしている。
- PR merge後のコミットを本番へ反映する。

## 本番の固定情報

- SSH alias: `prod-nas`。`prodnas` と言われても、まず `prod-nas` を試す。
- hostname: `prod-nas`
- 本番checkout: `/tank/projects/prod/easylog`
- systemd service: `easylog.service`
- backup timer: `easylog-private-backup.timer`
- local health: `http://127.0.0.1:3001/`
- Tailscale URL: `https://prod-nas.tail979ed9.ts.net:9443/`
- デプロイスクリプト: `/tank/projects/prod/easylog/ops/ubuntu/deploy-private.sh`

古い値として `/tank/projects/apps/easylog`、`ubuntu-i9-9900`、`prodnas`、Mac用 `ops/macos/deploy-local.sh` が混ざりやすい。EasyLog本番では使わない。

## 事前確認

1. ローカルrepoで `git status --short --branch` を確認し、デプロイ対象SHAを決める。
2. PR/merge後なら、mutableなbranch名ではなくmerge commit SHAを使う。
3. 本番をread-firstで確認する。

```bash
ssh -o BatchMode=yes -o ConnectTimeout=8 prod-nas 'hostnamectl --static; whoami; cd /tank/projects/prod/easylog && git status --short --branch && git log -1 --oneline; systemctl is-active easylog || true; systemctl is-active easylog-private-backup.timer || true; systemctl cat easylog.service | sed -n "1,120p"; tailscale serve status'
```

## No-Go

以下があればデプロイしない。

- SSH先hostnameが `prod-nas` ではない。
- `/tank/projects/prod/easylog` が存在しない、または別repoに見える。
- 本番checkoutに未コミット変更がある。
- service unitの `User`/`Group` が `linux3911` ではない。
- service unitの `WorkingDirectory` が `/tank/projects/prod/easylog` ではない。
- Tailscale `:9443` が `127.0.0.1:3001` へ向いていない。
- CI/test/buildの既知失敗が残っている。
- デプロイ対象SHAがmerge済みか確認できない。

## デプロイ

デプロイはpinしたSHAで実行する。

```bash
ssh -o BatchMode=yes -o ConnectTimeout=8 prod-nas 'cd /tank/projects/prod/easylog && ./ops/ubuntu/deploy-private.sh --ref <merge-commit-sha>'
```

`--ref main` のようなmutable refは、ユーザーが明示しない限り使わない。script側で拒否された場合も `--allow-mutable-ref` で突破しない。

## 事後確認

```bash
ssh -o BatchMode=yes -o ConnectTimeout=8 prod-nas 'cd /tank/projects/prod/easylog && git rev-parse HEAD; systemctl is-active easylog; systemctl is-active easylog-private-backup.timer; systemctl cat easylog.service | sed -n "1,120p"; curl -fsSI http://127.0.0.1:3001/ | sed -n "1,5p"'
curl -fsSI https://prod-nas.tail979ed9.ts.net:9443/ | sed -n '1,8p'
curl -fsS https://prod-nas.tail979ed9.ts.net:9443/api/ai-assist
```

確認すること。

- 本番HEADがデプロイ対象SHAと一致する。
- `easylog.service` が `active`。
- `easylog-private-backup.timer` が `active`。
- local healthとTailscale URLがHTTP 200を返す。
- `/api/ai-assist` は `{"configured":false}` でもデプロイ失敗ではない。AI補助proxy env未設定という意味で報告する。
- `npm audit` 警告が出た場合は件数とseverityだけ報告し、デプロイ成否とは分ける。

## ロールバック

直前の安定SHAを確認して、同じscriptをpinしたSHAで実行する。

```bash
ssh -o BatchMode=yes -o ConnectTimeout=8 prod-nas 'cd /tank/projects/prod/easylog && git log --oneline -n 5'
ssh -o BatchMode=yes -o ConnectTimeout=8 prod-nas 'cd /tank/projects/prod/easylog && ./ops/ubuntu/deploy-private.sh --ref <previous-good-sha>'
```

## 報告形式

- デプロイ対象SHA
- 実行した本番hostとcheckout
- service/timer状態
- Tailscale URLのHTTP結果
- AI補助のconfigured状態
- 残った警告や次の作業

## 禁止事項

- 本番反映にMac用local deploy scriptを使わない。
- `ops/ubuntu/deploy.sh` と `deploy-private.sh` を取り違えない。
- public-demoへ反映しない。依頼がpublic-demoなら別手順として確認する。
- `.env` やsecret値を表示・貼り付けしない。
- 本番envやWebhook secretを、デプロイついでに変更しない。
