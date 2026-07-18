# Global Codex Skill Registry

最終確認日: 2026-07-18

この台帳は `~/.codex/skills/` に入っているグローバルCodex Skillの管理元を確認するためのメモです。

## 使い方

- `repo管理` はこのリポジトリの `.codex/skills/` に同名Skillがあるもの。
- `globalのみ` は現時点でこのリポジトリ内に同名Skillがないもの。
- `原本確認` は同名Skillが別Repo内にもあり、global版との一致を確認できたもの。
- `関連先確認` はSkill本文から対象Repoや関連URLは分かるが、Skill原本Git URLとは断定しないもの。
- `未整理` は移植元、作成元、今後このRepoへ取り込むかを別途確認します。

## 管理元サマリ

| 種別 | 件数 | メモ |
| --- | ---: | --- |
| repo管理 | 54 | `git@github.com:ainoobmanpg/ainoobmanpg-skills.git` |
| globalのみ | 13 | 原本確認 3、関連先確認 8、未整理 2 |

## Globalのみ

| Skill | Global path | 状態 | Git URL / 関連URL | 根拠 |
| --- | --- | --- | --- | --- |
| `chronicle` | `~/.codex/skills/chronicle/SKILL.md` | 関連先確認 | `https://github.com/openai/codex` | 本文にChronicle memoryのoriginal implementationとして記載。Skill原本とは未断定。 |
| `codex-app-server-execution-context` | `~/.codex/skills/codex-app-server-execution-context/SKILL.md` | 原本確認 | `git@github.com:ainoobmanpg/codex-agmsg-monitor.git` | `/Users/macbook_3911/github/codex-agmsg-monitor/.codex/skills/` に同名Skillあり。global版と一致確認済み。 |
| `goal-setter` | `~/.codex/skills/goal-setter/SKILL.md` | 関連先確認 | `https://github.com/gotalab/goal-setter-skill` | READMEのCodex Skill導入先が `skills/goal-setter` で、global版と同名Skill・同名 `scripts/validate_goal_length.py` を確認。ローカル版には `agents/openai.yaml` も同梱。 |
| `gpu-homebase-access` | `~/.codex/skills/gpu-homebase-access/SKILL.md` | 関連先確認 | なし | メモリに「personal Codex skill」として作成記録あり。本文は `server-helper` 文脈だが同Repoにoriginなし。 |
| `headroom-auto` | `~/.codex/skills/headroom-auto/SKILL.md` | 関連先確認 | なし | 本文は `/Users/macbook_3911/github/pc-helper-mac/` 配下のHeadroom scriptsを参照。同Repoにoriginなし。 |
| `home-network-evidence-capture` | `~/.codex/skills/home-network-evidence-capture/SKILL.md` | 未整理 | なし | 同名のrepo内原本を確認できていない。 |
| `mac-windows-codex-sync` | `~/.codex/skills/mac-windows-codex-sync/SKILL.md` | 原本確認 | `git@github.com:ainoobmanpg/infra-helper.git` | `/Users/macbook_3911/github/infra-helper/.codex/skills/` の同名Skillと一致確認済み。 |
| `report-skills` | `~/.codex/skills/report-skills/SKILL.md` | 関連先確認 | なし | グローバル直置きのユーザー定義Skill。`agents/openai.yaml` あり。2026-06-05作成。本文と補助YAML以外にローカル原本は未発見。 |
| `sdneo-windows-generation-smoke` | `~/.codex/skills/sdneo-windows-generation-smoke/SKILL.md` | 関連先確認 | `git@github.com:ainoobmanpg/danbot-ja-sdxl-prompt-eval.git`, `git@github.com:ainoobmanpg/sdneo-ja-prompt.git` | 本文はWindows GPU host上のForge Neo/SD Neo確認。`danbot-ja-sdxl-prompt-eval` と `sdneo-ja-prompt` を参照。Skill原本とは未断定。 |
| `tabemonosearchkun-deploy` | `~/.codex/skills/tabemonosearchkun-deploy/SKILL.md` | 関連先確認 | `git@github.com:ainoobmanpg/tabemonosearchkun.git` | 本文の対象が `tabemonosearchkun`。同Repoのoriginを確認。Skill原本とは未断定。 |
| `vrchat-avatar-workflow` | `~/.codex/skills/vrchat-avatar-workflow/SKILL.md` | 原本確認 | `git@github.com:ainoobmanpg/vrchat-avatar-mods.git` | `skills/vrchat-avatar-workflow/` に同名原本あり。global版には未反映の更新あり。 |
| `whichllm` | `~/.codex/skills/whichllm/SKILL.md` | 未整理 | なし | 同名のrepo内原本を確認できていない。 |
| `windows-forge-neo-runner` | `~/.codex/skills/windows-forge-neo-runner/SKILL.md` | 関連先確認 | `git@github.com:ainoobmanpg/danbot-ja-sdxl-prompt-eval.git`, `git@github.com:ainoobmanpg/sdneo-ja-prompt.git` | 2026-06-26作成。メモリに同名Skillがあるがglobal版とは一致しない。本文は `danbot-ja-sdxl-prompt-eval` を作業起点にし、`sdneo-ja-prompt` のForge/Neo検証にも使う。Repo内原本は未発見。 |

## Repo管理

Git URL: `git@github.com:ainoobmanpg/ainoobmanpg-skills.git`

| Skill | Global path | Repo path |
| --- | --- | --- |
| `ai-task-delegator` | `~/.codex/skills/ai-task-delegator/SKILL.md` | `.codex/skills/ai-task-delegator/SKILL.md` |
| `away-mode-handoff` | `~/.codex/skills/away-mode-handoff/SKILL.md` | `.codex/skills/away-mode-handoff/SKILL.md` |
| `chatgot-codex-proxy` | `~/.codex/skills/chatgot-codex-proxy/SKILL.md` | `.codex/skills/chatgot-codex-proxy/SKILL.md` |
| `cloak-browser-search` | `~/.codex/skills/cloak-browser-search/SKILL.md` | `.codex/skills/cloak-browser-search/SKILL.md` |
| `cognitive-rhythm-writing` | `~/.codex/skills/cognitive-rhythm-writing/SKILL.md` | `.codex/skills/cognitive-rhythm-writing/SKILL.md` |
| `codex-skill-sync-global` | `~/.codex/skills/codex-skill-sync-global/SKILL.md` | `.codex/skills/codex-skill-sync-global/SKILL.md` |
| `easylog-deploy` | `~/.codex/skills/easylog-deploy/SKILL.md` | `.codex/skills/easylog-deploy/SKILL.md` |
| `gh-execution-context` | `~/.codex/skills/gh-execution-context/SKILL.md` | `.codex/skills/gh-execution-context/SKILL.md` |
| `git-commit` | `~/.codex/skills/git-commit/SKILL.md` | `.codex/skills/git-commit/SKILL.md` |
| `git-push-branch` | `~/.codex/skills/git-push-branch/SKILL.md` | `.codex/skills/git-push-branch/SKILL.md` |
| `github-actions-rerun` | `~/.codex/skills/github-actions-rerun/SKILL.md` | `.codex/skills/github-actions-rerun/SKILL.md` |
| `github-issue-add-sub-issue` | `~/.codex/skills/github-issue-add-sub-issue/SKILL.md` | `.codex/skills/github-issue-add-sub-issue/SKILL.md` |
| `github-issue-checkbox` | `~/.codex/skills/github-issue-checkbox/SKILL.md` | `.codex/skills/github-issue-checkbox/SKILL.md` |
| `github-issue-close` | `~/.codex/skills/github-issue-close/SKILL.md` | `.codex/skills/github-issue-close/SKILL.md` |
| `github-issue-comment` | `~/.codex/skills/github-issue-comment/SKILL.md` | `.codex/skills/github-issue-comment/SKILL.md` |
| `github-issue-create` | `~/.codex/skills/github-issue-create/SKILL.md` | `.codex/skills/github-issue-create/SKILL.md` |
| `github-issue-remove-parent` | `~/.codex/skills/github-issue-remove-parent/SKILL.md` | `.codex/skills/github-issue-remove-parent/SKILL.md` |
| `github-issue-remove-sub-issue` | `~/.codex/skills/github-issue-remove-sub-issue/SKILL.md` | `.codex/skills/github-issue-remove-sub-issue/SKILL.md` |
| `github-issue-reopen` | `~/.codex/skills/github-issue-reopen/SKILL.md` | `.codex/skills/github-issue-reopen/SKILL.md` |
| `github-issue-set-assignees` | `~/.codex/skills/github-issue-set-assignees/SKILL.md` | `.codex/skills/github-issue-set-assignees/SKILL.md` |
| `github-issue-set-labels` | `~/.codex/skills/github-issue-set-labels/SKILL.md` | `.codex/skills/github-issue-set-labels/SKILL.md` |
| `github-issue-set-milestone` | `~/.codex/skills/github-issue-set-milestone/SKILL.md` | `.codex/skills/github-issue-set-milestone/SKILL.md` |
| `github-issue-set-parent` | `~/.codex/skills/github-issue-set-parent/SKILL.md` | `.codex/skills/github-issue-set-parent/SKILL.md` |
| `github-issue-template-create` | `~/.codex/skills/github-issue-template-create/SKILL.md` | `.codex/skills/github-issue-template-create/SKILL.md` |
| `github-issue-template-update` | `~/.codex/skills/github-issue-template-update/SKILL.md` | `.codex/skills/github-issue-template-update/SKILL.md` |
| `github-issue-update-body` | `~/.codex/skills/github-issue-update-body/SKILL.md` | `.codex/skills/github-issue-update-body/SKILL.md` |
| `github-issue-view` | `~/.codex/skills/github-issue-view/SKILL.md` | `.codex/skills/github-issue-view/SKILL.md` |
| `github-label-create-update` | `~/.codex/skills/github-label-create-update/SKILL.md` | `.codex/skills/github-label-create-update/SKILL.md` |
| `github-pr` | `~/.codex/skills/github-pr/SKILL.md` | `.codex/skills/github-pr/SKILL.md` |
| `github-pr-check-ci` | `~/.codex/skills/github-pr-check-ci/SKILL.md` | `.codex/skills/github-pr-check-ci/SKILL.md` |
| `github-pr-check-review-comments` | `~/.codex/skills/github-pr-check-review-comments/SKILL.md` | `.codex/skills/github-pr-check-review-comments/SKILL.md` |
| `github-pr-close` | `~/.codex/skills/github-pr-close/SKILL.md` | `.codex/skills/github-pr-close/SKILL.md` |
| `github-pr-comment` | `~/.codex/skills/github-pr-comment/SKILL.md` | `.codex/skills/github-pr-comment/SKILL.md` |
| `github-pr-create` | `~/.codex/skills/github-pr-create/SKILL.md` | `.codex/skills/github-pr-create/SKILL.md` |
| `github-pr-merge` | `~/.codex/skills/github-pr-merge/SKILL.md` | `.codex/skills/github-pr-merge/SKILL.md` |
| `github-pr-reopen` | `~/.codex/skills/github-pr-reopen/SKILL.md` | `.codex/skills/github-pr-reopen/SKILL.md` |
| `github-pr-review` | `~/.codex/skills/github-pr-review/SKILL.md` | `.codex/skills/github-pr-review/SKILL.md` |
| `github-pr-update-body` | `~/.codex/skills/github-pr-update-body/SKILL.md` | `.codex/skills/github-pr-update-body/SKILL.md` |
| `github-pr-view` | `~/.codex/skills/github-pr-view/SKILL.md` | `.codex/skills/github-pr-view/SKILL.md` |
| `github-release-create` | `~/.codex/skills/github-release-create/SKILL.md` | `.codex/skills/github-release-create/SKILL.md` |
| `github-repo-create` | `~/.codex/skills/github-repo-create/SKILL.md` | `.codex/skills/github-repo-create/SKILL.md` |
| `github-repo-protect-branch` | `~/.codex/skills/github-repo-protect-branch/SKILL.md` | `.codex/skills/github-repo-protect-branch/SKILL.md` |
| `japanese-tech-writing` | `~/.codex/skills/japanese-tech-writing/SKILL.md` | `.codex/skills/japanese-tech-writing/SKILL.md` |
| `oracle-consult` | `~/.codex/skills/oracle-consult/SKILL.md` | `.codex/skills/oracle-consult/SKILL.md` |
| `plain-language-explainer` | `~/.codex/skills/plain-language-explainer/SKILL.md` | `.codex/skills/plain-language-explainer/SKILL.md` |
| `ponytail` | `~/.codex/skills/ponytail/SKILL.md` | `.codex/skills/ponytail/SKILL.md` |
| `ship-change` | `~/.codex/skills/ship-change/SKILL.md` | `.codex/skills/ship-change/SKILL.md` |
| `source-policy` | `~/.codex/skills/source-policy/SKILL.md` | `.codex/skills/source-policy/SKILL.md` |
| `thinking-partner` | `~/.codex/skills/thinking-partner/SKILL.md` | `.codex/skills/thinking-partner/SKILL.md` |
| `worker-review-before-commit` | `~/.codex/skills/worker-review-before-commit/SKILL.md` | `.codex/skills/worker-review-before-commit/SKILL.md` |
| `worker-review-before-deploy` | `~/.codex/skills/worker-review-before-deploy/SKILL.md` | `.codex/skills/worker-review-before-deploy/SKILL.md` |
| `worker-review-before-push` | `~/.codex/skills/worker-review-before-push/SKILL.md` | `.codex/skills/worker-review-before-push/SKILL.md` |
| `worker-review-deliverable` | `~/.codex/skills/worker-review-deliverable/SKILL.md` | `.codex/skills/worker-review-deliverable/SKILL.md` |
| `write-docs` | `~/.codex/skills/write-docs/SKILL.md` | `.codex/skills/write-docs/SKILL.md` |

## 外部原本から取り込んだSkill

| Skill | 外部原本 | 確認版 |
| --- | --- | --- |
| `cognitive-rhythm-writing` | `https://gist.github.com/k16shikano/eb2929f13ed19c97188393d297be8432` | `a3b1e26` |
| `japanese-tech-writing` | `https://gist.github.com/k16shikano/fd287c3133457c4fd8f5601d34aa817d` | `209db7d` |
| `ponytail` | `https://github.com/DietrichGebert/ponytail` | `4.8.4` / `16f2980`（Codex未対応の `argument-hint` のみ除外） |
