---
name: codex-skill-sync-global
description: repo内のCodex Skillをグローバルな `~/.codex/skills` に同期するときに使う。同期元、同期先、差分確認、外部ディレクトリへの書き込み確認を日本語で明示する。
---

# Codex Skill Sync Global

repo内のCodex SkillをグローバルなCodex Skill配置先へ同期するためのSkillです。同期元と同期先を確認し、コピー後に一致確認を行います。

## 共通方針

- 同期元は原則としてrepo内の `.codex/skills/<skill-name>/SKILL.md` です。
- 同期先は原則として `${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>/SKILL.md` です。
- 同期結果、差分確認、報告文は日本語で書きます。

## 使うべき状況

- repoで整備したCodex Skillをグローバルに使えるようにしたい。
- repo版とグローバル版のSkillが一致しているか確認したい。
- 複数Skillをまとめて `~/.codex/skills` へ反映したい。

## 期待する入力

- 同期するSkill名。
- repo root。
- 必要なら同期先の明示パス。未指定なら `${CODEX_HOME:-$HOME/.codex}/skills` を使います。
- 上書きしてよいかの確認。

## 実行手順

1. repo rootを確認し、`.codex/skills/<skill-name>/SKILL.md` が存在することを確認します。
2. 同期先パスを `${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>/SKILL.md` として展開し、対象を表示します。
3. 同期先が既に存在する場合は、`diff -u` や `cmp -s` で差分有無を確認します。
4. workspace外のグローバルディレクトリへ書き込むため、必要な権限や承認があるか確認します。
5. `mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>"` で同期先ディレクトリを用意します。
6. `cp .codex/skills/<skill-name>/SKILL.md "${CODEX_HOME:-$HOME/.codex}/skills/<skill-name>/SKILL.md"` でコピーします。
7. `cmp -s` または `diff -q` でrepo版とグローバル版が一致することを確認します。

## 出力形式

- 同期したSkill名。
- 同期元と同期先の絶対パス。
- 上書き有無。
- 一致確認の結果。

## 禁止事項と確認が必要な操作

- `.claude/skills` への同期は、このSkillでは扱いません。
- 明示依頼なしにグローバル側の余分なSkill削除や一括削除を行いません。
- 同期元が存在しない、またはSkill名が曖昧なままコピーしません。
- workspace外への書き込みが必要な場合は、実行前に承認や権限を確認します。
- 認証情報、APIキー、個人情報を含むファイルを同期対象にしません。
