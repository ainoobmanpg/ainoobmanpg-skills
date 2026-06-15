# ainoobmanpg-skills

日本語でCodex用およびClaude Code用のSkillを整備するリポジトリです。

## 目的

- よく使う作業手順をSkillとして再利用できる形にする。
- CodexとClaude Codeの両方で使える日本語の作業ガイドを管理する。
- 破壊的操作、認証、ネットワーク利用などの注意点をSkill単位で明確にする。

## 配置

```text
.codex/skills/      Codex向けSkill
.claude/skills/     Claude Code向けSkill
AGENTS.md           Codex向けの作業規約
CLAUDE.md           Claude Code向けの作業規約
```

## Skillの最小構成

```text
<tool>/skills/<skill-name>/
  SKILL.md
```

`SKILL.md` は次の形式にします。

```markdown
---
name: skill-name
description: "このSkillが必要になる依頼、対象、ユーザーが言いそうな語彙を一文で説明する。"
---

# Skill Name

このSkillが何をするか、いつ使うか、手順、出力、禁止事項を書く。
```

## 運用メモ

- Skill名はASCIIのkebab-caseにする。
- 説明文と本文は日本語で書く。
- `description` はSkill本文が読まれる前の主要な発火条件として扱う。
- `description` には概要だけでなく、いつ使うか、対象リソース、ユーザーが使いそうな言い回し、近いSkillとの切り分けを短く含める。
- `description` にコロン、バッククォート、記号、箇条書き風の表現を含める場合は、YAMLの型事故を避けるため引用符で囲む。
- サンプルやテンプレートに秘密情報を含めない。
- 外部API、ネットワーク、削除、Git操作、認証が必要な場合は本文に明記する。
